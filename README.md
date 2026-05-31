# CPF Customer Agent - Powered by Neo4j &amp; GraphRAG
A hackathon to build an AI agent on top of a knowledge graph that understands context

## 1.	What It Does 
The CPF Customer Agent is an advanced reasoning assistant built for customer service executives handling Central Provident Fund (CPF) inquiries. It uses GraphRAG to merge semantic vector search with structural graph relationships. 

When an executive receives an inquiry, the agent simultaneously retrieves verified policy documents and crucially layers in the member's specific profile (e.g. age, account balance) and past question history. By executing multi-hop reasoning over this connected data, the agent provides customer service executives with hyper-personalised, accurate and factually grounded response in real-time.

For example, if a member asks: "How much do I need to top up to reach the FRS (Full Retirement Sum) in 2027?", the agent doesn't just return a generic definition of FRS. It automatically retrieves the member's current account balance via the graph, calculates their specific profile constraints, and produces a contextual answer.

## 2.	Dataset 
The agent utilises GraphRAG to structurally unify three distinct data dimensions into a single, cohesive knowledge network:
a) Customer Profiles (Structured): synthetically generated customer data containing attributes like customerId, gender, age, accountBalance and archetype
b) FAQ Knowledge Base (Unstructured): question-answer pairs extracted from CPF website. The knowledge base contains questionId, questionText, answerText, questionEmbedding (generated via gemini-embedding-001)
c) Interaction History (Relational): direct relationships tracking what customers have asked in the past
<img width="822" height="739" alt="image" src="https://github.com/user-attachments/assets/60ffc1ea-060b-46ff-bc28-c0578ec13e39" />

## 3.	Why a Graph Fits
In public pension systems, policy answers are a dynamic function of a citizen's identity, not a static document. A graph addresses this by providing the ultimate contextual scaffolding:
a) **Contextual Convergence Without Joins**: A flat vector search can find a document about "CPF LIFE eligibility," but it cannot simultaneously evaluate if Customer X is eligible. GraphRAG allows the agent to pull the semantically relevant policy node and instantly traverse to the specific customer node in a single operation.
b) **Hyper-Focused Grounding**: GraphRAG extracts a precise, pre-filtered subgraph (exact policy criteria + customer profile) to inject into the LLM prompt, mitigating hallucinations
c) **Temporal Awareness**: By traversing the [:ASKED] relationships, the agent can instantly see if a customer's current question is a follow-up to a previous inquiry (e.g., a member asking about housing policy right after asking about retirement funds). This prevents the executive from repeating information and provides an immediate, continuous narrative of the member's journey.

## 4.	Agent Tools 
The agent pipeline is driven by an orchestrated suite of specialised tools that blend deterministic graph querying and semantic reasoning using three core tools:
a) Get Customer Profile	(Cypher Template) - retrieves structured demographic variables
b) Get Customer Question History (Cypher Template) - traverses the [:ASKED] relationships for a specific Customer ID to return a chronological list of prior questions 
c) Get semantically similar question (Similarity Search) - embeds the incoming inquiry using gemini-embedding-001 and executes a cosine similarity search against the knowledge base to find the closest verified CPF FAQ

## 5.	Agent & Multi-Hop Reasoning in Action
**a) Scenario A: Eligibility Check**
- Query: Customer X asks: 'Am I eligible to join CPF LIFE?'
- Similarity Search Tool: The agent embeds the question text and searches the vector index. It returns the CPF LIFE eligibility node, which states that eligibility depends on an age threshold of 65 to 80 years old
- Get Customer Profile Tool: The agent calls the Cypher template and fetches Customer X’s age property 
- Reasoning & Output: The agent combines these signals. Instead of a generic eligibility criterion, the agent generates a definitive, profile verified "Yes/No" eligibility draft for the executive
<img width="828" height="280" alt="image" src="https://github.com/user-attachments/assets/a4969947-9050-41d5-b98c-2a264d9aec5d" />
<img width="828" height="283" alt="image" src="https://github.com/user-attachments/assets/41061e92-c9e1-43f2-af59-f006d046e5ec" />

**b) Scenario B: Dynamic Calculations**
- Query: Customer X asks: 'How much do I have to top up to hit FRS in 2027?'
- Similarity Search Tool: The agent embeds the question text and searches the vector index. It returns the node detailing the 2027 FRS requirements
- Get Customer Profile Tool: The agent calls the Cypher template for Customer ID X and pulls the customer's accountBalance
- Reasoning & Output: The agent merges the policy and the member's current balance into a unified context window. The LLM mathematically processes this highly localised context window to generate a step-by-step calculation, explaining exactly how much the customer needs to top up based on their profile
<img width="825" height="984" alt="image" src="https://github.com/user-attachments/assets/639559a5-5f11-43b1-9318-8c4f48cdd307" />

## 6.	Tech Stack
<img width="940" height="346" alt="image" src="https://github.com/user-attachments/assets/e5256a23-2508-4f1c-92b8-cbd0f38c4bb3" />
- Graph Database & Vector Index: Neo4j AuraDB to handle Cypher templates, vector search and cosine similarity vector matching
- Core Reasoning Engine: Gemini 1.5 Pro / Flash
- Embeddings Model: Vertex AI gemini-embedding-001
- Agent Orchestration: LangChain / LlamaIndex for Aura agent orchestration, tool-calling pipelines and managing the GraphRAG memory flow
- Backend: Asynchronous Python / FastAPI using the official Neo4j Python Driver.

## 7.	What is the Impact?
a) **From "Search" to "Reasoning"**: Most AI bots perform a vector lookup and dump a block of text into a prompt. The CPF Customer Agent reasons across a connected knowledge network, providing the LLM with the multi-dimensional context required for complex public policy
b) **Enterprise-Grade Grounding**: Public sector applications require absolute precision. By bounding the LLM strictly to factual subgraphs retrieved via Cypher templates, hallucinations are completely mitigated.
c) **Operational Efficiency**: By unifying policies, customer profiles, and conversation history into a single, cohesive graph canvas, we eliminate the need for customer service executives to swivel-chair between different CRM tabs and policy manuals, drastically slashing Average Handle Time (AHT) while driving up response accuracy.


## Resources
a) https://neo4j.com/developer/genai-ecosystem/
b) https://neo4j.com/product/aura-agent/ 
