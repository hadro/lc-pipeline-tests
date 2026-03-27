---
marp: true
---

# Directory Pipeline 
**(A personal digital collections project)**


<br>

2026-03-27
Data and Society Reading Group
Josh Hadro

---

## What the pipeline does

Takes any item with [a IIIF manifest](https://iiif.io/get-started/how-iiif-works/) and runs through a human-in-the-loop, Large Language Model (LLM)-augmented set of scripts to produce structured data with minimal customization required.

---

## Example outputs

- [Date Explorer for 19th Century brewery guides, extracted from a brewery guide](https://hadro.github.io/brewery-guides/explorer#about)
- [Example map listings from the 1947 Green Book](https://hadro.github.io/green-book-iiif-test/map)


---

## What are we talking about today
- At root, very targeted LLM-assisted tooling that may aid in the exploration of library materials
- Not just talking about LC stuff! Any [IIIF](https://iiif.io/get-started/how-iiif-works/)-compliant digital collection items
- Not a replacement for anything! An augmentation -- like OCR, and other access points, as means of increasing the surface area of discovery for potentially interested patrons and users. Likewise, helping interested researchers get to the most interesting parts of their inquiry more quickly
- Mainly for directories, gazetteers, and similarly structured items, where latent information is often implicit in the structure of the page and the listings
- Less useful for general tabular data, but it does work surprisingly well for structured hand-written documents

--- 


## Thesis

The tradeoffs have shifted.

For digtized collections:
- Data extraction with computers used to be impossible.
- Then it was doable, but hard and expensive, and not that useful. 
- Then it was doable, and relatively cheap, but useful only in narrow circumstances. 

Now, I think we're on the cusp of doable, cheap, and broadly useful. 

---

## How I think about this

The baseline I'm evaluating against isn't "is this perfect" (OCR is far from perfect!). It's "is this still useful, despite known flaws." For OCR, we answered that question affirmatively decades ago. I think the answer for some collections materials in the library sector, informed by these kinds of enrichments, is getting close to yes.

---
## Background


---


### Vonnegut's Barber

In Kurt Vonnegut's _Player Piano_ from 1952, there's a minor sub-plot about a barber who sees automation happening all around him, but somehow still not impacting his work as a barber. But he lies awake at night, every night, and spends so much time thinking about how automation could work in terms of barbering if only someone with the right domain knowledge worked on it, that he ends up doing the work to create the barbering robot that kept him up at night.

![bg right:25% contain](https://upload.wikimedia.org/wikipedia/en/6/6f/PlayerPianoFirstEd.jpg)

---
## Background inspiration

### Navigating the Green Book 

https://beefoo.github.io/greenbook-map/

---

![bg](https://drupal.nypl.org/sites-drupal/default/files/styles/max_width_960/public/blogs/navigating_the_green_books.png)

---




![bg](https://drupal.nypl.org/sites-drupal/default/files/styles/max_width_960/public/blogs/green_book_facsimile.png)




---

## The pipeline

---



### Basic pipeline
&nbsp;&nbsp;&nbsp;&nbsp;Download files 
→ Select samples and scope* 
→ Generate OCR + NER prompts 
→ Run LLM OCR 
→ Extract entry data

<br><br><sub>* denotes "human in the loop" step</sub>

---

### Enriched pipeline
&nbsp;&nbsp;&nbsp;&nbsp;Download files 
→ Select samples and scope* 
→ Generate OCR + NER prompts 
→ Run LLM OCR 
→ Run layout detection 
→ Match lines 
→ Review alignment*  
→ Extract entry data 
→ Align entries to bounding boxes 


<br><br><sub>* denotes "human in the loop" step</sub>

---

### Then the fun stuff
- Data explorer
- Map interface (with geocoding)
- Cross-volume comparison
- And more!


---

## How prompts figure into the Pipeline

Some LLM-generated prompt examples, based on 3-5 example pages each: 
- [OCR prompt](https://github.com/hadro/lc-pipeline-tests/blob/main/steiger_s_german_american_cookbook_steig_07033856/ocr_prompt.md) for [the Steiger’s German American cookbook](https://www.loc.gov/item/07033856/) 
- [Entity extraction prompt](https://github.com/hadro/lc-pipeline-tests/blob/main/directory_of_the_grand_and_subordinate_l_74175569/ner_prompt.md) for [Directory of the Grand and Subordinate Lodges, F.A.A.M. of the District of Columbia, 1903-1905](https://www.loc.gov/item/74175569/)



---

## Human-in-the-loop interface examples

---

![bg](https://github.com/hadro/directory-pipeline/raw/main/docs/screenshots/web-interface.png)


---

![bg](https://github.com/hadro/directory-pipeline/raw/main/docs/screenshots/select-pages.png)

---

![bg](https://github.com/hadro/directory-pipeline/raw/main/docs/screenshots/review-alignment.png)


---


![bg](https://github.com/hadro/directory-pipeline/raw/main/docs/screenshots/ner-in-cli.png)


---


## Pipeline actors

### What a **human** is doing in this pipeline

- Bringing curiosity to bear
- Exhibiting agency and responsibility
- Employing materials judgement and expertise
- Model selection
- Prompt review
- QA: OCR quality, alignment review, gut check etc.
- Synthesizing, understanding, and getting excited about possibilities

---
## Pipeline actors

### What an **LLM** is doing in this pipeline
- Meta-prompting for prompt-generation (OCR, NER)
- Item-specific OCR extraction
- Handwriting detection 
- Item-specific data extraction

---
## Pipeline actors

### What a **plain old computer** is doing in this pipeline
- File management
- Spread detection (.e.g., microfilm with 2-up pages)
- Column detection
- Layout analysis
- Bounding box identification
- Outlier detection
- LLM OCR to bounding box alignment
- IIIF annotation and content state generation

---

## Examples

### Woods Directory


[The Woods Directory](https://hadro.github.io/woods-directory/explorer#about) -- Black and minority business, professional and trades directory of New Orleans, Louisiana. 

1911, 1912, and 1913 editions digitized on [loc.gov](https://www.loc.gov/item/73644404/).




- [Pipeline Data Explorer](https://hadro.github.io/woods-directory/explorer#about)
- [Pipeline Map Explorer](https://hadro.github.io/woods-directory/map#about)

---

### Woods Directory

The physical resource was [written about by a geneologist in 2013. ](https://www.creolegen.org/2013/02/28/woods-directory-2/)

Like a lot of these kinds of reference resource posts, nearly all the comments on that post identify relatives named in the Directory, and want to see if there's a digital copy where they could see their relative's names in print. People are instead emailing each other cellphone photos taken of hard copies in private hands.

All the relative's names are searchable in the pipeline output, with link to the exact spots in the relevant pages!
E.g. https://hadro.github.io/woods-directory/explorer#q=Raoul



---
## More examples

https://hadro.github.io/lc-pipeline-tests/


---

## What vibe coding enabled, and what it didn't

Paul Ford, on [vibe coding](https://www.nytimes.com/2026/02/18/opinion/ai-software.html?unlocked_article_code=1.V1A.OOea.4QQ9xxw6vlue&smid=url-share): 

>  [Claude Code] was always a helpful coding assistant, but in November it suddenly got much better, and ever since I’ve been knocking off side projects that had sat in folders for a decade or longer.

For me, the recent spate of tools have meant sustained attention on personal projects that previously languished sometimes for a literal decade.

Put another way: I probably could have always done something sort of like this ... but I was never going to.


---

## Where should this pipeline go next?

### What kinds of things might this unlock? 


---

## Useful links: 

Code repository: https://github.com/hadro/directory-pipeline 
LC tests and examples: https://hadro.github.io/lc-pipeline-tests/

### Other examples: 

- 1911-1913 Woods' Business, Professional and Trades Directory of New Orleans, Louisiana
    - [Explorer](https://hadro.github.io/woods-directory/explorer#about)
    - [Map viewer](https://hadro.github.io/woods-directory/map#about)
- [1842 copyright ledger (printed text & handwritten text)](https://hadro.github.io/copyright-ledger-test/explorer)
- [19th Century brewery guides, extracted from a brewery guide](https://hadro.github.io/brewery-guides/explorer#about)
- [Example map listings from the 1947 Green Book](https://hadro.github.io/green-book-iiif-test/map)


---
N.B. Gemini and similar API models are currently the best mix of quality and value for all this stuff, but nearly all of these things could be done locally with tools like Surya/Chandra and models like Qwen 3.5, removing the paid API aspect of this.


---

## Readings

These substantially inspired my interest in this project: 
- [Multimodal LLMs for OCR, OCR Post-Correction, and Named Entity Recognition in Historical Documents](https://arxiv.org/abs/2504.00414)
- [Gemini 3 Solves Handwriting Recognition and it’s a Bitter Lesson](https://generativehistory.substack.com/p/gemini-3-solves-handwriting-recognition)
- [The A.I. Disruption We’ve Been Waiting for Has Arrived](https://www.nytimes.com/2026/02/18/opinion/ai-software.html?unlocked_article_code=1.V1A.OOea.4QQ9xxw6vlue&smid=url-share)

---

## AI Disclosure

Claude Code wrote: 
- Most of the code for this project
- Most of the code and git repo documentation for this project 

I wrote:
- Some small amount of the code for this project
- Amendments and edits of the documentation 

I also wrote all of the material for this presentation -- no AI was used in these slides (for better or for worse)