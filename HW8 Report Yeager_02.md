# Report transcription

Text extracted from the author-provided course report. Figures are not reproduced and tables may lose formatting. Student identifiers and institutional email addresses are removed.

## Page 1

CIS 5300: Natural Language Processing
Homework 8: Information Retrieval & RAG
Report Template
Name: Yeager Ye
Penn ID: [STUDENT_ID_REDACTED]
Learning Objectives
1. Implement TF-IDF and BM25 from scratch and understand how term weighting works;
2. Use dense retrieval with bi-encoder models and compare to sparse methods;
3. Build a RAG pipeline end-to-end: chunk, index, retrieve, and generate;
4. Evaluate retrieval using precision@k and recall@k;
5. Analyze RAG tradeoffs including chunking strategies and when RAG helps vs. hurts.
Points Breakdown
Section Points Description
Answer 2.2 10 pts Sparse vs. Dense Retrieval Comparison
Answer 4.1 5 pts Retrieval Quality Analysis
Answer 4.2 5 pts Chunking Strategy Comparison
Answer 4.3 5 pts Generation Faithfulness
Answer 4.4 3 pts Systematic Chunking Comparison
Answer 5.1 4 pts When Does RAG Help vs. Hurt
Answer 5.2 4 pts Sparse vs. Dense Retrieval Tradeoffs
Answer 5.3 4 pts Hallucination in RAG Systems
Total 40 pts
Answer 2.2: Sparse vs. Dense Retrieval Comparison
Performance Table
Method P@5 R@5
TF-IDF 0.2667 0.5833
BM25 0.3000 0.6528
Dense 0.3167 0.6528
Reference specific queries and results from your experiments.
Analysis:
1. Which method performs best overall? Is the difference large or small?
Dense retrieval performs best overall with P@5=0.3167 and R@5=0.6528, followed by BM25 (P@5=0.3000, R@5=0.6528) and TF-IDF (P@5=0.2667, R@5=0.5833). The difference between Dense and BM25 is small (1.7 percentage points in P@5, identical R@5), while both substantially
outperform TF-IDF in recall (0.6528 vs 0.5833). BM25's improvement over TF-IDF comes from its term frequency saturation and document length normalization, which prevent long documents from dominating scores. Dense retrieval's edge comes from its ability to capture semantic
similarity beyond exact word overlap. For example, on query 1 ("What is BERT and how is it trained?"), Dense retrieves both relevant docs (1, 21) in the top 2, while BM25 retrieves doc 1 but misses doc 21 ("Transformer Architecture") entirely, and TF-IDF also misses doc 21. On query 4
("How do dense retrieval systems work?"), Dense retrieves the top relevant doc (12) at rank 1, while both sparse methods fail to retrieve doc 12 in the top 5.
2. Vocabulary mismatch query analysis (query 11 — "How do automobile repair guides help car owners?") Report which method(s) actually retrieve doc 34 in their top-k, and explain the result based on the words the query and doc 34 do (or do not) share. Then,
in general terms, explain why sparse methods (TF-IDF, BM25) can struggle on vocabulary mismatch and how dense retrieval is designed to help. (See Lecture M10.2.1 and textbook §11.3 on the vocabulary mismatch problem. Note: dense retrieval does not
always win in practice — report what you actually observe rather than what you expect.)
For query 11 ("How do automobile repair guides help car owners?"), the only relevant document is doc 34 ("Vocabulary Mismatch Problem"). Both TF-IDF and BM25 successfully retrieve doc 34 at rank 1 (TF-IDF score: 0.2409, BM25 score: 1.5807). This is because doc 34 explicitly contains
the words "automobile," "repair," and "car" - it uses the query's exact terms to illustrate the vocabulary mismatch concept. Surprisingly, Dense retrieval FAILS on this query, retrieving docs [22, 14, 3, 18, 36] instead of doc 34. This is counterintuitive because dense retrieval is designed to
bridge vocabulary gaps. However, doc 34's content is about the vocabulary mismatch problem itself (a meta-level discussion), while the query asks about automobile repair guides in a practical sense. The dense encoder maps the query to a region near documents about semantic search
and question answering, rather than to the meta-discussion in doc 34. This demonstrates that dense retrieval does not always win - when the relevant document happens to share exact lexical terms with the query (as doc 34 does here), sparse methods can outperform. In general, sparse
methods struggle when queries and documents use different surface forms for the same concept (e.g., "car" vs "vehicle"), while dense retrieval is designed to bridge such gaps through learned semantic embeddings.
3. Can you find a query where sparse retrieval outperforms dense retrieval? Why might this happen?
Query 11 is a clear case where sparse retrieval outperforms dense retrieval. Both TF-IDF and BM25 retrieve the sole relevant document (doc 34) at rank 1, while Dense retrieval misses it entirely. This happens because doc 34 shares exact lexical terms ("automobile," "repair," "car") with the
query, giving sparse methods a strong signal. Dense retrieval, designed for semantic matching, maps the query to a different semantic region. Additionally, on query 5 ("What is the vocabulary mismatch problem?"), all three methods retrieve doc 34, but TF-IDF and BM25 rank it at position 1
with higher confidence, while Dense also retrieves it at rank 1. On query 6 ("How does BM25 scoring work?"), BM25 retrieves doc 13 at rank 1 (the BM25 Algorithm document), while Dense also retrieves it at rank 1 but with lower separation from non-relevant docs. Sparse methods excel
when queries contain specific technical terms that appear verbatim in relevant documents, as the exact-match signal is very strong.
Answer 4.1: Retrieval Quality Analysis
For each RAG question, examine the retrieved chunks.. For each, cite the specific retrieved chunks (by title / passage number) and evaluate:
- Were the retrieved chunks relevant?
- Do they contain the information needed to answer the question?
- Were any irrelevant passages or passages that failed to support an answer included?
Question 1: What is retrieval-augmented generation and why does it help with hallucination?
Retrieved chunks: [1] "Hallucination in Language Models" (score: 0.6206), [2] "Retrieval-Augmented Generation" (score: 0.5876), [3] "Retrieval-Augmented Generation" (score: 0.5605). All three chunks are highly relevant. Chunk 1 explains that hallucination occurs when LLMs generate
fluent but factually incorrect text, and states that RAG helps by grounding outputs in retrieved documents. Chunks 2 and 3 define RAG as combining information retrieval with language model generation, and explain it reduces hallucination by grounding responses in retrieved evidence. No
irrelevant passages were included - all three directly address the question.
Question 2: How does BM25 differ from basic TF-IDF?

## Page 2

CIS 5300: Natural Language Processing
Retrieved chunks: [1] "BM25 Algorithm" (score: 0.7892), [2] "BM25 Algorithm" (score: 0.7356), [3] "TF-IDF Weighting" (score: 0.6101). All chunks are relevant. Chunks 1 and 2 explain that BM25 extends TF-IDF by adding term frequency saturation and document length normalization, with
parameters k1=1.5 and b=0.75. Chunk 3 provides background on TF-IDF weighting, explaining that IDF measures how discriminative a word is across the collection. The chunks contain all information needed to answer the question. No irrelevant passages were included.
Question 3: What are the advantages of bi-encoder architecture over cross-encoders for retrieval?
Retrieved chunks: [1] "Bi-Encoder Architecture" (score: 0.7495), [2] "Bi-Encoder Architecture" (score: 0.7417), [3] "Cross-Encoder Reranking" (score: 0.6802). All chunks are highly relevant. Chunks 1 and 2 explain that bi-encoders use two separate encoders to independently encode queries
and documents into dense vectors, storing document vectors at index time and computing similarity via dot product at query time. This is far more efficient than cross-encoders, which must jointly encode each query-document pair. Chunk 3 explains that cross-encoders produce more accurate
scores but are too expensive for full-collection search, and are used for reranking. The chunks fully answer the question. No irrelevant passages were included.
Question 4: What chunking strategies can be used in RAG systems and what are their tradeoffs?
Retrieved chunks: [1] "Chunking Strategies for RAG" (score: 0.7684), [2] "Chunking Strategies for RAG" (score: 0.5803), [3] "Vector Databases" (score: 0.4194). Chunks 1 and 2 are highly relevant, describing fixed-size chunking, sentence-based chunking, semantic chunking, and overlapping
chunks. They explain the key tradeoff: smaller chunks are more precise but lose context, while larger chunks preserve context but may include irrelevant information. Chunk 3 is marginally relevant - it mentions RAG systems at scale but does not directly address chunking strategies. This is a
minor case of an irrelevant passage being included, though it does not detract from the answer.
Question 5: What is the vocabulary mismatch problem and how does dense retrieval address it?
Retrieved chunks: [1] "Vocabulary Mismatch Problem" (score: 0.7734), [2] "Vocabulary Mismatch Problem" (score: 0.6368), [3] "Sparse vs Dense Retrieval" (score: 0.5516). All chunks are highly relevant. Chunks 1 and 2 define the vocabulary mismatch problem as occurring when queries and
documents use different words for the same concept (e.g., "automobile repair" vs "car maintenance"), and explain that sparse methods like TF-IDF and BM25 cannot bridge this gap because they rely on exact word overlap. Dense retrieval addresses this by mapping semantically similar texts
to nearby vectors using learned embeddings. Chunk 3 provides additional context comparing sparse and dense retrieval methods. All chunks directly support the answer. No irrelevant passages were included.
Answer 4.2: Chunking Strategy Comparison
Compare at least two chunk sizes. Paste your printed comparison and analyze the tradeoff.
Configuration Question Relevant Chunks Retrieved? (Yes/No/Partial) Notes
chunk_size=20, Q1: RAG & hallucination: doc 11 (Retrieval- Yes - relevant docs retrieved for all 5 questions 162 chunks total. Smaller chunks are more focused and
Augmented), doc 16 (Hallucination in Lang.), doc 11 precise, but may split answers across chunk
overlap=5
(Retrieval-Augmented) boundaries. Higher retrieval scores for specific
Q2: BM25 vs TF-IDF: doc 13 (BM25 Algorithm), doc 13 passages.
(BM25 Algorithm), doc 13 (BM25 Algorithm)
Q3: Bi-encoder vs cross-encoder: doc 29 (Bi-Encoder
Arch.), doc 29 (Bi-Encoder Arch.), doc 29 (Bi-Encoder
Arch.)
Q4: Chunking strategies: doc 19 (Chunking Strategies),
doc 19 (Chunking Strategies), doc 19 (Chunking
Strategies)
Q5: Vocabulary mismatch: doc 34 (Vocabulary
Mismatch), doc 34 (Vocabulary Mismatch), doc 34
(Vocabulary Mismatch)
chunk_size=50, Q1: RAG & hallucination: doc 16 (Hallucination in Yes - relevant docs retrieved for all 5 questions 76 chunks total. Larger chunks preserve more context
Lang.), doc 11 (Retrieval-Augmented), doc 11 and avoid splitting answers, but may include irrelevant
overlap=10
(Retrieval-Augmented) information. Fewer chunks means faster indexing.
Q2: BM25 vs TF-IDF: doc 13 (BM25 Algorithm), doc 13
(BM25 Algorithm), doc 23 (TF-IDF Weighting)
Q3: Bi-encoder vs cross-encoder: doc 29 (Bi-Encoder
Arch.), doc 29 (Bi-Encoder Arch.), doc 30 (Cross-
Encoder Rerank)
Q4: Chunking strategies: doc 19 (Chunking Strategies),
doc 19 (Chunking Strategies), doc 17 (Vector
Databases)
Q5: Vocabulary mismatch: doc 34 (Vocabulary
Mismatch), doc 34 (Vocabulary Mismatch), doc 39
(Sparse vs Dense Retr.)
Which chunk size retrieves chunks that actually contain the answer, and the precision-vs-context tradeoff. Give specific examples (small chunks = more focused but may split the answer; large chunks = more context but add noise).
Comparing chunk_size=20 (162 chunks) vs chunk_size=50 (76 chunks): Both strategies successfully retrieve relevant documents for all 5 RAG questions, but with different tradeoffs. With chunk_size=20, the smaller chunks are more focused - for Q2 (BM25 vs TF-IDF), all 3 retrieved chunks
come from doc 13 (BM25 Algorithm), each containing a specific aspect of the answer. The top score is 0.7804, slightly lower than chunk_size=50's 0.7892. With chunk_size=50, chunks contain more context - for Q1 (RAG & hallucination), the top chunk from doc 16 (Hallucination) includes
the full definition and the RAG connection in one passage. However, chunk_size=50 sometimes introduces noise: for Q4 (chunking strategies), the third retrieved chunk comes from doc 17 (Vector Databases), which is only marginally relevant. The precision-vs-context tradeoff is clear:
smaller chunks (size=20) provide more precise retrieval but risk splitting answers across boundaries, requiring multiple chunks to form a complete answer. Larger chunks (size=50) preserve context and often contain the complete answer in a single passage, but add potential noise from
adjacent irrelevant text. For this corpus, where documents are short (2-3 sentences each), chunk_size=50 with overlap=10 works well because it captures most documents in a single chunk while the overlap prevents losing context at boundaries.
Answer 4.3: Generation Faithfulness
For each of the 5 RAG answers, evaluate faithfulness. Cite the specific passage(s) supporting each claim. If an answer has no unsupported claims, state "no observed hallucinations."
Question Key claims Supported by passages Unsupported / Faithfulness
(list claims) (list supported claims + passage numbers) hallucinated score
(list any, or "none observed") (fraction)
Answer 1 What is retrieval-augmented 1. RAG combines information retrieval with language model Claim 1 — Passage 2 none observed 2/2
generation and why does it help generation. Claim 2 — Passages 1, 2
with hallucination? 2. RAG reduces hallucination by grounding outputs in retrieved
evidence.
Answer 2 How does BM25 differ from basic 1. BM25 extends TF-IDF by adding term frequency saturation Claim 1 — Passage 1 none observed 3/3
TF-IDF? and document length normalization. Claim 2 — Passage 1
2. Parameters k1 and b control term-frequency scaling and Claim 3 — Passages 1, 2
length normalization.
3. With k1=1.5 and b=0.75, BM25 consistently outperforms basic
TF-IDF.
Answer 3 What are the advantages of bi- 1. Bi-encoders encode queries and documents independently, Claim 1 — Passages 1, 2 none observed 4/4
encoder architecture over cross- making them far more efficient than cross-encoders. Claim 2 — Passage 1
encoders for retrieval? 2. At index time documents are pre-encoded; at query time only Claim 3 — Passage 3
the query is encoded and similarity is computed via dot product. Claim 4 — Passage 2
3. Cross-encoders are too expensive for full collection search.
4. Bi-encoders trade some accuracy for dramatic efficiency gains.
Answer 4 What chunking strategies can be 1. Strategies include fixed-size, sentence-based, semantic, and Claim 1 — Passage 2 none observed 2/2
used in RAG systems and what are overlapping chunking. Claim 2 — Passages 1, 2
their tradeoffs? 2. Smaller chunks are more precise but lose context; larger
chunks preserve context but may include irrelevant information.
Answer 5 What is the vocabulary mismatch 1. Vocabulary mismatch occurs when queries and documents use Claim 1 — Passages 1, 2 none observed 3/3
problem and how does dense different words for the same concept (e.g., automobile repair vs. Claim 2 — Passages 1, 2
retrieval address it? car maintenance). Claim 3 — Passages 2, 3
2. Sparse retrieval methods rely on exact word overlap and
cannot bridge this gap.
3. Dense retrieval maps semantically similar texts to nearby
vectors.
Overall Faithfulness Summary: All 5 RAG-generated answers are faithful to their retrieved passages. Claude (claude-sonnet-4-5) at temperature=0.0 consistently cited correct passage numbers and grounded every claim in the provided context. No hallucinated facts, fabricated sources, or unsupported extrapolations were
observed. The structured prompt ("Answer based ONLY on the provided passages. Cite which passage(s) support your answer...") proved effective at constraining the model to retrieved evidence only. This demonstrates that with proper retrieval and prompt design, RAG systems can produce trustworthy, grounded responses.
Answer 4.4: Systematic Chunking Comparison
Compare at least three chunking strategies (fixed-size 20, fixed-size 40, paragraph-based) for each query whether the top-3 retrieved chunks contain the information needed to answer the question.
Strategy Question Top-3 Chunks Contain Answer?
(Yes/No/Partial)
Fixed-size 20 words Q1: What is retrieval-augmented generation and why does it help with hallucination? Q1: Yes
Q2: How does BM25 differ from basic TF-IDF? Q2: Yes
Q3: What are the advantages of bi-encoder architecture over cross-encoders for retrieval? Q3: Yes
Q4: What chunking strategies can be used in RAG systems and what are their tradeoffs? Q4: Yes
Q5: What is the vocabulary mismatch problem and how does dense retrieval address it? Q5: Yes
Fixed-size 40 words Q1: What is retrieval-augmented generation and why does it help with hallucination? Q1: Yes
Q2: How does BM25 differ from basic TF-IDF? Q2: Yes
Q3: What are the advantages of bi-encoder architecture over cross-encoders for retrieval? Q3: Yes
Q4: What chunking strategies can be used in RAG systems and what are their tradeoffs? Q4: Yes
Q5: What is the vocabulary mismatch problem and how does dense retrieval address it? Q5: Yes
Paragraph-based Q1: What is retrieval-augmented generation and why does it help with hallucination? Q1: Yes
Q2: How does BM25 differ from basic TF-IDF? Q2: Yes
Q3: What are the advantages of bi-encoder architecture over cross-encoders for retrieval? Q3: Yes
Q4: What chunking strategies can be used in RAG systems and what are their tradeoffs? Q4: Partial
Q5: What is the vocabulary mismatch problem and how does dense retrieval address it? Q5: Yes
Precision vs. context tradeoff: The precision-context tradeoff discussed in Lecture M10.4.3 is clearly visible. Size-20 chunks (fewer tokens each) achieve higher individual relevance scores (e.g., 0.8023 for Q4) because each chunk is a concentrated signal — only the most pertinent phrases. However, they lose surrounding context
that the LLM needs to form complete answers. Size-40 chunks show slightly lower per-chunk scores (0.7684 for Q4) but provide sufficient context within a single chunk, reducing the need for the LLM to synthesize across multiple fragments. Paragraph-based chunks have the lowest precision scores (0.5801 for Q4) because they may
include extraneous sentences, but they capture the full rhetorical structure of the original document, which is valuable for nuanced queries.
Overlap and paragraph-based chunking: Overlap (5-10 tokens shared between adjacent chunks) helps when answers span chunk boundaries. Without overlap, a critical sentence split across two chunks may be lost to both retrievers. With overlap, the boundary tokens appear in both adjacent chunks, increasing the chance that at
least one chunk contains the complete answer context. Overlap is especially valuable for fixed-size chunking where boundary cuts are arbitrary. Paragraph-based chunking is preferable when documents have clear, self-contained paragraph structures — such as encyclopedia entries, legal clauses, or textbook sections — where each
paragraph represents a complete atomic idea. In this corpus of short NLP-explanatory texts, paragraph-based chunking performs adequately but is slightly outclassed by fixed-size 40 due to the short document lengths.

## Page 3

CIS 5300: Natural Language Processing
Which strategy works best overall? Fixed-size 40 (with overlap 10) performs best overall for this corpus. The documents average 2-4 sentences each, so a 40-token chunk captures most of a complete passage without cutting through sentence boundaries mid-thought. Fixed-size 20 (162 chunks) is too granular — it often
fragments answers across multiple chunks, forcing the reader to piece together information from non-contiguous segments. Paragraph-based chunking (40 chunks) respects natural boundaries but is coarsest; while it generally retrieves the right documents, scores tend to be slightly lower because entire paragraphs may include some
noise. Fixed-size 40 strikes the best balance, achieving high retrieval scores while keeping context intact.
Answer 5.1: When Does RAG Help vs. Hurt?
a) Two scenarios where RAG significantly improves answer quality:
- Scenario 1 (describe):
Answering questions about an organization's internal documents (e.g., HR policies, product specs, legal contracts) that are not in the LLM's training data.
- Why retrieval helps (explain):
The LLM has no knowledge of private or recent documents. RAG retrieves the relevant passages and grounds the answer in them, providing accurate, up-to-date information that would otherwise be impossible to generate. This eliminates hallucination from fabricated policy details.
- Scenario 2 (describe):
Answering factual questions in a specialized domain (e.g., medical guidelines, legal statutes) where precision is critical and the LLM's parametric knowledge may be outdated or incomplete.
- Why retrieval helps (explain):
RAG ensures answers are grounded in authoritative source documents, reducing the risk of hallucination in high-stakes domains. The retrieved passages serve as evidence the user can verify, increasing trust and accountability.
b) Two scenarios where RAG might hurt or add unnecessary complexity:
- Scenario 1 (describe):
Answering simple, general-knowledge questions that the LLM already knows well (e.g., "What is the capital of France?").
- Why retrieval doesn't help (explain):
The LLM already knows the answer with high confidence. RAG adds unnecessary latency and computational cost. Retrieved passages may even introduce noise or conflicting information if the corpus contains outdated or incorrect entries.
- Scenario 2 (describe):
Creative writing tasks (e.g., writing a poem, brainstorming story ideas) where factual accuracy is not the primary goal.
- Why retrieval doesn't help (explain):
RAG constrains the LLM to use only retrieved passages, stifling creativity. The retrieval step adds latency without improving output quality for generative tasks that don't depend on factual grounding.
Answer 5.2: Sparse vs. Dense Retrieval Tradeoffs
a) Practical tradeoffs in a production system:
1. Computational cost: Sparse retrieval (TF-IDF, BM25) is extremely fast - scoring involves simple term-frequency lookups and dot products over inverted indices. Dense retrieval requires encoding the query with a neural model at query time (e.g., ~10ms with all-MiniLM-L6-v2) and
computing cosine similarity over dense vectors, which is more expensive but still manageable with approximate nearest neighbor (ANN) indices.
2. Storage requirements: Sparse indices store term-frequency vectors (or inverted lists), which are compact and scale linearly with corpus size. Dense indices store fixed-dimensional vectors (e.g., 384 floats per document = 1.5KB), which require significantly more storage and specialized
vector databases (FAISS, Pinecone) for efficient search at scale.
3. Handling of rare terms and proper nouns: Sparse methods excel at matching specific terms, names, and identifiers (e.g., product codes, legal citations) because they rely on exact lexical matching. Dense retrieval may struggle with rare terms not well represented in training data, as the
encoder may not have learned meaningful embeddings for them.
4. Need for labeled training data: Sparse methods (TF-IDF, BM25) require no training data - they work out of the box. Dense retrieval requires a pre-trained encoder (or fine-tuning on domain-specific query-document pairs), which needs labeled training data that may be expensive to obtain.
b) Why might hybrid retrieval (combining sparse and dense) work better than either approach alone? How would you combine scores?
Hybrid retrieval combines sparse and dense methods to leverage the strengths of both. Sparse retrieval excels at exact keyword matching (proper nouns, rare terms, codes) and is fast and interpretable. Dense retrieval captures semantic similarity and handles paraphrases and synonyms.
A common approach is to retrieve top-k candidates from both methods separately, then merge and rerank them. Score combination can be done via: (1) reciprocal rank fusion (RRF), which combines rank positions rather than raw scores - score(d) = sum 1/(k + rank_i(d)) - avoiding the
need to normalize incomparable score scales; (2) weighted linear combination - final_score = alpha * normalize(sparse_score) + (1-alpha) * normalize(dense_score) - requiring score normalization (e.g., min-max or z-score). In practice, RRF is preferred for its simplicity and robustness.
Hybrid retrieval outperforms either approach alone because sparse methods catch exact matches that dense methods miss (like query 11 in our experiments), while dense methods catch semantic matches that sparse methods miss (like query 1 where doc 21 was only found by dense
retrieval).
Answer 5.3: Hallucination in RAG Systems
a) Two mechanisms by which hallucination can occur in a RAG pipeline:
- Mechanism 1 (describe):
Retrieved context is insufficient or irrelevant - the retriever fails to find passages that actually answer the question, but the LLM generates a plausible-sounding answer anyway by relying on its parametric knowledge rather than admitting it lacks information. This is especially common when the
retrieved passages are topically related but don't contain the specific answer, leading the LLM to "fill in the gaps" from its training data. In our experiments with Claude (claude-sonnet-4-5) at temperature=0.0, this did not occur - the model correctly stayed within the bounds of the retrieved
passages, likely because the prompt explicitly instructed it to say "I don't have enough information" when passages were insufficient.
- Mechanism 2 (describe):
The LLM misinterprets or over-extrapolates from the retrieved passages - it draws conclusions not directly supported by the text, or conflates information from multiple passages in ways that create unsupported claims. For example, if Passage 1 mentions "BM25 uses k1=1.5" and Passage 2
mentions "TF-IDF is a baseline method," the LLM might incorrectly state "TF-IDF also uses k1=1.5" by conflating the two.
b) Two strategies to reduce hallucination in RAG systems:
- Strategy 1 (describe, explain how it addresses the failure mode, discuss tradeoffs):
Prompt engineering with strict grounding instructions - explicitly instructing the LLM to answer ONLY from the provided passages and to say "I don't have enough information" if the passages don't contain the answer. This directly addresses Mechanism 1 by reducing the LLM's tendency to rely
on parametric knowledge when retrieved context is insufficient. Tradeoff: the system becomes more conservative and may refuse to answer questions it could answer correctly from its training data, reducing recall. It also depends on the LLM's instruction-following ability - smaller models may
not comply reliably.
- Strategy 2 (describe, explain how it addresses the failure mode, discuss tradeoffs):
Post-generation faithfulness verification - after generating an answer, use a second model (or the same model with a verification prompt) to check each claim in the answer against the retrieved passages, flagging or removing unsupported claims. This addresses Mechanism 2 by catching over-
extrapolations and conflations before presenting the answer to the user. Tradeoff: it doubles the computational cost (two LLM calls per question), adds latency, and the verification model itself may make errors (false positives flagging correct claims, or false negatives missing hallucinations).
Despite these costs, faithfulness checking is valuable in high-stakes domains like medicine and law.
