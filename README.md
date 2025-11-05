# 📚 Information Retrieval Pipeline (Elasticsearch + Python)

This repository contains the **technical implementation** for an Elasticsearch-based Information Retrieval pipeline used in the **IR2025 coursework**. It provides a reproducible environment for document indexing, query execution, and evaluation using the `trec_eval` tool.

## ⚙️ System Requirements

| Component | Minimum Version | Recommended | 
 | ----- | ----- | ----- | 
| Python | 3.10 | 3.12 | 
| Elasticsearch | 9.1.4 | 9.x | 
| OS | Windows 10 / 11 | macOS, Linux | 
| RAM | 4 GB | 8 GB+ | 
| CPU | 2 cores | 4+ cores | 

## 🚀 Getting Started: Setup Instructions

### 1️⃣ Clone the Repository
```bash
git clone [Semantic Information Retrieval System](https://github.com/Swkratis210204/Semantic-Information-Retrieval-System-ElasticSearch-FAISS-Transformers-)
cd IR2025-Pipeline
```

### 2️⃣ Create and Activate a Virtual Environment

**Windows (PowerShell/Command Prompt):**

```bash
python -m venv venv
.\venv\Scripts\activate
```

**macOS / Linux (Bash/Zsh):**
```bash
python3 -m venv venv
source venv/bin/activate
```

### 3️⃣ Install Python Dependencies


```bash
pip install -r requirements.txt
```

If you encounter dependency issues, consider upgrading your tools:


```bash
python -m pip install --upgrade pip setuptools wheel
```

## 4️⃣ Install & Run Elasticsearch (v9.1.4)

### 🪟 Windows Setup

1. **Download:** Get the ZIP file from: git clone https://github.com/Swkratis210204/Semantic-Information-Retrieval-System-ElasticSearch-FAISS-Transformers-

2. **Extract:** Extract the ZIP to a folder like this:


```bash
C:\elasticsearch-9.1.4
```

3. **Start Server:** Open **PowerShell (Admin)** and run the following commands:


```bash
cd C:\elasticsearch-9.1.4\bin
.\elasticsearch.bat
```

### 🐧 macOS / Linux Setup

1. **Unpack the Archive:** Use the `tar` command to extract the downloaded compressed file (`.tar.gz`).

| Command | Description | 
 | ----- | ----- | 
| `tar` | The tape archive utility. | 
| `-x` | Extract files. | 
| `-z` | Decompress using gzip. | 
| `-f` | Specify the archive filename. | 


```bash
tar -xzf elasticsearch-9.1.4-linux-x86_64.tar.gz
```

2. **Start Server:** Navigate and execute the startup script:


```bash
cd elasticsearch-9.1.4/bin
./elasticsearch
```

### 🔔 Verification (Both OS)

Wait for the log output to confirm successful startup:

> Started Elasticsearch in  
> $$xxx$$  
> seconds

Then, verify the server is accessible at: [http://127.0.0.1:9200](http://127.0.0.1:9200)

## 🏃 Running the Pipeline

Once Elasticsearch is running and your Python environment is active, you can execute the pipeline scripts.

**Note:** Ensure you have placed your data files (e.g., documents, queris, qrels, results) in the designated directory (e.g., `./IR2025/`).

