#  Using an LLM to rank videos based on relevance to the query

### Starting Point: The Available Data

The project begins with a structured, tabular dataset containing features extracted from video events. While this data is organized, its format makes it difficult to search for complex, contextual scenarios using natural language queries. The goal is to build a system that can understand and accurately respond to such queries.

---

### Step 1: Data Enrichment with an LLM

* **What is being done:** Each row of tabular data is programmatically converted into a detailed, consistent paragraph. This is achieved by feeding the data into a Large Language Model (Anthropic's Claude 3.5 Sonnet on AWS Bedrock) with a specific prompt template. To handle the entire dataset efficiently, this process is parallelized to make numerous API calls at once.
* **Why it is necessary:** This step transforms the structured data into a unified, unstructured narrative format. Creating these rich text descriptions is essential for enabling powerful natural language search techniques that can understand context and meaning, which is not possible with the raw tabular data alone.

### Step 2: Building a Hybrid Search Index

* **What is being done:** With the text descriptions created, a dual-component search index is built:
    1.  **A Semantic Index:** An embedding model (Amazon Titan) is used to convert each text description into a numerical vector that captures its semantic meaning. These vectors are then loaded into a FAISS index, which allows for extremely fast and efficient similarity searches.
    2.  **A Keyword Index:** A traditional TF-IDF matrix is also created from the descriptions. This allows the system to find videos based on exact keyword matches.
* **Why it is necessary:** A hybrid search approach is more robust than a single method. The semantic index finds conceptually related results, while the keyword index ensures that specific terms or phrases are not missed. Combining them maximizes recall, ensuring a comprehensive set of candidate videos is retrieved for any given query.

### Step 3: Advanced Re-Ranking and Scoring

* **What is being done:** When a user enters a query, the hybrid index retrieves an initial list of candidate videos. This list is then passed to a second, more advanced LLM agent that acts as a "ranking analyst". Using a highly detailed prompt with a scoring rubric, this LLM analyzes each candidate against the query, assigns a final relevance score from 1-10, and generates a written justification for its ranking. The system also includes a tie-breaker logic that applies bonus points for specific risk factors if multiple videos are deemed perfect matches.
* **Why it is necessary:** This final stage provides precision and intelligence. The initial search is broad to ensure no relevant videos are missed. The re-ranking step then filters and sorts these results with a nuanced, human-like level of judgment. This delivers a highly accurate and explainable final list, showing not just *what* was found, but *why* it is relevant.
