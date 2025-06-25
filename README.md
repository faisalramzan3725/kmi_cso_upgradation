# kmi_cso_upgradation
# 🧠 CSO Concept Embedding Workflow Pipeline

This project leverages the [Computer Science Ontology (CSO)](https://cso.kmi.open.ac.uk/) to process academic paper metadata, identify key research topics, and generate word embeddings using Word2Vec for semantic analysis and downstream NLP tasks.

---

## ✨ Diagram

Figure 1: Workflow diagram illustrating the five-step pipeline for creating computer science concept embeddings using CSO (Computer Science Ontology) and Semantic Scholar paper dataset. The process flows from data preprocessing through concept matching to embedding model training and downstream applications.

![image](https://github.com/user-attachments/assets/7a6c8e44-e510-4106-8a31-e24c555ec5a8)

## 🚀 Workflow steps / Pseudo code:

Step 1: Download and Preprocess CSO Concepts

  1.1 Download the CSO ontology .ttl (Turtle) file, which contains structured computer science concepts.
  
  1.2 Extract all concept labels from the file using an RDF parser (e.g., rdflib in Python).
  
  1.3 Preprocess the labels (If required):

  - Convert to lowercase
    
  - Remove extra spaces or symbols
    
  - Keep the concept phrases as-is (e.g., "computer science")
    

Step 2: Download and Preprocess the Paper Dataset

  2.1 Download the full Semantic Scholar dataset or a custom dump.
  
  2.2 Parse the dataset to create a list of documents, where each document consists of:
  
  - Title
  - Abstract
  - Title
  - Abstract

  2.3 Preprocess each document:

  - Convert text to lowercase
  
  - Remove or normalize special characters, punctuation, or extra whitespace

Step 3: Concept Matching with NLTK or Gensim

  3.1 Use CSO concepts as search terms.
  
  3.2 For each paper (title + abstract), search for exact matches of CSO concepts.
  
  3.3 If a match is found:

- Replace the matched phrase with an underscore-separated version (e.g., "computer science" → "computer_science")

 Note: This keeps multi-word terms as a single token for training

  Example: CSO Concepts:
  ["computer science", "web", "information retrieval", "large language models"]
  
  - Original Abstract:
  "...recent advances in computer science and large language models have improved web search..."
  
  - After Replacement:
  "...recent advances in computer_science and large_language_models have improved web search..."

Step 4: Phrase Mining (Optional but Recommended)

  4.1 Extract frequent bigrams and trigrams from the paper dataset using NLTK or Gensim's Phrases module.

Step 5: Train Embedding Model

  5.1 Use the cleaned and processed paper dataset to train a Word2Vec model using gensim's latest implementation.
  
  5.2 The model will learn vector embeddings where similar scientific terms are close in vector space.

Step 6: Topic Modeling or Downstream Tasks

  6.1 The trained Word2Vec model can now be used for:
  
  - Topic modeling
  
  - Semantic clustering
  
  - Recommendation systems
  
  - Query expansion, etc.

---

## Pseudo Code:

      
    Step 1: Load and Preprocess CSO Concepts
    
      cso_labels = load_ttl("cso.ttl")
      
      cso_labels = [clean_text(label) for label in cso_labels]
      
    
    Step 2: Load and Preprocess Paper Dataset
      
      titles, abstracts = load_paper_dataset("papers.txt")
      
      documents = preprocess_documents(titles + abstracts)
      
    
    Step 3: Match and Replace CSO Concepts in Documents
    
      processed_docs = []
      
      for doc in documents:
          for concept in cso_labels:
              if concept in doc:
                  doc = doc.replace(concept, concept.replace(" ", "_"))
          processed_docs.append(doc)
    
    
    Optional Step 4: Phrase Mining
    
      phrases = extract_phrases(processed_docs, min_count=5)
      
      for phrase in phrases:
          for i, doc in enumerate(processed_docs):
              if phrase in doc:
                  processed_docs[i] = doc.replace(phrase, phrase.replace(" ", "_"))
                  
    
    Step 5: Train Word2Vec Model
    
      tokenized_docs = [doc.split() for doc in processed_docs]
      
      model = train_word2vec(tokenized_docs)
      
      save_model(model, "scientific_embeddings.model")
      
    
    Step 6: Use Embeddings for Topic Modeling or Search etc..

 ---



## ✨ Features

- **Ontology-based Concept Extraction**
  - Parses CSO ontology and extracts research topics
- **Text Normalization & Tokenization**
  - Replaces concepts with underscore-form (e.g., `large_language_models`)
  - Supports phrase mining (bigrams/trigrams)
- **Word Embedding Training**
  - Trains Word2Vec model on preprocessed paper metadata
- **Modular Architecture**
  - Can be extended to semantic search, clustering, topic modeling


## 📁 Project Structure

📁 cso_label/ # Ontology and extracted concepts

📁 CSO/ # Contains CSO.3.4.1.ttl (ontology file)

- cso_label_counts.csv # Extracted CSO concepts with counts

📁 paper_dataset/ # Paper metadata and processed versions

- paper_dataset.csv # Input dataset (title and abstract)

- processed_title_abstract_v2.csv # Output after CSO + trigram processing

--- 

## Files and Descriptions

1_cso_script.py:
Extracts concepts from the CSO ontology to prepare a list of topics for downstream processing. This script serves as the foundation for identifying relevant terms in the dataset.

2_paper_dataset.py:
Loads the initial dataset (e.g., CSV files with titles and abstracts) and filters the columns to retain only the necessary data (e.g., titles and abstracts), ensuring compatibility with subsequent steps.

3_dataset_partitions.py:
Partitions the large dataset files into manageable chunks based on total lines and memory affordance. This step splits the data into multiple files (e.g., abstracts_part_v1_X.txt) for parallel processing.

4_clean_data.py:
Performs preprocessing and cleaning of the partitioned data, replacing space-containing CSO topics with underscore versions (e.g., machine learning → machine_learning) and normalizing text artifacts.

5_strip_tokens.py:
Strips and processes each sentence into tokenized forms, splitting on spaces and removing punctuation. The output is saved as JSON files (e.g., abstracts_filtered_striped_part_v1_X.txt) for further analysis.

6_bigrams_trigrams.py:
Identifies bigrams and trigrams from the tokenized data using the Gensim library. The results are saved as separate files (e.g., abstracts_bigrams_part_v1_X.txt and abstracts_trigrams_part_v1_X.txt).

7_w2v_model.py:
Configures and trains a Word2Vec model on the trigram-processed sentences. The trained model is saved as a binary file (e.g., 9M[256-10]_sg.bin) with customizable parameters like vector size and window.

pipeline.py:
Orchestrates the complete workflow, executing steps 1 through 7 sequentially. This file manages the end-to-end process from data loading to model training, with logging for monitoring.

requirements.txt:
all project dependencies (e.g., pandas, langdetect, gensim) required to run the scripts. Use pip install -r requirements.txt to install them.


 
 ---



## 🛠️ Setup Instructions

### 1. Clone the Repository

bash
git clone https://github.com/yourusername/kmi_project_cso.git

cd kmi_project_cso


### 2. Install Dependencies
pip install -r requirements.txt

Alternatively, install manually:
pip install pandas gensim nltk rdflib



