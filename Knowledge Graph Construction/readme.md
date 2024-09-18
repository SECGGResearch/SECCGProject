## Knowledge Graph Construction

### Overview
In the **Knowledge Graph Construction** module, we leverage SECCG to create a security-oriented knowledge graph from Cyber Threat Intelligence (CTI) articles. The system plays a crucial role in identifying hidden behaviors and relationships of cyber threats described across multiple CTI articles. The constructed knowledge graph provides a comprehensive view of security-related entities, such as vulnerabilities (CVEs), attack tactics, and threat groups, along with the relationships between them.

### Key Steps

1. **CTI Sentence Classification**:
   - SECCG employs RoBERTa classification models for **CTI Article Classification**, which is a critical step in focusing on relevant information in large CTI articles. These models are used to classify sentences into **tactics sentences**, distinguishing those that describe cyber attack techniques from irrelevant content.
   - The **Tactical Model** identifies sentences that discuss attack tactics. This classification ensures that the knowledge graph is built on highly relevant and focused data.

2. **Security-Related Entity Recognition**:
   - **Security-related entity recognition** is another essential step where SECCG identifies critical entities, such as malware names, CVE identifiers, IP addresses, and more. Multiple LLM agents are used to extract security-specific entities from the text, ensuring that key information related to cyber threats is captured efficiently.
   - This step is vital in forming the nodes of the knowledge graph, as it determines which entities (such as malware or CVEs) should be connected within the graph structure. By recognizing these entities with high accuracy, SECCG ensures that the graph represents real-world cyber threats and their interactions across various CTI articles.

3. **Triple Extraction**:
   - After identifying relevant sentences and security entities, SECCG extracts knowledge in the form of triples (subject, relation, object) that describe how different entities interact (e.g., "Formbook **is** malware" or "CVE-2024-XXXX **exploits** Windows").
   - Multiple LLM agents collaborate to extract these triples, refining and merging them through a feedback loop. This process ensures the accuracy of the knowledge represented in the graph, with each triple contributing to the relationships between entities like vulnerabilities, tactics, and malware.

4. **Graph Construction**:
   - The knowledge graph is built using the triples extracted from CTI articles. The nodes represent the security-related entities (e.g., CVEs, malware, APT groups), while the edges depict the relationships or interactions (e.g., exploitation, attack behaviors) among these entities.
   - SECCG uses advanced techniques to ensure consistency across multiple articles, refining connections using vector embeddings to align similar entities. The result is a comprehensive, security-oriented knowledge graph that reveals cross-article correlations.

### Files Description

1. **Knowledge Graph Construction.ipynb**:
   - This Jupyter Notebook implements the full process for SECCG's knowledge graph construction. It processes input CTI articles, classifies sentences, recognizes security entities, and builds the knowledge graph by extracting relevant triples.

2. **1000_articles_of_100CVEs_in_2024.csv**:
   - This CSV file contains 1,000 CTI articles corresponding to 100 CVEs from 2024. The articles serve as the input data for the notebook to generate a security-oriented knowledge graph.

### Results and Evaluation
- SECCG was evaluated on 1,000 articles covering 100 CVEs, achieving a precision of 97.32% and a recall of 99.11% in constructing accurate knowledge graphs. These results far surpass traditional methods like NVD or ChatGPT.
- The system identified 98.9% of the relevant behaviors described in the CVE articles, surpassing NVD and ChatGPT by significant margins. For CVEs lacking full descriptions in NVD (referred to as CVEnvd+), SECCG identified an average of 4.39 behaviors out of 4.48.

### Usage

1. Ensure you have downloaded and extracted all the files in this folder.
2. Open and run the **Knowledge Graph Construction.ipynb** notebook to process the data in **1000_articles_of_100CVEs_in_2024.csv**.
3. The notebook will automatically classify sentences, recognize security-related entities, and build the knowledge graph, which can then be visualized and further analyzed.
