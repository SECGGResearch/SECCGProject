# SECCG

## Introduction
SECCG (Security Entity and Community Cyber Graph) is a tool designed to build security-oriented knowledge graphs from unstructured Cyber Threat Intelligence (CTI) articles. By leveraging multiple collaborative Large Language Model (LLM) agents and a dual-memory design, SECCG extracts security-related entities, reveals hidden behaviors, and identifies relationships across different CTI articles. This approach significantly outperforms traditional methods in analyzing and visualizing complex cyber threats.
SECCG also includes security-oriented community detection, which helps analysts uncover connections between various cyber threats and better understand the evolving threat landscape. With high precision and recall rates, SECCG enables efficient extraction of insights from a large number of CTI articles, providing valuable support for threat intelligence and incident response teams.

## Approach
Figure 2 shows the architecture of SECCG, which consists of three phases: (1) CTI Article Classification, (2) Knowledge Graph Construction, and (3) Security-Oriented Community Detection.

![image](https://i.imgur.com/iD6BRT9.png)

1. **CTI Article Classification**: This phase determines whether an article contains information about cyber threats and highlights the sections that need further analysis. It includes three components: (1) a tactics and behavior model to classify CTI sentences that describe cyber attack tactics and security-related behaviors; (2) an LLM-based entity recognition component to identify security-related entities; and (3) a classification component that uses CTI sentence data and IOC statistics to identify articles with cyber threat knowledge.

2. **Knowledge Graph Construction**: At the core of SECCG, this phase employs multiple LLM agents to extract knowledge from CTI articles and represent it in the form of triples, which are combined into a knowledge graph. Five types of LLM agents participate in this process: the worker agent extracts triples, the integrator agent merges results, the refiner agent normalizes data, the checker agent corrects errors, and the merger agent assembles triples from different segments to form a unified graph using long-term memory.

3. **Security-Oriented Community Detection**: Built on top of the knowledge graph, SECCG detects security-oriented communities by using the outputs of the CTI sentence and article classification components, along with other edge statistics. This helps identify interconnected cyber threats and behaviors, offering a comprehensive view of how various threats are related.

## Evaluation

We evaluated **SECCG** on a comprehensive dataset consisting of 72,538 Cyber Threat Intelligence (CTI) articles from 67 online sources, as well as 1,000 CVE-related articles covering 100 CVEs from 2024. **SECCG** demonstrated notable improvements over existing methods in both behavior identification and knowledge graph construction.

### CVE Behavior Description

The results show that **SECCG** outperforms models like **NVD** and **ChatGPT** in identifying behaviors related to CVEs, especially for CVEs not fully described by the NVD (referred to as CVEnvd+). **SECCG** consistently identifies more behaviors across all CVSS levels.

- **All CVEs**: SECCG identifies an average of 3.73 behaviors per CVE, closely aligning with the ground truth (3.77) and outperforming NVD (3.4) and ChatGPT (3.58).
  
- **CVEnvd+**: For CVEs not fully described by NVD, SECCG identifies an average of 4.39 behaviors, significantly surpassing NVD (2.87) and ChatGPT (3.74). This demonstrates SECCG's strength in extracting more comprehensive insights from incomplete datasets.

![image](https://i.imgur.com/t3lnBPp.png)

### Knowledge Graph Construction

In terms of knowledge graph construction, **SECCG** achieves superior performance with higher recall and F1 scores compared to **GPT-4o** and **GraphRAG**, demonstrating its advanced capability in extracting security-related behaviors and relationships while maintaining high precision.

![image](https://i.imgur.com/HFaHHDN.png)

### Classification Model Effectiveness

The classification models used by **SECCG**—including the tactics model, behavior model, and CTI article classification model—demonstrate strong performance in identifying cyber attack tactics and behaviors.

- **Tactics Model**: Trained using active learning with a dataset of 17,420 labeled sentences, the tactics model achieves a weighted F1-score of 89%, with high precision (88%) and recall (90%). Notably, **Persistence** and **Impact** tactics score particularly well with F1-scores of 92% and 93%, respectively.

- **Behavior Model**: This model, which classifies attack behaviors, performs well with 83.23% precision and 82.01% recall, effectively identifying key behaviors across a range of cyber threats.

- **CTI Article Classification Model**: With a labeled dataset of 3,588 articles (1,799 CTI articles and 1,789 non-CTI articles), this model achieves a precision of 81.39% and a recall of 84.38%. By adjusting the threshold to 0.2, the model enhances recall to 90.5%, ensuring that fewer relevant articles are mistakenly filtered out, thus improving community detection accuracy.


