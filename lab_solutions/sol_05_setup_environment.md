# Soluzioni Lab 05 — Setup ambiente: Python + Jupyter + PySpark

## Verifica installazione

Apri il terminale in VS Code (Ctrl+`) ed esegui:

```bash
python -m venv bigdata_env
bigdata_env\Scripts\activate      # su Windows
pip install pyspark pandas matplotlib seaborn numpy
```

Poi crea un nuovo file `lab_05_setup.ipynb` in VS Code e seleziona il kernel dal virtual environment.

## Cella 1: Java (Windows)

```python
import os
import platform
import subprocess
from pathlib import Path

def setup_java_home():
    if os.environ.get("JAVA_HOME"):
        java_bin = Path(os.environ["JAVA_HOME"]) / "bin"
    elif platform.system() != "Windows":
        return
    else:
        java_home = _find_jdk_on_windows()
        if not java_home:
            print("JAVA_HOME non trovato. Installa JDK 11+ o imposta JAVA_HOME.")
            return
        os.environ["JAVA_HOME"] = java_home
        java_bin = Path(java_home) / "bin"
    bin_path = str(java_bin)
    path = os.environ.get("PATH", "")
    if bin_path.casefold() not in path.casefold():
        os.environ["PATH"] = bin_path + os.pathsep + path
    print(f"JAVA_HOME = {os.environ['JAVA_HOME']}")

def _find_jdk_on_windows():
    try:
        out = subprocess.check_output(["where", "java"], text=True, stderr=subprocess.DEVNULL)
        java_exe = Path(out.strip().splitlines()[0]).resolve()
        if java_exe.name.lower() == "java.exe" and java_exe.parent.name.lower() == "bin":
            return str(java_exe.parent.parent)
    except (subprocess.CalledProcessError, FileNotFoundError, IndexError):
        pass
    for root in (
        Path(r"C:\Program Files\Java"),
        Path(r"C:\Program Files\Eclipse Adoptium"),
        Path(r"C:\Program Files\Microsoft"),
        Path(r"C:\Program Files\Amazon Corretto"),
    ):
        if not root.is_dir():
            continue
        for folder in sorted(root.iterdir(), reverse=True):
            if (folder / "bin" / "java.exe").exists():
                return str(folder)
    return None

setup_java_home()
```

## Cella 2: SparkSession

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("Lab05") \
    .master("local[*]") \
    .getOrCreate()

print(f"Spark version: {spark.version}")
# Output atteso: 3.x.x
```

## Cella 3: DataFrame da lista Python

```python
data = [
    (1, "Office Supplies", "Binders", 14.62),
    (2, "Technology", "Phones", 907.15),
    (3, "Furniture", "Chairs", 498.00),
    (4, "Office Supplies", "Paper", 15.55),
    (5, "Technology", "Accessories", 127.98),
]

df = spark.createDataFrame(data, ["Row_ID", "Category", "Sub_Category", "Sales"])
df.show()
df.printSchema()
```

**Output atteso:**

```
+------+---------------+------------+------+
|Row_ID|       Category|Sub_Category| Sales|
+------+---------------+------------+------+
|     1|Office Supplies|     Binders| 14.62|
|     2|     Technology|      Phones|907.15|
|     3|      Furniture|      Chairs| 498.0|
|     4|Office Supplies|       Paper| 15.55|
|     5|     Technology| Accessories|127.98|
+------+---------------+------------+------+
```

## Cella 4: Leggi dataset del corso

```python
df_csv = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)
df_csv.show(5)
df_csv.printSchema()
```

## Cella 5: Conta righe

```python
print(f"Numero righe: {df_csv.count()}")
# Output atteso: 9800
```

## Cella 6: Chiudi Spark

```python
spark.stop()
print("SparkSession chiusa.")
```
