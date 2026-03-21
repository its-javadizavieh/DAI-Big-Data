# Big Data — Lab & Esercitazioni

Materiali didattici per il corso **"Big Data"** — ITS ICT Piemonte, Tecnico Superiore Data Analyst & AI Specialist B.F. 2025/2027.

## 📚 About This Repository

This repository contains the hands-on lab exercises, solutions, and datasets for the course. Labs guide students through real-world Big Data engineering practices using PySpark, Spark SQL, MongoDB, and end-to-end data pipelines.

Each lab includes:

- goal + scenario
- step-by-step instructions with code snippets
- expected output + verification checklist
- collapsible solutions (in `lab_solutions/`)

## 📂 Labs

Hands-on laboratory exercises (Italian) covering:

### Semester 1 — Big Data Foundations & PySpark

| Lab                                                | Topic                                            |
| -------------------------------------------------- | ------------------------------------------------ |
| [00](labs/00_datasets_description.md)              | Descrizione dei dataset                          |
| [01](labs/01_intro_big_data_5v.md)                 | Introduzione ai Big Data e le 5V                 |
| [02](labs/02_distributed_systems_cluster.md)       | Sistemi distribuiti: cluster, nodi, parallelismo |
| [03](labs/03_partitioning_replication_ft.md)       | Partizionamento, replicazione, fault tolerance   |
| [04](labs/04_hadoop_ecosystem_scalability.md)      | Da Hadoop a Spark: perché usiamo Python          |
| [05](labs/05_setup_python_jupyter_pyspark.md)      | Setup ambiente: Python + Jupyter + PySpark       |
| [06](labs/06_pyspark_dataframe_transformations.md) | PySpark DataFrame API: trasformazioni            |
| [07](labs/07_pyspark_actions_lazy_eval.md)         | PySpark azioni e lazy evaluation                 |
| [08](labs/08_pyspark_joins_aggregations.md)        | PySpark: join e aggregazioni                     |
| [09](labs/09_spark_sql_queries_temp_views.md)      | Spark SQL: query e viste temporanee              |
| [10](labs/10_file_formats_csv_json_parquet.md)     | Formati file: CSV, JSON, Parquet                 |
| [11](labs/11_spark_streaming_intro.md)             | Introduzione a Spark Streaming                   |
| [12](labs/12_exploratory_analysis.md)              | Analisi esplorativa: Serie A e Coppa Italia      |
| [13](labs/13_batch_pipeline_practice.md)           | Batch Pipeline Practice: CSV → PySpark → Parquet |
| [14](labs/14_midterm_exam.md)                      | Verifica intermedia (Midterm)                    |

### Semester 2 — NoSQL, MongoDB & Pipeline

| Lab                                              | Topic                                          |
| ------------------------------------------------ | ---------------------------------------------- |
| [15](labs/15_nosql_families_sql_comparison.md)   | Famiglie NoSQL e confronto con SQL             |
| [16](labs/16_mongodb_installation_crud.md)       | MongoDB: installazione e operazioni CRUD       |
| [17](labs/17_data_ingestion_pipeline.md)         | Pipeline di ingestion: CSV → PySpark → MongoDB |
| [18](labs/18_data_cleaning_batch.md)             | Pulizia dati e trasformazioni batch            |
| [19](labs/19_advanced_aggregations_spark_sql.md) | Aggregazioni avanzate e Spark SQL              |
| [20](labs/20_mongodb_modeling_indexes.md)        | MongoDB: modellazione documenti e indici       |
| [21](labs/21_sharding_performance.md)            | Sharding e ottimizzazione performance          |
| [22](labs/22_replication_cap_theorem.md)         | Replicazione, consistenza e CAP theorem        |
| [23](labs/23_pipeline_end_to_end.md)             | Pipeline end-to-end: CSV → PySpark → MongoDB   |
| [24](labs/24_mini_project_design.md)             | Mini-progetto: progettazione e avvio           |
| [25](labs/25_mini_project_development.md)        | Mini-progetto: sviluppo e completamento        |
| [26](labs/26_final_exam.md)                      | Verifica finale                                |
| [27](labs/27_final_review.md)                    | Correzione verifica finale e chiusura corso    |

## 📝 Solutions

Solutions are provided for labs 01–13 and 15–25 in the [`lab_solutions/`](lab_solutions/) folder.

| Solution                                                | Topic                                            |
| ------------------------------------------------------- | ------------------------------------------------ |
| [01](lab_solutions/sol_01_intro_big_data_5v.md)         | Introduzione ai Big Data e le 5V                 |
| [02](lab_solutions/sol_02_distributed_systems.md)       | Sistemi distribuiti: cluster, nodi, parallelismo |
| [03](lab_solutions/sol_03_partitioning_replication.md)  | Partizionamento, replicazione, fault tolerance   |
| [04](lab_solutions/sol_04_hadoop_spark.md)              | Da Hadoop a Spark                                |
| [05](lab_solutions/sol_05_setup_environment.md)         | Setup ambiente                                   |
| [06](lab_solutions/sol_06_dataframe_transformations.md) | DataFrame API: trasformazioni                    |
| [07](lab_solutions/sol_07_actions_lazy_eval.md)         | Azioni e lazy evaluation                         |
| [08](lab_solutions/sol_08_joins_aggregations.md)        | Join e aggregazioni                              |
| [09](lab_solutions/sol_09_spark_sql.md)                 | Spark SQL                                        |
| [10](lab_solutions/sol_10_file_formats.md)              | Formati file                                     |
| [11](lab_solutions/sol_11_spark_streaming.md)           | Spark Streaming                                  |
| [12](lab_solutions/sol_12_exploratory_analysis.md)      | Analisi esplorativa                              |
| [13](lab_solutions/sol_13_batch_pipeline.md)            | Batch Pipeline                                   |
| [15](lab_solutions/sol_15_nosql_families.md)            | Famiglie NoSQL                                   |
| [16](lab_solutions/sol_16_mongodb_crud.md)              | MongoDB CRUD                                     |
| [17](lab_solutions/sol_17_ingestion_pipeline.md)        | Pipeline di ingestion                            |
| [18](lab_solutions/sol_18_data_cleaning.md)             | Pulizia dati                                     |
| [19](lab_solutions/sol_19_advanced_aggregations.md)     | Aggregazioni avanzate                            |
| [20](lab_solutions/sol_20_mongodb_modeling.md)          | MongoDB: modellazione e indici                   |
| [21](lab_solutions/sol_21_sharding_performance.md)      | Sharding e performance                           |
| [22](lab_solutions/sol_22_replication_cap.md)           | Replicazione e CAP theorem                       |
| [23](lab_solutions/sol_23_pipeline_end_to_end.md)       | Pipeline end-to-end                              |
| [24](lab_solutions/sol_24_mini_project_design.md)       | Mini-progetto: progettazione                     |
| [25](lab_solutions/sol_25_mini_project_development.md)  | Mini-progetto: sviluppo                          |

## 📊 Datasets

Two real-world datasets are included in `labs/`:

| File                                 | Description                                                   |
| ------------------------------------ | ------------------------------------------------------------- |
| `serie_a_coppa_italia_2015_2023.csv` | Italian football — Serie A & Coppa Italia matches (2015–2023) |
| `superstore_sales.csv`               | E-commerce sales data (Superstore)                            |

> Students must choose **one** dataset before Lab 01 and use it throughout the course.

## 👨‍💻 Author

**Seyed Hossein Javadizavieh**

📧 <seyedhossein.javadizavieh@its-ictpiemonte.it>

🔗 [LinkedIn Profile](https://www.linkedin.com/in/seyedhosseinjavadizavieh)

## 🎓 Course Information

- **Institution**: ITS ICT Piemonte
- **Course**: Big Data
- **Programme**: Tecnico Superiore Data Analyst & AI Specialist B.F. 2025/2027
- **Duration**: 76h (27 lectures), 2 semesters
- **Language**: Labs in Italian

## 📄 License

Educational materials for ITS ICT Piemonte.

---

_Last Updated: March 2026_
