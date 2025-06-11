# Comparison and Analysis of Value Linking in Text-to-SQL Systems

**Experiment, Analysis & Benchmark**

---

## Overview

This repository contains the code and resources associated with the paper **"Comparison and Analysis of Value Linking in Text-to-SQL Systems"**. Here, you will find everything needed to reproduce the experiments, analysis, and benchmarks presented in the paper.

---
## Repo-setup
Clone the repo and download the dev set of BIRD :

    git clone https://github.com/apostolhskouk/experimental-analysis-of-value-inking.git
    cd experimental-analysis-of-value-inking/
    wget https://bird-bench.oss-cn-beijing.aliyuncs.com/dev.zip
    unzip dev.zip
    rm dev.zip
    cd dev_20240627/
    unzip dev_databases.zip
    rm dev_databases.zip 
    cd ..

Download the pre-computed indexes for the BIRD dataset and configure the folder: 

    cd assets 
    wget https://huggingface.co/datasets/ApostolosK/value_linking_indexes/resolve/main/indexes.zip
    unzip indexes.zip
    rm indexes.zip 
    cd ..
    
The experiment scripts will locate and use the indexes. If you skip this step, the scripts will first create the indexes before initiating the query process, which may take some time.

## Environment Setup

### 1. Create a Conda Environment

Create and activate a new Conda environment with Python 3.10:

    conda create -n value_linking python=3.10 -y
    conda activate value_linking
    
### 2. Install JDK and Maven Dependencies

Install the required JDK and Maven dependencies using Conda:

    conda install -c conda-forge openjdk=21 maven -y

### 3. Install Python Dependencies

Install the necessary Python packages by running:

    pip install -r requirements.txt
    python -m spacy download en_core_web_sm


---

## Dataset Preparation

 can safely ignore this step since the dataset already exist in the assets folder. However you can try creating the datasets on tour own by executing:

    python create_value_linking_dataset.py

The assets folder also contains JSON files with golden value links, both with and without injected false positives, named accordingly. It also includes files with predicted value links for CodeS and CHESS. These files are used for text-to-SQL experiments.

---
## Value Reference Detection with LLM

In order to run value reference detection locally with an LLM, you would typically need to configure Ollama. However, this repository already includes a precomputed file (stored in the `assets` directory) containing value references for each NLQ, generated using **Llama:3.1 70B**. Consequently, you do **not** need to perform any LLM inference when running the experiments. A dedicated class has been implemented and is utilized by the scripts to automatically handle the stored value references. If you still wish to run the LLM detection on-the-fly, update your Ollama configuration as in `LLMExtractor` and replace the `DictionaryExtractor` with `LLMExtractor` in the code. This will enable the real-time generation of value references through your local LLM setup.


## Running the Experiments


This repository includes several scripts to run the experiments described in the paper. Use the following commands to execute each experiment:

- **Baseline Experiments**  
      
      python run_baselines.py
  
- **Indexes Experiments**  
      
      python run_indexes_experiments.py
  
- **Value Reference Detection Experiments**  
      
      python run_value_reference_experiments.py
  
- **Filtering Experiments**  
      
      python run_filtering_experiments.py
  
- **Text-to-SQL experiments**  
      
      python run_text_to_sql.py 
---

## Additional Information

- Each experiment script generates console logs and a log file in the logs folder, recording key metrics for each run.
- Each script is annotated with comments explaining its function and how it contributes to the overall analysis.
- For detailed explanations of the experiments, please refer to the paper and the inline comments within the code.

# How to Create a New Benchmark

### 1. Initial Setup

First, you need to clone the repository and download the necessary datasets.

```bash
# 1. Clone the repository
git clone https://github.com/apostolhskouk/experimental-analysis-of-value-inking.git

# 2. Navigate into the project directory
cd experimental-analysis-of-value-inking

# 3. Download the databases used for value variations
huggingface-cli download ApostolosK/value_linking_databases --repo-type dataset --local-dir assets/all_databases
```

### 2. Selecting Values for the Benchmark

The next step is to manually select the values that will form the basis of your new benchmark.

1.  **Navigate to the Source Data**
    Go to the `assets/data_exploration` directory. This folder contains JSON files filled with potential values.

2.  **Understand the JSON Structure**
    Open any JSON file. The first few records list the distinct **table-column** pairs available in that file. This is a helpful index for finding specific data using a search (`Ctrl+F`).

3.  **Select and Copy Values**
    Your goal is to select **30-50 representative values** to create the benchmark. Aim to include values from as many different columns as possible. Copy the desired value entries from the JSON file.

4.  **Create Your "Human-Selected" File**
    - Create a new, corresponding JSON file inside the `assets/data_exploration_human` directory.
    - For example, if you are working with `some_data.json`, you could name your new file `some_data_human_selection.json`.
    - Paste the values you copied in the previous step into this new file and save it.

### 3. Generating the Final Benchmark File

After creating your file with the selected values, you need to run a script to process it into the final benchmark format.

1.  **Configure the Script**
    Open the `alter_execute_verify.py` script in a text editor. You must manually change two variables:

    - **`variations_json_path`**: Update this path to point to the new JSON file you just created in `assets/data_exploration_human`.
      ```python
      # Example:
      variations_json_path = 'assets/data_exploration_human/some_data_human_selection.json'
      ```

    - **`output_json_path`**: Set this to the desired output path for your final benchmark. It should be placed in `assets/all_benchmarks_human`.
      ```python
      # Example:
      output_json_path = 'assets/all_benchmarks_human/my_new_benchmark.json'
      ```

2.  **Run the Script**
    Execute the script from your terminal:
    ```bash
    python alter_execute_verify.py
    ```

Once the script finishes, your new benchmark will be available at the output path you specified.

---
