# A Large-Language-Model Framework for Automated Humanitarian Situation Reporting

This repository contains the complete implementation, data, and results for the paper *"A Large-Language-Model Framework for Automated Humanitarian Situation Reporting"*.

## Overview

This framework automatically generates comprehensive situation reports from humanitarian documents for specific events. The pipeline processes raw humanitarian data through multiple stages—including document clustering, question generation, answer extraction, and report synthesis—to produce structured, citation-backed reports that can be organized by topics or Sustainable Development Goals (SDGs).

![Pipeline Overview](./Images/pipeline.pdf)
*The complete workflow from document selection to final report generation*

## Repository Structure
```
├── Codes/              # Implementation notebooks for each pipeline stage
├── Results/            # Intermediate outputs from each processing step
├── Results_evaluation/ # Human-annotated evaluation data
└── Viewer/             # Interactive visualization of generated reports
```

---

## Pipeline Workflow

### 0. Data Selection

**Notebook:** `0-data-selection-countryPeriod.ipynb`

**Description:**
Loads the master Excel file and filters documents by country and time period (week). Applies manual filtering on document titles and enriches selected documents with metadata.

**Input paths:**
- `full_data.xlsx`

**Output paths:**
- `/Results/Sources/SourcesCountryEvent/`
- `/Results/Sources/SourcesCountryEvent-Metadata/`

---

### 1. Document Cleaning & Clustering

**Notebook:** `1-clean-clustering.ipynb`

**Description:**
Processes source documents by splitting them into paragraphs, cleaning and preprocessing text, performing dimensionality reduction, and applying HDBSCAN clustering. Uses random parameter search with evaluation metrics to optimize cluster quality.

**Input paths:**
- `/Results/Sources/SourcesCountryEvent-Metadata/`
- `/Results/Sources/SourcesCountryEvent/`

**Output paths:**
- `/Results/paragraphs_metadata/`
- `/Results/Cluster/Clusters/`

---

### 2. Question Generation

**Notebook:** `2.0-QuestionsGeneration.ipynb`

**Description:**
Generates cluster-level questions based on the methodology described in the paper. Uses metadata from source documents and applies deduplication to filter redundant questions.

**Input paths:**
- `/Results/Sources/SourcesCountryEvent-Metadata/`

**Output paths:**
- `/Results/Questions/Test questions different prompts/1-Questions generated/Dev set`

&nbsp;

**Notebook:** `2.1-Questions_filtering_SDGs.ipynb`

**Description:**
Filters previously generated questions using evaluation metrics and classifies each question into relevant Sustainable Development Goals (SDGs).

**Input paths:**
- `/Results/Questions/Test questions different prompts/1-Questions generated/Dev set`

**Output paths:**
- `/Results/Questions/Test questions different prompts/3-Filtered questions/Dev set/`
- `/Results/Questions/Test questions different prompts/4-Filtered questions with SDGs/Dev set`

---

### 3. Answer Extraction

**Notebook:** `3.0-Rag GeneratedQuestions.ipynb`

**Description:**
Implements a Retrieval-Augmented Generation (RAG) pipeline that takes filtered questions and related paragraphs to produce answers enriched with citations to source documents.

**Input paths:**
- `/Results/Questions/Test questions different prompts/3-Filtered questions/Dev set/`
- `/Results/Questions/Test questions different prompts/1-Questions generated/Dev set/`

**Output paths:**
- `/Results/Answers/Answers-Subtopics/Dev set`

&nbsp;

**Notebook:** `3.3-RAG_PostProcessingCitations.ipynb`

**Description:**
Post-processes RAG outputs to clean, update, and refine citation formatting, ensuring citation accuracy and consistency across all generated answers.

**Input paths:**
- `/Results/Answers/Answers-Subtopics/Dev set`

**Output paths:**
- `/Results/Answers/Answers-Subtopics/Dev set/Updated_citations/`

---

### 4. Summary Generation

**Notebook:** `5-Summary for each paragraph.ipynb`

**Description:**
Generates cluster-level summaries from RAG-processed paragraphs, producing narrative descriptions for each identified topic cluster.

**Input paths:**
- `/Results/Answers/Answers-Subtopics/Dev set/Updated_citations/`

**Output paths:**
- `/Results/Summaries/UniqueSummary-EachCluster/`

&nbsp;

**Notebook:** `5-SDG-Summary.ipynb`

**Description:**
Organizes paragraphs and answers by Sustainable Development Goals and generates SDG-level summaries for structured reporting aligned with humanitarian frameworks.

**Input paths:**
- `/Results/Answers/Answers-Subtopics/Dev set/Updated_citations/`
- `/Results/Questions/Test questions different prompts/4-Filtred questions with SDGs/`

**Output paths:**
- `/Results/Answers/Answers-SDGs`
- `/Results/Summaries/UniqueSummary-EachSDG`

---

### 5. Executive Summary Generation

**Notebook:** `4-Executive summary generation.ipynb`

**Description:**
Synthesizes RAG-processed paragraphs into a concise executive summary for each event, including a headline that captures the key situation overview.

**Input paths:**
- `/Results/Answers/Answers-subtopics/Dev set/Updated_citations`

**Output paths:**
- `/Results/Executive Summary/Dev set`

&nbsp;

**Notebook:** `4.1-Executive summary-PostProcess Citations.ipynb`

**Description:**
Refines and updates citations in the executive summaries to ensure consistency and accuracy.

**Input paths:**
- `/Results/Executive Summary/Dev set`

**Output paths:**
- `/Results/Executive Summary/Dev set/updated citations`

---

### 6. Report Visualization

**Notebook:** `6-Report Generation.ipynb`

**Description:**
Integrates all previously generated outputs to produce final situation reports. Generates both Q&A-style and summary-style reports in Markdown and JSON formats.

**Input paths:**
- `./Results/Sources/SourcesCountryEvent/Dev set/`
- `./Results/Cluster/Clusters+Headline`
- `./Results/Answers/Answers-subtopics/Dev set/Updated_citations`
- `./Results/paragraphs_metadata`
- `./Results/Executive Summaries/Dev set/Updated_citations`
- `./Results/Summaries/UniqueSummary-EachCluster/Dev set`

**Output paths:**
- `./Results/Reports/JSON_Report_QA/Dev set`
- `./Results/Reports/Markdown_Report_QA/Dev set`
- `./Results/Reports/JSON_Report_Summary/Dev set`
- `./Results/Reports/Markdown_Report_Summary/Dev set`

&nbsp;

**Notebook:** `6-Report Generation-SDGs.ipynb`

**Description:**
Generates SDG-organized versions of the final reports, structuring content according to Sustainable Development Goals. Produces both QA and Summary formats in Markdown and JSON.

**Input paths:**
- `./Results/Sources/SourcesCountryEvent/Dev set/`
- `./Results/Cluster/Clusters+Headline`
- `./Results/Answers/Answers-subtopics/Dev set/Updated_citations`
- `./Results/paragraphs_metadata`
- `./Results/Executive Summaries/Dev set/Updated_citations`
- `./Results/Summaries/UniqueSummary-SDGs/Dev set`

**Output paths:**
- `./Results/Reports/JSON_Report_QA_SDGs/Dev set`
- `./Results/Reports/Markdown_Report_QA_SDGs/Dev set`
- `./Results/Reports/JSON_Report_Summary_SDGs/Dev set`
- `./Results/Reports/Markdown_Report_Summary_SDGs/Dev set`

&nbsp;

**Notebook:** `6.1-Reports Postprocessing.ipynb`

**Description:**
Final post-processing step that ensures consistent and unique citation numbering across all generated reports.

**Input paths:**
- `./Results/Reports/JSON_Report_QA_SDGs/Dev set`
- `./Results/Reports/JSON_Report_Summary_SDGs/Dev set`
- `./Results/Reports/JSON_Report_QA/Dev set`
- `./Results/Reports/JSON_Report_Summary/Dev set`

**Output paths:**
- Same as inputs, each with `_updated_citations` appended

---

## Citation

If you use this code or framework in your research, please cite our paper:
```bibtex
[Add your citation here]
```

## License

[Add your license information here]

## Contact

[Add contact information here]
