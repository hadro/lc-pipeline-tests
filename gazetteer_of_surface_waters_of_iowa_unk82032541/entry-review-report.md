### Data Quality Audit Report

**Overall Quality Assessment**
The extraction is highly successful at identifying discrete stream entries and capturing complex coordinate data (Township/Range/Section). However, there is significant structural overlap between the `flow_description` and `destination_details` fields, and the pipeline struggles with entries originating outside Iowa.

---

### 1. Redundant Narrative Overlap
In a majority of records, the `flow_description` field captures the entire narrative paragraph, including the destination and hierarchy. This information is then repeated (often in a truncated or identical form) in the `destination_details` field. This suggests the NER model is failing to identify the semantic boundary between the "flow" (direction/length) and the "destination" (the body of water entered).

*   **Examples:**
    *   `Bear Creek` (Buchanan): `flow_description` contains "flows southwest 17 miles into Cedar River (tributary through Iowa River to the Mississippi)..." which is then repeated in `destination_details`.
    *   `Bear Creek` (Dallas): `flow_description` includes the full destination and county coordinates.
    *   `Beaver Creek` (Guthrie): `flow_description` contains the parenthetical hierarchy belonging in `destination_details`.
*   **Affected Rows:** ~85% of entries.
*   **Cause/Fix:** The extraction prompt does not provide a clear delimiter for where "flow" ends and "destination" begins. **Fix:** Update the prompt to define `flow_description` as *only* the direction and distance, and `destination_details` as starting from the word "into."

### 2. Misplaced Geographic Metadata
Information regarding the physical location of a stream's mouth or specific length measurements is frequently shunted into the `additional_data` field instead of being integrated into the flow or destination columns.

*   **Examples:**
    *   `Buck Creek` (Wright): `additional_data` contains "1½ miles north of Webster City" (Destination detail).
    *   `Hewett Creek` (Clayton): `additional_data` contains "7 miles long" (Flow detail).
    *   `Rat Creek` (Osceola): `additional_data` contains "length, 4 miles" (Flow detail).
*   **Affected Rows:** ~10–15% of entries.
*   **Cause/Fix:** The model is using `additional_data` as a "catch-all" for any text it can't easily map to the primary schema. **Fix:** Explicitly instruct the model to include distance/length in `flow_description` even if it appears at the end of the entry.

### 3. Systematic Handling of Out-of-State Origins
When a stream originates in Minnesota (e.g., "Dodge County, Minn."), the pipeline inconsistently populates the `primary_county` and `origin` fields. In some cases, the out-of-state county is put in the `primary_county` field; in others, it is left null.

*   **Examples:**
    *   `Cedar River`: `primary_county` is "Dodge County, Minn." (Correct for origin, but inconsistent with the Iowa-centric schema).
    *   `Deer Creek` (Freeborn County): `primary_county` is null.
    *   `Iowa River, Upper`: `primary_county` is null.
*   **Affected Rows:** ~5% of entries.
*   **Cause/Fix:** The prompt assumes a "Primary County" is always an Iowa county listed after the name. **Fix:** Instruct the NER to capture the first-mentioned county as `primary_county` regardless of state, or create a separate `origin_state` field.

### 4. OCR Numerical and Character Artifacts
There are recurring instances where OCR has misread numbers or substituted Greek characters for Latin ones in coordinates.

*   **Examples:**
    *   `Ballard Creek` (Story): `flow_description` says "flows east 91 miles." Contextually, this is almost certainly "9 miles" (a common OCR error where a space or period is read as a '1').
    *   `Bear Creek` (Boone): `destination_details` contains "T. 83 Ν., R. 26 W." (The 'N' is the Greek character `U+039D`).
    *   `Black Creek` (Audubon): `flow_description` contains "15[?] miles" (OCR uncertainty marker left in data).
*   **Affected Rows:** ~3–5% of entries.
*   **Cause/Fix:** Standard OCR noise. **Fix:** Implement a post-processing regex to normalize "Ν" to "N" and flag suspiciously high mileage (e.g., any creek > 50 miles that isn't a major river).

### 5. Near-Duplicate Records
The CSV contains multiple entries for the same stream name within the same county, often representing different branches or segments that the NER has failed to distinguish or has extracted twice.

*   **Examples:**
    *   `Asher Creek` (Marshall County): Rows 8 and 9 are nearly identical.
    *   `Bear Creek` (Delaware County): Rows 32 and 33 represent different branches but use the same `stream_name`.
*   **Affected Rows:** ~5% of entries.
*   **Cause/Fix:** The source document sometimes lists segments or has a repetitive structure. **Fix:** Instruct the NER to append the branch name (e.g., "East Branch") to the `stream_name` if it is mentioned in the text but not bolded in the header.