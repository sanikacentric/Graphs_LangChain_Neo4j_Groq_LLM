# Graphs LangChain Neo4j Groq LLM — Knowledge Graph RAG Pipeline

A knowledge graph-powered RAG system integrating Neo4j graph database with LangChain and Groq's ultra-fast LLM inference. Enables natural language querying of graph-structured data with Cypher query generation and relationship-aware retrieval.

## Description

This project combines the power of graph databases with large language models to create a knowledge graph RAG system. Instead of flat vector retrieval, it leverages Neo4j's graph structure to traverse entity relationships, enabling multi-hop reasoning queries that traditional RAG cannot handle.

Groq's LPU inference engine provides sub-100ms response times, making the system fast enough for real-time conversational graph querying. LangChain orchestrates the pipeline from natural language input through Cypher generation, graph execution, and response synthesis.

## Features

- **Neo4j Knowledge Graph** — Entity and relationship storage with full Cypher query support
- - **LangChain GraphCypherQAChain** — Automatic natural language to Cypher query translation
  - - **Groq LLM Backend** — Ultra-fast inference with Llama 3 and Mixtral models via Groq API
    - - **Graph Schema Introspection** — Automatic schema extraction to inform LLM query generation
      - - **Multi-Hop Reasoning** — Traverse multiple relationship hops to answer complex queries
        - - **Entity Extraction** — NER pipeline to extract entities and relationships from documents for graph population
          - - **Vector + Graph Hybrid Search** — Combine semantic similarity with graph traversal for richer retrieval
            - - **Streaming Responses** — Real-time token streaming from Groq for responsive UX
             
              - ## Architecture
             
              - ```
                Natural Language Query
                        |
                        v
                LangChain (GraphCypherQAChain)
                        |
                        +--> Schema Fetcher --> Neo4j Schema
                        |
                        +--> Cypher Generator (Groq LLM)
                        |         |
                        |         v
                        |   Cypher Query
                        |         |
                        +--> Neo4j Executor --> Graph Results
                                  |
                                  v
                        Response Synthesizer (Groq LLM)
                                  |
                                  v
                        Natural Language Answer
                ```

                ## Tech Stack

                | Component | Technology |
                |-----------|-----------|
                | Graph Database | Neo4j (AuraDB / Self-hosted) |
                | LLM | Groq (Llama 3, Mixtral 8x7B) |
                | Orchestration | LangChain |
                | Query Language | Cypher |
                | Embeddings | HuggingFace / OpenAI |
                | Language | Python 3.10+ |

                ## Setup Instructions

                ### Prerequisites

                - Python 3.10+
                - - Neo4j instance (Neo4j AuraDB free tier or local Docker)
                  - - Groq API key
                   
                    - ### Installation
                   
                    - ```bash
                      git clone https://github.com/sanikacentric/Graphs_LangChain_Neo4j_Groq_LLM.git
                      cd Graphs_LangChain_Neo4j_Groq_LLM
                      pip install langchain langchain-groq neo4j langchain-community
                      ```

                      ### Configuration

                      ```bash
                      export GROQ_API_KEY="your-groq-api-key"
                      export NEO4J_URI="bolt://localhost:7687"
                      export NEO4J_USERNAME="neo4j"
                      export NEO4J_PASSWORD="your-password"
                      ```

                      ### Populate the Graph

                      ```python
                      from neo4j import GraphDatabase

                      driver = GraphDatabase.driver(NEO4J_URI, auth=(NEO4J_USERNAME, NEO4J_PASSWORD))
                      with driver.session() as session:
                          session.run("CREATE (p:Person {name: 'Alice'})-[:WORKS_AT]->(c:Company {name: 'OpenAI'})")
                      ```

                      ### Query the Graph

                      ```python
                      from langchain_groq import ChatGroq
                      from langchain.chains import GraphCypherQAChain
                      from langchain_community.graphs import Neo4jGraph

                      graph = Neo4jGraph(url=NEO4J_URI, username=NEO4J_USERNAME, password=NEO4J_PASSWORD)
                      llm = ChatGroq(model="llama3-8b-8192")

                      chain = GraphCypherQAChain.from_llm(llm, graph=graph, verbose=True)
                      result = chain.run("Who does Alice work for?")
                      print(result)
                      ```

                      ## License

                      MIT License
