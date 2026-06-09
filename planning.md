# Project 1 Planning: The Unofficial Guide

> Write this document before you write any pipeline code.
> Your spec and architecture diagram are what you'll use to direct AI tools (Claude, Copilot, etc.) to generate your implementation — the more specific they are, the more useful the generated code will be.
> Update the Retrieval Approach and Chunking Strategy sections if you change your approach during implementation.
> Update this file before starting any stretch features.

---

## Domain

<!-- What domain did you choose? Why is this knowledge valuable and hard to find through official channels? -->

-CS Course and Professor Reviews at Minnesota State University, Mankato

## Documents

<!-- List your specific sources: URLs, subreddit names, forum threads, or file descriptions.
     Aim for at least 10 sources that together cover different subtopics or perspectives within your domain. -->

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

<!-- How will you split documents into chunks?
     State your chunk size (in tokens or characters), overlap size, and explain why those
     numbers fit the structure of your documents.
     A review-heavy corpus warrants different chunking than a long FAQ. -->

**Chunk size:** Approximately 100–300 words

**Overlap:** 0-20 words

**Reasoning:**

-Because the knowledge base contains short, opinion-based reviews from Rate My Professors and Reddit, smaller chunk sizes are preferred. Using one review or comment per chunk preserves individual opinions and prevents unrelated experiences from being merged together. This improves retrieval precision by ensuring that retrieved chunks directly answer questions about specific professors, courses, workloads, grading styles, and student experiences.

## Retrieval Approach

<!-- Which embedding model are you using (e.g., all-MiniLM-L6-v2 via sentence-transformers)?
     How many chunks will you retrieve per query (top-k)?
     If you were deploying this for real users and cost wasn't a constraint, what tradeoffs
     would you weigh in choosing a different embedding model — context length, multilingual
     support, accuracy on domain-specific text, latency? -->

**Embedding model:**

all-MiniLM-L6-v2 (via sentence-transformers)

This model is lightweight, fast, and performs well on short text such as professor reviews and Reddit comments. Since the knowledge base consists primarily of brief, opinion-based documents, all-MiniLM-L6-v2 provides a good balance between retrieval quality and computational efficiency.


**Top-k:**
5

The retrieval system will return the 5 most relevant chunks for each query. This provides enough evidence from multiple student reviews while avoiding excessive noise from less relevant comments.


**Production tradeoff reflection:**

-If cost were not a constraint, I would evaluate larger embedding models such as BGE-large or OpenAI's text-embedding-3-large. Larger models often capture semantic meaning more accurately and may improve retrieval for nuanced questions about teaching style, course difficulty, or workload. However, they require more storage, increase latency, and are more expensive to run. For this project, all-MiniLM-L6-v2 offers an effective balance between speed, resource usage, and retrieval accuracy for short student reviews and discussion posts.

## Evaluation Plan

<!-- List your 5 test questions with their expected correct answers.
     Questions should be specific enough that you can judge whether the system's response
     is right or wrong. "What are good dining halls?" is too vague.
     "What do students say about wait times at [dining hall name] during lunch?" is testable. -->

## Evaluation Plan

| # | Question | Expected answer |
|---|----------|-----------------|
| 1 | What courses does the Computer Science B.S. program require students to complete? | The response should list core CS requirements such as programming, data structures, algorithms, computer architecture, software engineering, and required mathematics courses according to the MNSU catalog. |
| 2 | What do students say about Rushit Dave's teaching style? | Students generally describe him as knowledgeable, helpful, and clear in explanations, with a focus on practical programming examples. |
| 3 | What do students report about Jonathan Hardwick's courses? | Reviews commonly mention challenging coursework, high expectations, and strong subject knowledge. |
| 4 | Which sources discuss student experiences with CS 201? | The system should identify relevant Reddit discussions, professor reviews, and course-related comments that mention CS 201. |
| 5 | Where can students find official information about Computer Science faculty at MNSU? | The system should reference the Computer Science Faculty & Staff page and CIS Faculty & Staff directory on the MNSU website. |

---

## Anticipated Challenges

<!-- What could go wrong? Name at least two specific risks with reasoning.
     Consider: noisy or inconsistent documents, missing source attribution, off-topic
     retrieval, chunks that split key information across boundaries. -->

1. **Noisy or inconsistent reviews**
   Student reviews from Rate My Professors and Reddit are subjective and may contain conflicting opinions about the same professor or course. The system may retrieve a small number of reviews that do not accurately represent the overall student experience.

2. **Off-topic retrieval**
   A query about a specific Computer Science professor may retrieve unrelated Reddit discussions or reviews about other courses because of similar keywords. This could cause the system to generate inaccurate or irrelevant answers.

---

## Architecture

<!-- Draw a diagram of your pipeline showing the five stages:
     Document Ingestion → Chunking → Embedding + Vector Store → Retrieval → Generation
     Label each stage with the tool or library you're using.
     You can use ASCII art, a Mermaid diagram, or embed a sketch as an image.
     You'll use this diagram as context when prompting AI tools to implement each stage. -->

---

## Architecture

```text
┌─────────────────────┐
│ Document Ingestion  │
│ Sources:            │
│ - Rate My Professors│
│ - Reddit r/MNSU     │
│ - MNSU Faculty Pages│
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│      Chunking       │
│ Chunk Size:         │
│ 100-300 words       │
│ One review/comment  │
│ per chunk           │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Embedding + Store   │
│ Model:              │
│ all-MiniLM-L6-v2    │
│ Vector DB: ChromaDB │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│      Retrieval      │
│ Similarity Search   │
│ Top-k = 5           │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│     Generation      │
│ LLM: Groq + Llama   │
│ Answer + Sources    │
└─────────────────────┘
```

## AI Tool Plan

<!-- For each part of the pipeline below, describe:
     - Which AI tool you plan to use (Claude, Copilot, ChatGPT, etc.)
     - What you'll give it as input (which sections of this planning.md, which requirements)
     - What you expect it to produce
     - How you'll verify the output matches your spec

     "I'll use AI to help me code" is not a plan.
     "I'll give Claude my Chunking Strategy section and ask it to implement chunk_text()
     with my specified chunk size and overlap" is a plan. -->


I plan to use AI tools such as ChatGPT and Claude to help implement specific parts of the pipeline, but I will use my `planning.md` as the main guide so the generated code matches my project design.

| Pipeline part | AI tool use plan |
|---|---|
| Document Ingestion | I will prompt ChatGPT or Claude with my **Source Documents** table and the assignment requirement for document ingestion. I will ask it to help create a script that loads URLs or local text files, stores the source name, URL, and document text, and preserves metadata for citation later. |
| Chunking | I will prompt Claude with my **Chunking Strategy** and **Risks and Failure Modes** sections. I will ask it to implement a `chunk_text()` function that uses small chunks, about 100–300 words, so short professor reviews and Reddit comments are not mixed together unnecessarily. |
| Embedding + Vector Store | I will give the AI my **Retrieval Approach** section, including the embedding model `all-MiniLM-L6-v2` and vector store `ChromaDB`. I will ask it to help write code that embeds each chunk, saves it in ChromaDB, and includes metadata such as professor name, course, source, and URL. |
| Retrieval | I will prompt ChatGPT or Copilot with my **Top-k** choice and **Evaluation Plan**. I will ask it to implement retrieval using `top-k = 5`, returning the most relevant chunks along with their source information. |
| Generation | I will give the AI my **Example Questions**, **Expected Answers**, and requirement for source-grounded answers. I will ask it to write a prompt template that tells the LLM to answer only from retrieved context and include source citations when possible. |
| Testing and Evaluation | I will prompt the AI with my **Evaluation Plan** table and ask it to create simple tests that run the five test questions, compare the system output to the expected answer, and check whether sources are included. |

**Milestone 3 — Ingestion and chunking:**

**Milestone 4 — Embedding and retrieval:**

**Milestone 5 — Generation and interface:**
