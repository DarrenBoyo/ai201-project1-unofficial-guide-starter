# The Unofficial Guide — Project 1

> **How to use this template:**
> Complete each section *after* you've built and tested the corresponding part of your system.
> Do not write placeholder text — if a section isn't done yet, leave it blank and come back.
> Every section below is required for submission. One-liners will not receive full credit.

---

## Domain

<!-- What topic or category of knowledge does your system cover?
     Why is this knowledge valuable, and why is it hard to find through official channels?
     Example: "Student reviews of CS professors at [university] — useful because official
     course descriptions don't reflect teaching style, exam difficulty, or workload." -->

---
This system covers student opinions and experiences related to Computer Science courses and professors at Minnesota State University, Mankato. The domain includes teaching style, workload, exam difficulty, grading practices, course expectations, and faculty information.

This knowledge is valuable because official course descriptions and faculty profiles do not capture the student experience. Students often want information about how professors teach, how difficult assignments are, and what strategies help students succeed. Much of this information is scattered across review sites, Reddit discussions, and informal online conversations rather than official university resources.

## Document Sources

<!-- List every source you collected documents from.
     Be specific: include URLs, subreddit names, forum thread titles, or file names.
     Aim for variety — sources that together cover different subtopics or perspectives. -->

| # | Source | Description | URL or location |
|---|--------|-------------|-----------------|
| 1 | Rate My Professors – Minnesota State University, Mankato|Student ratings and reviews of professors. |https://www.ratemyprofessors.com/school/559?utm_source=chatgpt.com |
| 2 | MNSU CIS Faculty & Staff | Official directory of Computer Information Science faculty and staff. | https://cset.mnsu.edu/departments/computer-information-science/faculty-and-staff/?utm_source=chatgpt.com|
| 3 | Reddit r/MNSU | Student discussions about classes, professors, and campus experiences. | https://www.reddit.com/r/mnsu/ |
| 4 | MNSU CIS faculty page | MNSU CIS Faculty page| https://cset.mnsu.edu/departments/computer-information-science/faculty-and-staff/ |
| 5 | Rate My Professors | Jonathan HardWick Review Page | https://www.ratemyprofessors.com/professor/1940059 |
| 6 | Rate My Professors | Rushit Dave Review Page | https://www.ratemyprofessors.com/professor/2832629 |
| 7 | Rate My Professors | Rebecca Bates Review Page | https://www.ratemyprofessors.com/professor/625846 |
| 8 | Rate My Professors | Mansi Bhavsar Review Page | https://www.ratemyprofessors.com/professor/3108459 |
| 9 | Rate My Professors | David Clisbee Review Page | https://www.ratemyprofessors.com/professor/3030073 |
| 10 |Rate My Professors | Sarah Kruse Review Page | https://www.ratemyprofessors.com/professor/206994 |
---

## Chunking Strategy

<!-- Describe your chunking approach with enough specificity that someone else could reproduce it.
     Include:
     - Chunk size (characters or tokens) and why that size fits your documents
     - Overlap size and why (or why not) you used overlap
     - Any preprocessing you did before chunking (e.g., stripping HTML, removing headers)
     - What your final chunk count was across all documents -->

**Chunk size:** Approximately 300 words maximum per chunk.

**Overlap:** 15-word overlap for chunks that exceed the maximum size.

**Why these choices fit your documents:**

Most of the documents consist of short professor reviews, Reddit comments, and faculty descriptions. Keeping reviews as individual chunks preserves complete opinions and allows retrieval to return specific feedback from students. Reddit threads are chunked by comment boundaries whenever possible so that individual viewpoints remain intact. Faculty pages are chunked by faculty member section rather than arbitrary paragraph boundaries. HTML tags, navigation text, repeated headers, and boilerplate content are removed before chunking.


**Final chunk count:** 53 Chunks

---

## Embedding Model

<!-- Name the embedding model you used and explain your choice.
     Then answer: if you were deploying this system for real users and cost wasn't a constraint,
     what tradeoffs would you weigh in choosing a different model?
     Consider: context length limits, multilingual support, accuracy on domain-specific text,
     latency, and local vs. API-hosted. -->

**Model used:** all-MiniLM-L6-v2 from Sentence Transformers.

**Production tradeoff reflection:**
I chose all-MiniLM-L6-v2 because it runs locally, requires no API key, and provides strong semantic search performance for short text such as reviews and comments. If cost were not a concern, I would evaluate larger embedding models that provide better semantic understanding and improved retrieval quality. I would also consider multilingual support, context length, latency, hosting requirements, and performance on opinion-based educational content before selecting a production model.
---

## Grounded Generation

<!-- Explain how your system enforces grounding — how does it prevent the LLM from answering
     beyond the retrieved documents?
     Describe both your system prompt (what instruction you gave the model) and any structural
     choices (e.g., how you formatted the context, whether you filtered low-relevance chunks).
     Do not just say "I told it to use the documents" — show the actual instruction or explain
     the mechanism. -->

**System prompt grounding instruction:**

The prompt instructs the model to answer only using the retrieved context and explicitly states:

"Answer ONLY using the retrieved context. Do NOT use outside knowledge. If the answer is not contained in the context, say: 'I don't have enough information in the documents.'"

**How source attribution is surfaced in the response:**

After retrieval, source names are collected programmatically from the retrieved chunks and appended to the final answer. This guarantees that every answer includes the sources used rather than relying on the language model to generate citations correctly.

---

## Evaluation Report

<!-- Run your 5 test questions from planning.md through your system and record the results.
     Be honest — a partially accurate or inaccurate result that you explain well is more
     valuable than a suspiciously perfect result. -->

| # | Question | Expected answer | System response (summarized) | Retrieval quality | Response accuracy |
|---|----------|-----------------|------------------------------|-------------------|-------------------|
| 1 | What resources help students succeed in MNSU CS courses? | Not Covered | I don't have enough information in the documents.
| Relevant | Accurate |
| 2 | What do students say about Rushit Dave's teaching style? | Clear explanations and organized instruction | Engaging, making students feel comfortable to ask questions (SOURCE 1) | Relevant | Accurate |
| 3 | What do students report about Jonathan Hardwick's courses? | Information about workload and course difficulty | Students report that Jonathan Hardwick's courses are "Lecture heavy" and "Test heavy" with "a lot of reading involved | Relevant | Accurate |
| 4 | What resources help students succeed in MNSU CS courses? | Not Covered | I don't have enough information in the documents. | Off-Target | Partially Accurate|
| 5 | Where can students find information about CIS faculty? | Faculty directory page | I don't have enough information in the documents. | Off Target | Partially Accurate|

**Retrieval quality:** Relevant / Partially relevant / Off-target  
**Response accuracy:** Accurate / Partially accurate / Inaccurate

---

## Failure Case Analysis

<!-- Identify at least one question where retrieval or generation did not work as expected.
     Write a specific explanation of *why* it failed, tied to a part of the pipeline.

     "The answer was wrong" is not an explanation.

     "The relevant information was split across a chunk boundary, so retrieval returned
     only half the context — the model didn't have enough to answer correctly" is an explanation.

     "The embedding model treated the professor's nickname as out-of-vocabulary and returned
     results from an unrelated review" is an explanation. -->

**Question that failed:**
What resources help students succeed in MNSU CS courses?

**What the system returned:**
The system returned a mixture of Reddit advice and professor review content. Some retrieved chunks were only loosely related to study resources.

**Root cause (tied to a specific pipeline stage):**
The retrieval stage returned chunks that matched "success" semantically but were focused on professors rather than study resources. The embedding model associated related educational concepts and ranked them highly.

**What you would change to fix it:**
I would add more Reddit and discussion-based documents focused specifically on student success strategies. I would also experiment with metadata filtering and retrieval reranking to improve relevance.

---

## Spec Reflection

<!-- Reflect on how planning.md shaped your implementation.
     Answer both questions with at least 2–3 sentences each. -->

**One way the spec helped you during implementation:**
The planning document forced me to think carefully about chunking before writing code. Deciding how to handle professor reviews, Reddit comments, and faculty pages in advance made implementation easier and reduced trial-and-error during development.

**One way your implementation diverged from the spec, and why:**
My original plan assumed all sources would be available through URLs, but I ultimately converted much of the content into local text files for easier ingestion and consistent formatting. This simplified cleaning and chunking while still preserving the underlying information.

---

## AI Usage

<!-- Describe at least 2 specific instances where you used an AI tool during this project.
     For each: what did you give the AI as input, what did it produce, and what did you
     change, override, or direct differently?

     "I used Claude to help me code" is not sufficient.
     "I gave Claude my Chunking Strategy section from planning.md and asked it to implement
     chunk_text(). It returned a function using a fixed character split. I overrode the
     chunk size from 500 to 200 because my documents are short reviews, not long guides." -->

**Instance 1**

- *What I gave the AI:* My chunking strategy, document list, and project requirements.
- *What it produced:* An ingestion and chunking pipeline that cleaned documents and generated chunks.
- *What I changed or overrode:* I modified the chunking logic so faculty pages were chunked by faculty member sections, Reddit threads preserved comment boundaries, and professor reviews remained one review per chunk whenever possible.

**Instance 2**

- *What I gave the AI:* My retrieval architecture, embedding model choice, and ChromaDB requirements.
- *What it produced:* Embedding and retrieval code using all-MiniLM-L6-v2 and ChromaDB.
- *What I changed or overrode:* I added metadata storage, source attribution, duplicate-chunk detection, and improved course code extraction to better support retrieval and grounding.
