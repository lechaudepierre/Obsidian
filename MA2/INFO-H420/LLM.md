## Word Embeddings

Word embeddings map discrete words to continuous vectors, enabling models to capture semantic similarity and perform vector operations (e.g., cosine distance). Benefits include fine‑grained representations, visualization in semantic spaces, and self‑supervised learning from large corpora. Classic and contextual examples: Word2Vec (2013), GloVe (global co‑occurrence), ELMo (contextual, bi‑LM), and BERT (masked LM with contextual embeddings). These embeddings underpin modern NLP by turning symbolic tokens into geometry amenable to neural computation.

## Language Modeling; objective/loss function

Language modeling estimates the next‑token distribution P(w | h) given a history h, and uses the chain rule to assign probabilities to full sequences: P(w1…wk) = Π P(wt | w1…wt−1). Training maximizes the likelihood of observed data via cross‑entropy (negative log‑likelihood) under an i.i.d. assumption:

- Objective: θ* = arg min −Σ log Pθ(wi | hi)
    
- Practical training: teacher forcing for next‑token prediction  
    Evaluation:
    
- Intrinsic: perplexity = exp(average cross‑entropy), interpreted as an “average branching factor”; lower is better, but only reliable when test data matches training domain
    
- Extrinsic: task performance on GLUE/SuperGLUE, HumanEval, HellaSwag, GSM‑8K; human preferences (Chatbot Arena); LLM‑as‑judge to approximate human evaluation
    

## Attention mechanism; query‑key‑value

Self‑attention computes a weighted sum of value vectors where weights reflect the similarity between queries and keys:

1. Project token embeddings to queries Q, keys K, values V
    
2. Compute attention scores (e.g., dot product QKᵀ)
    
3. Apply softmax to normalize scores
    
4. Weight and sum values to produce context‑aware representations  
    Intuition: a “fuzzy hashtable” where a query retrieves relevant values via approximate matches to keys. Multi‑head attention lets the model attend to different relations simultaneously (e.g., coreference vs. modifiers). Transformer advantages over RNNs include constant interaction distance (O(1)) and parallelizable computation. Architectures:
    

- Encoder‑decoder (original Transformer; e.g., T5)
    
- Decoder‑only for causal LMs (e.g., GPT)
    
- Encoder‑only for masked LMs (e.g., BERT)
    

## Sampling from output distribution; role of temperature

Decoding methods determine how tokens are selected from the model’s output distribution:

- Deterministic: greedy (argmax), beam search (searches top sequences)
    
- Stochastic truncation: top‑k (sample among the k most probable), top‑p/nucleus (sample from the smallest set covering cumulative probability p)
    
- Temperature: rescale logits u → u/τ before softmax
    
    - Low τ (0 < τ ≤ 1) sharpens the distribution, increasing determinism
        
    - High τ (> 1) flattens the distribution, increasing diversity  
        As τ → 0, the distribution collapses toward the top token; with higher τ, exploration increases. Choice of decoding affects diversity, coherence, and faithfulness.
        

## Retrieval Augmented Generation; retriever, data store, embedding model, LLM

RAG grounds generation in external knowledge:

- Data store: corpus indexed by vector embeddings; documents are chunked for efficient retrieval
    
- Embedding model: encodes queries and documents into a shared vector space capturing semantic similarity
    
- Retriever: nearest‑neighbor search over embeddings to fetch top‑K relevant chunks (neural IR replaces term‑based TF‑IDF for semantic matching)
    
- LLM: conditions its response on retrieved context, improving factuality and domain coverage without full fine‑tuning  
    Pipeline: embed query → retrieve passages → construct prompt with citations/context → generate answer. RAG complements prompt engineering and can be more cost‑effective than fine‑tuning in dynamic or broad domains.
    

## Approximate nearest neighbor; Hierarchical Navigable Small Worlds (HNSW)

ANN accelerates similarity search in high‑dimensional spaces. HNSW builds a multi‑layer “small‑world” graph with hierarchical skip connections:

- Upper layers: sparse graphs for fast, coarse navigation toward the right neighborhood
    
- Lower layers: denser graphs for fine‑grained local search  
    Queries start high, descend layer by layer, maintaining candidates via greedy or heuristic steps. This yields high recall with low latency, making HNSW a popular choice for large‑scale vector search in embedding‑based retrieval for RAG systems.