# Aspect-Based Sentiment Analysis (ABSA) on Tokopedia Employee Reviews

This project aims to perform Aspect-Based Sentiment Analysis (ABSA) on employee reviews. The analysis is designed to identify key aspects related to job satisfaction based on Herzberg's theory, measure the sentiment towards these aspects, and conduct a comparative analysis.

The primary methodology in this analysis is the calculation of a **Net-Score** (the proportion of positive sentiment minus the proportion of negative sentiment) for each aspect. The change **(Δ) in Net-Score** is analyzed between two time periods (before and after January 31, 2024) to measure the impact of events or policy changes. The results are visualized in a **Priority Grid** to provide actionable strategic recommendations.

## 🚀 Key Features

  * **Data Pre-processing**: Cleans and prepares raw review data for analysis.
  * **Automated Aspect Extraction & Classification**: Uses TF-IDF and a *Sentence Transformer* model to identify relevant keywords and classify them into 13 aspects of Herzberg's theory.
  * **Advanced Sentiment Analysis**: Implements an ABSA model from Hugging Face (`yangheng/deberta-v3-base-absa-v1.1`) to determine the sentiment (positive, negative, neutral) for each identified aspect.
  * **Net-Score Based Comparative Analysis**: Compares sentiment changes using the **Δ Net-Score** metric before and after January 31, 2024.
  * **Statistical Significance Testing**: Utilizes a *two-proportion z-test* to validate whether the change in the proportion of positive sentiment is statistically significant.
  * **Strategic Visualization**: Generates a Δ Net-Score bar chart and a **Priority Grid** that maps changes against aspect categories (Motivator vs. Hygiene) to determine priority areas (Fix, Nurture, Maintain, Monitor).
  * **Report Table Generation**: Produces specific tables (such as Table 5.3 and 5.4) ready for use in research reports.

## 📂 Directory Structure

```
.
├── 📄 raw_data/
│   └── export_1750496855742(tokped_review).csv  # Raw input file
│
├── 📓 1_data_pre-processing.ipynb
├── 📓 2_aspect_lexicon.ipynb
├── 📓 3_sentiment_analysis.ipynb
│
├── 📊 analysis_results/
│   ├── tokopedia_reviews_date_cleaned.csv  # Output from script 1
│   ├── aspect_lexicon_v5.csv                # Output from script 2
│   ├── absa_batch_processing_results.csv    # Raw sentiment analysis results
│   ├── aggregation_results.csv              # Aggregated data per period
│   ├── calculation_results.csv              # Net-Score and z-test calculation results
│   ├── table_5.3.csv                        # Δ Positive Proportion Table
│   ├── table_5.4.csv                        # Significance Test Table
│   ├── delta_bar.png                        # Net-Score change bar chart
│   └── priority_grid.png                    # Priority Grid chart
│
└── README.md                                # (you are here)
```

## ⚙️ Installation

Ensure you have Python 3.8+ installed. Then, install all required libraries by running the following command:

```bash
pip install pandas scikit-learn spacy "sentence-transformers>=2.2.0" torch transformers nltk matplotlib scipy statsmodels adjustText
```

Next, download the English language model for spaCy:

```bash
python -m spacy download en_core_web_sm
```

## 💡 Workflow & How to Run

This project consists of three main notebooks that must be executed sequentially.

### Step 1: Data Pre-processing

Run the `1_data_pre-processing.ipynb` notebook to clean the raw data.

  * **Input**: `raw_data/export_1750496855742(tokped_review).csv`
  * **Output**: `analysis_results/tokopedia_reviews_date_cleaned.csv`

### Step 2: Creating the Aspect Lexicon

Run the `2_aspect_lexicon.ipynb` notebook to generate a list of keywords classified into Herzberg's aspects.

  * **Input**: A cleaned reviews file (e.g., `shopee_cleaned_reviews.csv`).
  * **Output**: `analysis_results/classification_results_with_aspect_description.csv` (can be renamed to `aspect_lexicon_v5.csv` for the next step).

### Step 3: Sentiment Analysis & Result Interpretation

Run the `3_sentiment_analysis.ipynb` notebook to perform the main analysis. This notebook is divided into several parts:

  * **Input**:
      * `analysis_results/tokopedia_reviews_date_cleaned.csv`
      * `analysis_results/aspect_lexicon_v5.csv`
  * **Process & Output**:
    1.  **Part A (Sentiment Labeling)**: Performs aspect-based sentiment labeling on all reviews. The output is `absa_batch_processing_results.csv`.
    2.  **Part B (Analysis)**: Performs aggregation, statistical calculations, and visualization based on the labeling results.
          * Generates `aggregation_results.csv` and `calculation_results.csv`.
          * Generates the report tables `table_5.3.csv` and `table_5.4.csv`.
          * Displays the **Δ Net-Score Bar Chart** and **Priority Grid** visualizations.

-----

## 📜 Script Details

### 1\. `data_pre-processing.ipynb`

  * **Purpose**: To clean the raw CSV file of irrelevant data, duplicates, and inconsistent formats to prepare it for analysis.
  * **Main Input**: `export_1750496855742(tokped_review).csv`.
  * **Main Output**: `tokopedia_reviews_date_cleaned.csv`.
  * **Key Processes**: Column selection, text cleaning, duplicate removal, and date format standardization.

### 2\. `aspect_lexicon.ipynb`

  * **Purpose**: To build a lexicon that maps keywords (including their synonyms) from the reviews to the 13 job satisfaction aspects according to Herzberg's theory.
  * **Main Input**: A cleaned reviews file (e.g., `shopee_cleaned_reviews.csv`).
  * **Main Output**: `classification_results_with_aspect_description.csv`.
  * **Key Processes**:
      * **Extraction**: Uses `TfidfVectorizer` to find significant terms.
      * **Filtering**: Uses `spaCy` to filter terms based on their part of speech (e.g., only nouns).
      * **Semantic Classification**: Uses a `SentenceTransformer` model to classify each term into the Herzberg aspect with the highest semantic similarity.

### 3\. `sentiment_analysis.ipynb`

  * **Purpose**: To analyze the sentiment for each aspect, calculate the change in **Net-Score** between periods, and visualize it in a **Priority Grid** for strategic recommendations.
  * **Main Input**: `tokopedia_reviews_date_cleaned.csv` and `aspect_lexicon_v5.csv`.
  * **Main Output**: `absa_batch_processing_results.csv`, `aggregation_results.csv`, `calculation_results.csv`, `table_5.3.csv`, `table_5.4.csv`, and plot visualizations.
  * **Key Processes**:
    1.  **Sentiment Labeling (ABSA)**: Uses the `yangheng/deberta-v3-base-absa-v1.1` model to assign sentiment labels (positive, negative, neutral) to each aspect detected in the reviews.
    2.  **Temporal Analysis**: Divides the data into two periods ("before" and "after") based on the date **January 31, 2024**.
    3.  **Metric Calculation**: Calculates the **Net-Score** (`(Positive - Negative) / Total`) for each aspect in both periods, then computes the change (**Δ Net-Score**).
    4.  **Statistical Test**: Performs a *two-proportion z-test* on the proportion of positive sentiments to test the significance of the change (`p-value`).
    5.  **Visualization**:
          * Creates a **bar chart** to show the Δ Net-Score per aspect.
          * Creates a **Priority Grid** that maps the Δ Net-Score (X-axis) against the Herzberg Category (Motivator/Hygiene, Y-axis) to identify priority areas.
