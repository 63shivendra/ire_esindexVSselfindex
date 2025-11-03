# 🌐 **Information Retrieval: Indexing and Retrieval System**

***Assignment Project Report — Shivendra Pratap Singh (Roll No: 2024202022)***
📅 *November 3, 2025*

---

## 🎯 **Executive Summary**

This project presents **SelfIndex**, a custom-built search indexing and retrieval system developed from scratch and benchmarked against **Elasticsearch**.
It explores **Boolean**, **TF**, and **TF-IDF** indexing models, multiple **datastores (JSON, SQLite)**, **compression techniques (Elias-Fano, Zlib)**, and **query modes (TAAT, DAAT)**.
Key findings reveal that the system achieves **competitive performance** with Elasticsearch, demonstrating efficiency and deep insight into search engine internals.

---

## 🧭 **Objectives**

* 🔹 Build a **custom search indexing system** with modular configurations
* 🔹 Compare **indexing strategies, datastores, compression methods**
* 🔹 Evaluate **query modes** — Term-at-a-Time (TAAT) vs Document-at-a-Time (DAAT)
* 🔹 Benchmark against **Elasticsearch** under multiple cache conditions
* 🔹 Measure artifacts: **latency (P95/P99)**, **throughput (QPS)**, **memory footprint**

---

## 📂 **Dataset Overview**

| Source           | Description                                      | Count |
| ---------------- | ------------------------------------------------ | ----- |
| 🧠 Wikipedia     | 50,000 articles from HuggingFace (`20231101.en`) | 50K   |
| 🗞️ Webz.io News | 50,000 real-world articles                       | 50K   |
| 📊 Total         | 100,000 documents (~15M tokens)                  | 100K  |

**Rationale:** A mixed corpus ensures language diversity — formal text from Wikipedia + dynamic topics from news data.

---

## ⚙️ **Data Preprocessing**

Implemented with **NLTK** for linguistic normalization.

**Pipeline Steps**

1. ✂️ Tokenization
2. 🔡 Lowercasing
3. 🚫 Stopword Removal
4. 🌱 Stemming (Porter Algorithm)
5. 🧹 Special Character Cleanup

✅ **Zipf’s Law Validation** confirmed natural term frequency distribution, ensuring corpus representativeness.
✅ **Vocabulary reduced by 48.6%**, improving lookup speed and index compactness.

---

## 🧩 **Implementation Details**

### 🗂️ Index Naming Convention

```
SelfIndex_i{x}d{y}c{z}o{optim}
```

| Parameter | Options | Meaning                  |
| --------- | ------- | ------------------------ |
| i{x}      | 1, 2, 3 | Boolean / TF / TF-IDF    |
| d{y}      | 1, 2    | JSON / SQLite            |
| c{z}      | 1, 2, 3 | None / Elias-Fano / Zlib |
| o{optim}  | 0, sp   | None / Skip Pointers     |

---

## 📚 **Index Types**

### 🟩 **Boolean Index (x=1)**

* Logical retrieval (`AND`, `OR`, `NOT`, `PHRASE`)
* ⚡ Fast average latency, but poor tail latency (Boolean Paradox)

### 🟨 **TF Index (x=2)**

* Stores **term frequency per document**
* Enables ranking → better consistency and early termination

### 🟥 **TF-IDF Index (x=3)**

* Adds **inverse document frequency weighting**
* 📈 Produces most relevant, ranked results

---

## 💾 **Datastores**

### 🧱 **Custom JSON Store (y=1)**

* In-memory, fast access
* +15.5% faster than SQLite
* Ideal for read-heavy workloads

### 🗃️ **SQLite Store (y=2)**

* Disk-based, ACID-compliant
* Efficient for large indices or write-heavy scenarios

---

## 🧨 **Compression Techniques**

| Method        | Space Saved       | Latency Impact    |
| ------------- | ----------------- | ----------------- |
| ❌ None        | Baseline (651 MB) | ⚡ Fastest         |
| 🧩 Zlib       | 59.6% smaller     | ⏱️ +172% latency  |
| 📦 Elias-Fano | 74.8% smaller     | 🐢 +1160% latency |

**Recommendation:**

* Real-time: None
* Balanced workloads: Zlib
* Archival: Elias-Fano

---

## 🚀 **Optimization: Skip Pointers (i=sp)**

* Adds skip pointers every √n postings
* 🧠 Faster Boolean intersections (3% latency improvement)
* Negligible storage overhead (+0.8%)
* Significant gains at larger scales (disk-based systems)

---

## 🔄 **Query Processing Modes**

| Mode        | Description                                | P95 Latency | QPS |
| ----------- | ------------------------------------------ | ----------- | --- |
| ⚙️ **TAAT** | Term-at-a-Time — sequential term scoring   | 9.47ms      | 275 |
| 🧮 **DAAT** | Document-at-a-Time — multi-pointer scoring | 16.12ms     | 157 |

🟢 **TAAT is 70% faster in Python** due to better cache locality and lower overhead.

---

## ⚖️ **Elasticsearch vs SelfIndex**

| System                  | Cache Mode | P95 (ms) | QPS     |
| ----------------------- | ---------- | -------- | ------- |
| 🟢 SelfIndex (TAAT)     | In-memory  | **9.47** | **275** |
| 🔵 Elasticsearch (WARM) | Full cache | 10.22    | 220     |
| 🟡 Elasticsearch (COLD) | Disk fetch | 12.60    | 98      |

✅ **SelfIndex outperforms ES by 7% latency and 25% throughput under warm conditions!**

---

## 🧠 **Key Insights**

### 🔍 **Boolean Retrieval Paradox**

* Highest throughput (344 QPS)
* Worst tail latency (P95 = 11.13ms)
  ➡️ Great for batch processing, not for user-facing search.

### 💾 **Compression Trade-offs**

* Zlib = best balance between space and speed
* Elias-Fano = heavy CPU overhead

### 🧩 **Datastore Decision**

* JSON faster for in-memory evaluation
* SQLite ideal for scalability beyond RAM

### ⚙️ **TAAT vs DAAT**

* TAAT wins in Python
* DAAT preferred in compiled systems (C++/Java)

### 🪜 **Skip Pointers**

* +3% speedup now
* Potential +100% in disk-based or multi-term systems

---

## 🧱 **Design Decisions**

✅ Implemented:

* 3 Index Types (Boolean, TF, TF-IDF)
* 2 Datastores (JSON, SQLite)
* 2 Compression Methods (Zlib, Elias-Fano)
* 2 Query Modes (TAAT, DAAT)
* Skip Pointers Optimization
* Elasticsearch Benchmark (COLD/WARM/MIXED)

🚫 Not Implemented (by design):

* Distributed Indexing
* Query Expansion or Synonyms
* BM25 or PageRank Ranking Models

---

## 🏁 **Conclusion**

This project demonstrates a **full-fledged, modular information retrieval system** that rivals Elasticsearch in controlled environments.

✨ **Highlights:**

* TAAT mode outperformed Elasticsearch (WARM) by 7%
* Compression & datastore studies reveal critical trade-offs
* 256 diverse queries ensured robustness
* The system achieved **real-world scale and interpretability**

> **“From indexing to insights — SelfIndex bridges theory and real-world search performance.”**

---

📘 **Repository & Resources**

* 💻 **GitHub:** [63shivendra/ire_esindexVSselfindex](https://github.com/63shivendra/ire_esindexVSselfindex)
* 📦 **Pre-built Indices:** [Google Drive Link](https://drive.google.com/drive/folders/15bKzPq0s4G_gNo3Ecq_n-83LIv3UJ8tK?usp=sharing)
* 📄 **Complete Codebase:** [Google Drive Link](https://drive.google.com/drive/folders/1grfj_vlyhXRH7P9xn_qL0kYZC4fUp8HW?usp=sharing)

---

🧑‍💻 **Author:** *Shivendra Pratap Singh*
🎓 *Information Retrieval Assignment — 2025*
