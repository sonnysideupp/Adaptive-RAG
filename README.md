# Adaptive-RAG


Adaptive RAG is a RAG strategy that combines query analysis with self-corrective RAG. 


I built an Adaptive RAG using LangGraph and Open AI GPT LLM to analyze Lyft's 2013 fiscal year 10-K financial document and answer users' questions regarding the information inside the 10-K document.


# Data Processing

In the data processing phase, I downloaded Lyft's 2013 10-K PDF file, formatted it, and saved to disk as a plain TXT file. 

Then I loaded the TXT file into memory, split the text into chunks of 500 tokens, and then embedded the chunks using Open AI's text embedding to Chroma vector DB. 

# Adaptive-RAG Graph

Below is the architecture of the Adpative RAG graph:

<img src='/assets/rag_graph.png' >

When a user asks a question, the query analyzer will analyze the question and then route it to the appropriate data source. If the question is related to Lyft, it will route to the vector DB. Otherwise it will route to web search. If the query analyzer fails to create a classification LLM will be used as a default. 

After the documents are retrieved from the vector DB, LLM grader will assess the documentions' relevancies and filter out irrelevant retrived documents. If all documents are irrelevant, new documents will be generated from web search. 

Then all the relevant documents will be combined to generate a coherent answer to the user's original question. The generated answer will be assessed to see if there are hallucinations and if the answer resolves the question. 

# Results

Below is the result for when the user asks: 'In what areas is Lyft looking to innovate in the future?'. The RAG correctly routes the question to the vector DB and fetches the relevant documents to formulate the answer to the user question. 

<img src='/assets/lyft_innovation_example.png'>

Below is the result for when the user asks: '"What is Lyft's market share in the US?'. The RAG routes the question to the vector DB but all the fetched documents are not relevant to the question (information doesn't exist in the 10-K document). As a result, web search is used as a backup to retrieve the relevant answer. 

<img src='/assets/lyft_market_share_example.png'>

Lastly, when a user ask a question that is not related to Lyft. It will route to the web to fetch the answer. 

<img src='/assets/irrelevant_question.png'>