# IR2025 – Αναφορά Φάσης 1  
### Baseline Retrieval με χρήση Elasticsearch (BM25)

**Μάθημα:** Information Retrieval 2025  
**Φοιτητές(A.M.):** Περικλής Παύλου(3220158), Σωκράτης-Βησσαρίων Γιαννούτσος(3220028)  
**Καθηγήτρια:** Αντωνία Κυριακοπούλου
**Ημερομηνία Υποβολής:** 3 Δεκεμβρίου 2025  

---
## Πληροφορίες για την εργασία

Η εργασία αυτή αποτελεί την **Πρώτη Φάση (Baseline – Κλασική Ανάκτηση)** του μαθήματος *Συστήματα Ανάκτησης Πληροφοριών (IR2025)*.  
Στόχος της είναι η κατανόηση και υλοποίηση ενός συστήματος ανάκτησης πληροφορίας βασισμένου σε **κλασικά μοντέλα ανάκτησης**, όπως το πιθανοτικό μοντέλο **BM25**, μέσω της μηχανής αναζήτησης **Elasticsearch**.

Η συλλογή δεδομένων που χρησιμοποιείται είναι η **IR2025**, η οποία περιλαμβάνει **18.316 κείμενα** που βρίσκονται στο αρχείο **`documents.csv`**,  
ένα σύνολο από **ερωτήματα αναζήτησης** στο αρχείο **`queries.csv`**  
και ένα αρχείο που καθορίζει τις **συναφείς απαντήσεις (relevant documents)** για κάθε ερώτημα στο **`qrels.txt`**. 
Σκοπός είναι η δημιουργία ενός ευρετηρίου στο Elasticsearch, η εκτέλεση των ερωτημάτων της συλλογής και η εξαγωγή των **top-k αποτελεσμάτων** (k = 20, 30, 50).  
Στη συνέχεια, τα αποτελέσματα αξιολογούνται με το εργαλείο **`trec_eval`** χρησιμοποιώντας τα μέτρα **Precision@k** και **Mean Average Precision (MAP)**.

Η εργασία στοχεύει στην εξοικείωση με τη διαδικασία της **ευρετηρίασης, αναζήτησης και αξιολόγησης** ενός συστήματος ανάκτησης, ενώ παράλληλα λειτουργεί ως βάση για τις επόμενες φάσεις της σημασιολογικής και υβριδικής ανάκτησης που θα ακολουθήσουν.

---

## Διάγραμμα Ροής του Συστήματος (Framework)

To παρακάτω διάγραμμα παρουσιάζει το συνολικό **διάγραμμα ροής** του συστήματος ανάκτησης που υλοποιήθηκε γαι το πρώτο μέρος της εργασίας. Το διάγραμμα αποτυπώνει τα **σημαντικότερα στάδια** της διαδικασίας — από την εισαγωγή και ευρετηρίαση των εγγράφων έως την αξιολόγηση των αποτελεσμάτων με το εργαλείο `trec_eval` — παρουσιάζοντας συνοπτικά τη συνολική ροή του συστήματος με τρόπο πλήρη και κατανοητό.

<p align="center">
  <img src="framework.drawio.png" alt="IR2025 Framework Diagram" width="700"/>
</p>

*Εικόνα 1 – Διάγραμμα ροής του συστήματος ανάκτησης με Elasticsearch.*

### Περιγραφή βασικών τμημάτων του συστήματος

- **Documents**  
  Περιέχει τα έγγραφα της συλλογής `documents.csv`, τα οποία αποτελούν το σύνολο δεδομένων που θα ευρετηριαστεί.

- **Index Exists / Create Empty Index / Delete Existing Index**  
  Έλεγχος αν υπάρχει ήδη ευρετήριο στο Elasticsearch.  
  Αν υπάρχει, διαγράφεται και δημιουργείται νέο κενό ευρετήριο για καθαρή εισαγωγή.

- **Index Documents (BM25)**  
  Ευρετηρίαση όλων των εγγράφων με την βοήθεια του Elasticsearch client χρησιμοποιώντας το μοντέλο **BM25** για την εκτίμηση ομοιότητας μεταξύ ερωτήματος και κειμένων.

- **Elasticsearch Client**  
  Ο Elasticsearch client που επικοινωνεί με την υπηρεσία και διαχειρίζεται τις λειτουργίες ευρετηρίασης και αναζήτησης.

- **Run Queries**  
  Εκτέλεση των ερωτημάτων από το αρχείο `queries.csv` και παραγωγή αποτελεσμάτων για διαφορετικές τιμές k (20, 30, 50).

- **Results (results_20, results_30, results_50)**  
  Αποθήκευση των top-k εγγράφων για κάθε ερώτημα σε μορφή **TREC format**, ώστε να μπορούν να αξιολογηθούν από το εργαλείο `trec_eval`.

- **Qrels**  
  Αρχείο `qrels.txt` που περιέχει τις συσχετίσεις μεταξύ ερωτημάτων και συναφών εγγράφων (ground truth) και ένα αρχείο που καθορίζει τις σχετικές απαντήσεις (relevance judgments) για κάθε ερώτημα στο **`qrels.csv`**..

- **trec_eval**  
  Εργαλείο αξιολόγησης που λαμβάνει ως είσοδο τα αποτελέσματα και το αρχείο `qrels.txt`, υπολογίζοντας δείκτες **Precision@k** και **Mean Average Precision (MAP)**.

- **Evaluation Metrics (Precision@k, MAP)**  
  Παρουσίαση και ανάλυση των μετρικών αξιολόγησης που προκύπτουν από το `trec_eval`.

---
## 1️⃣ Προεπεξεργασία της Συλλογής IR2025

Η φάση προεπεξεργασίας στοχεύει στην οργάνωση των αρχείων εισόδου, στον καθαρισμό του κειμένου, και στη μετατροπή των εγγράφων σε κατάλληλη μορφή για ευρετηρίαση στο Elasticsearch. Τα παρακάτω βήματα βασίζονται αποκλειστικά στον κώδικα που υλοποιήθηκε στο project.

## 1.1 Ανάγνωση και Έλεγχος Αρχείων

Σε αυτό το βήμα ορίζονται όλες οι βασικές διαδρομές αρχείων (paths) της εργασίας ποθ θα μας χρησιμεύσουν σε δυναμική κλήση αρχείων σε μελλοντικά στάδια.

### Τι κάνει ο κώδικας

- Ορίζει τους βασικούς φακέλους:
  - **BASE_DIR**: ο φάκελος όπου εκτελείται το notebook.
  - **DATA_DIR**: ίδιος με το BASE_DIR και χρησιμοποιείται ως ο κεντρικός χώρος αποθήκευσης όλων των αρχείων.

- Ορίζει paths για όλα τα βασικά αρχεία του pipeline:
  - **documents.csv** → περιέχει τα αρχικά κείμενα της συλλογής.
  - **documents.jsonl** → το αρχείο που θα παραχθεί μετά την προεπεξεργασία για indexing.
  - **queries.csv** → το σύνολο των ερωτημάτων αξιολόγησης.
  - **qrels.csv** → τα αρχικά qrels στη μη επεξεργασμένη μορφή.
  - **qrels.txt** → τα qrels σε μορφή TREC (δημιουργούνται αυτόματα αν δεν υπάρχουν).

- Φορτώνει το `documents.csv` στην μεταβλητή `df` και πραγματοποιεί κρίσιμους ελέγχους:
  - Εμφανίζει πόσα έγγραφα βρέθηκαν.
  - Εμφανίζει τις στήλες του αρχείου.
  - Επιβεβαιώνει ότι υπάρχουν οι απαιτούμενες στήλες **ID** και **Text**.
    - Αν λείπουν, η διαδικασία σταματά άμεσα.

```python
# Define paths
BASE_DIR       = Path.cwd()
DATA_DIR       = BASE_DIR

CSV_PATH       = DATA_DIR / "documents.csv"
JSONL_PATH     = DATA_DIR / "documents.jsonl"
QUERIES_PATH   = DATA_DIR / "queries.csv"
QRELS_CSV_PATH = DATA_DIR / "qrels.csv"   
QRELS_TXT_PATH = DATA_DIR / "qrels.txt" 
```

## 1.2 Δομή του `documents.csv` και Έλεγχος Απαραίτητων Πεδίων
Σε αυτό το στάδιο το pipeline ελέγχει ότι το αρχείο **documents.csv** έχει τη σωστή δομή πριν συνεχιστεί η επεξεργασία.

### Τι ελέγχει ο κώδικας

- Το σύστημα απαιτεί **αυστηρά δύο βασικές στήλες**:
  - `ID` — μοναδικό αναγνωριστικό εγγράφου
  - `Text` — το περιεχόμενο του εγγράφου

- Ο κώδικας εντοπίζει αν κάποια από αυτές λείπει:
  - Αν λείπει έστω και μία από τις απαιτούμενες στήλες, η εκτέλεση **σταματάει άμεσα** με μήνυμα λάθους.
  - Αυτό προστατεύει το pipeline από indexing σε λάθος μορφοποιημένα δεδομένα.

### Πολιτική για κενές τιμές

- Εγγραφές χωρίς τιμή στο πεδίο `Text` δεν μπορούν να ευρετηριαστούν στο Elasticsearch.
- Ο κώδικας αφαιρεί αυτόματα αυτές τις εγγραφές με: 

```python
 df = df.dropna(subset=["Text"])
```

### 1.3 Καθαρισμός κειμένου — `preprocess`

Σε αυτό το βήμα εφαρμόζεται η συνάρτηση **`preprocess`**, η οποία καθαρίζει κάθε τιμή του πεδίου `Text` πριν σταλεί για ευρετηρίαση.

### Τι κάνει η συνάρτηση

Η `preprocess` εκτελεί μια σειρά από βασικούς καθαρισμούς:

- Αφαιρεί leading/trailing κενά και συμπιέζει πολλαπλά κενά σε ένα.
- Αντικαθιστά URLs με το placeholder `<URL>`.
- Αντικαθιστά email addresses με `<EMAIL>`.
- Αφαιρεί HTML tags.
- Διαγράφει control characters (μη εκτυπώσιμους).
- Αφαιρεί μονά και διπλά εισαγωγικά.

Κάθε κείμενο καθαρίζεται με την εντολή: 

```python
df["Text"] = df["Text"].astype(str).map(preprocess)
```

```python
# Preprocess Text Data
def preprocess(s):
    if not isinstance(s, str):
        return ""
    s = s.strip()
    s = re.sub(r"\s+", " ", s)                       # collapse spaces/newlines
    s = re.sub(r"http\S+|www\.\S+", "<URL>", s)      # replace URLs
    s = re.sub(r"\S+@\S+", "<EMAIL>", s)             # replace emails
    s = re.sub(r"<[^>]+>", " ", s)                   # remove HTML tags
    s = ''.join(ch for ch in s if ord(ch) >= 32)     # remove control chars
    s = re.sub(r"['\"]", "", s)                      # remove single and double quotes
    return s.strip()


df = df.dropna(subset=["Text"])
df["Text"] = df["Text"].astype(str).map(preprocess)
```

### 1.4 Μετατροπή σε Μορφή JSONL

Σε αυτό το στάδιο τα καθαρισμένα έγγραφα γράφονται στο αρχείο **`documents.jsonl`**, όπου κάθε γραμμή είναι ένα ανεξάρτητο JSON αντικείμενο.

### Τι κάνει ο κώδικας

- Μετατρέπει το πεδίο `ID` σε καθαρή string μορφή (`astype(str).str.strip()`).
- Για κάθε γραμμή του DataFrame δημιουργεί ένα dictionary:
```json
  {"id": "...", "text": "..."}
```

```python
# Convert to JSONL
records_written = 0
df["ID"] = df["ID"].astype(str).str.strip()
with open(JSONL_PATH, "w", encoding="utf-8") as f:
    for _, row in df.iterrows():
        record = {
            "id": str(row["ID"]).strip(),
            "text": row["Text"]
        }
        json_line = json.dumps(record, ensure_ascii=False)
        f.write(json_line + "\n")
        records_written += 1

print(f"Converted {records_written} rows → JSONL format")
print(f"Output saved at: {JSONL_PATH.resolve()}")
```

## 2️⃣ Δημιουργία Ευρετηρίου στο Elasticsearch


## 2.1 Δημιουργία Ευρετηρίου (`create_index`)

**Στόχος.** Σύνδεση στο Elasticsearch και δημιουργία ενός νέου ευρετηρίου με όνομα `IR2025`.

### Τι κάνει αυτό το βήμα
- Συνδέεται στο cluster μέσω `Elasticsearch("http://127.0.0.1:9200")`.
- Εμφανίζει πληροφορίες σύνδεσης (όνομα cluster, έκδοση, lucene version).
- Αν υπάρχει ήδη ευρετήριο `IR2025`, διαγράφεται.
- Ξεκινά η διαδικασία δημιουργίας (οι ρυθμίσεις BM25 και analyzer εξηγούνται στο επόμενο subsection).

```python
#Initiate search class and index name
search=Search()
INDEX_NAME = "ir2025" 
```

<p align="center">
  <img src="images/elastic2.png" alt="elastic" width="500"/>
</p>

---

## 2.2 Ρυθμίσεις BM25, Analyzer και Mapping

**Στόχος.** Ορισμός του τρόπου ανάλυσης και αξιολόγησης των εγγράφων στο ευρετήριο.

### Τι περιλαμβάνει

#### 🔹 BM25 (k1 = 1.4, b = 0.7)
- **k1 (term saturation):** Ρυθμίζει πόσο επηρεάζει η συχνότητα εμφάνισης μιας λέξης στο έγγραφο.  
  Τιμή 1.4 δίνει ισορροπία ανάμεσα σε “πολύ μεγάλη” και “πολύ μικρή” επίδραση της συχνότητας.
- **b (length normalization):** Ελέγχει πόσο επηρεάζει το μήκος του εγγράφου.  
  Η τιμή 0.7 είναι μια συνηθισμένη επιλογή για συλλογές με ποικιλία μεγέθους κειμένων, ώστε τα μακρύτερα έγγραφα να μην ευνοούνται υπερβολικά. Δοκιμάζοντας διάφορες τιμές, καταλήξαμε στο ότι αυτές παράγουν τα πιο ισσοροπημένα αποτελέσματα.

#### 🔹 Custom English Analyzer
- **standard tokenizer:** Σπάει το κείμενο σε λέξεις με αξιόπιστο τρόπο για αγγλικά κείμενα.
- **lowercase:** Όλα τα tokens σε πεζά → αποφεύγουμε να θεωρούνται διαφορετικά οι "House" και "house".
- **english_stop:** Αφαιρούνται κοινές λέξεις όπως *the, and, in* που δεν συμβάλλουν στη σημασιολογική διάκριση.
- **porter_stem:** Μειώνει τις λέξεις στη ρίζα τους (e.g., "running" → "run") ώστε διαφορετικές μορφές της ίδιας λέξης να ταιριάζουν.

#### 🔹 Mapping
- `id` ως `keyword` ώστε να μην αναλύεται και να χρησιμοποιείται ως σταθερό μοναδικό αναγνωριστικό.
- `text` ως `text` για να επιτρέπεται ανάλυση, stemming και scoring από το BM25.

### 🔹 Σύνοψη
Οι συγκεκριμένες ρυθμίσεις επιλέγονται ώστε το ευρετήριο να είναι **σταθερό**, **αποδοτικό** και **προσαρμοσμένο σε αγγλικά κείμενα**.  
Ο συνδυασμός BM25 + English analyzer παρατηρούμε πως βελτιώνει τη σχετικότητα των αναζητήσεων, μειώνει θόρυβο στο κείμενο και βοηθά το σύστημα να εντοπίζει πιο σωστές αντιστοιχίες μεταξύ `queries` και `docs`.


### Κώδικας Ρυθμίσεων Ευρετηρίου

```python
index_settings = {
    "settings": {
        "similarity": {
            "default": {"type": "BM25", "k1": 1.4, "b": 0.7}
        },
        "analysis": {
            "analyzer": {
                "my_english_analyzer": {
                    "type": "custom",
                    "tokenizer": "standard",
                    "filter": ["lowercase", "english_stop", "porter_stem"]
                }
            },
            "filter": {
                "english_stop": {
                    "type": "stop",
                    "stopwords": "_english_"
                }
            }
        }
    },
    "mappings": {
        "properties": {
            "id":   {"type": "keyword"},
            "text": {"type": "text", "analyzer": "my_english_analyzer"}
        }
    }
}
```
### 2.3 Εισαγωγή Εγγράφων στο Ευρετήριο (`Index Documents`)

**Στόχος.** Να φορτωθούν όλα τα έγγραφα από το `documents.jsonl` στο νέο ευρετήριο `ir2025`.

### Πώς λειτουργεί (σύμφωνα με την κλάση `Search`)
- Η μέθοδος `index_documents(...)` καλεί εσωτερικά την `generate_actions`, η οποία:
  - διαβάζει το αρχείο JSONL γραμμή-γραμμή,
  - μετατρέπει κάθε γραμμή σε *bulk action* για το Elasticsearch,
  - εξασφαλίζει ότι το `id` είναι καθαρό και χρησιμοποιείται ως `_id`.
- Η `helpers.bulk(...)` εκτελεί όλες τις εισαγωγές ταυτόχρονα, ώστε η διαδικασία να είναι πολύ πιο γρήγορη από το single insert.
- Μετά την ολοκλήρωση, εμφανίζεται μήνυμα με τον ακριβή αριθμό εγγράφων που ευρετηριάστηκαν επιτυχώς.

Το αποτέλεσμα είναι πλήρης και γρήγορη ευρετηρίαση όλων των documents.

---

### 2.4 Έλεγχος και Διαχείριση Ευρετηρίου (`Index Exists` / `Delete Index`)

**Στόχος.** Να εξασφαλίζεται ότι κάθε εκτέλεση ξεκινά από «καθαρό» ευρετήριο.

### Πώς λειτουργεί (σύμφωνα με την κλάση `Search`)
- Η `exists(index_name)` ελέγχει αν υπάρχει ήδη ευρετήριο με αυτό το όνομα.
- Αν υπάρχει, η `delete_index(index_name)` το διαγράφει πλήρως από τον Elasticsearch.
- Στη συνέχεια καλείται η `create_index()`, η οποία ξαναδημιουργεί το ίδιο ευρετήριο με τις ρυθμίσεις BM25 και τον custom English analyzer.
- Τέλος, καλείται ξανά η διαδικασία εισαγωγής εγγράφων.

### Σύνδεση με το παραπάνω screenshot
Το screenshot δείχνει ακριβώς αυτή τη ροή:

1. **Έλεγχος ύπαρξης ευρετηρίου**  
2. **Διαγραφή παλιού ευρετηρίου**  
3. **Δημιουργία νέου κενού ευρετηρίου**  
4. **Εισαγωγή εγγράφων**

<p align="center">
  <img src="images/searchcalls.png" alt="searchcalls" width="500"/>
</p>

**Συνοπτικό σχόλιο.**  
Η λειτουργικότητα που περιγράφεται στην Ενότητα 2 (δημιουργία ευρετηρίου, διαγραφή, έλεγχος ύπαρξης, bulk εισαγωγή και αναζήτηση) υλοποιείται πλήρως στην κλάση `Search`.  
Μπορείς να δεις ολόκληρο τον κώδικα εδώ:

[Πλήρης κώδικας της κλάσης `Search` (Appendix A.1)](#appendix-search)

---

## 3️⃣ Αξιολόγηση με trec_eval

Η ενότητα αυτή περιγράφει πώς αξιολογείται το σύστημα ανάκτησης με το εργαλείο **trec_eval**, μέσω της κλάσης `Evaluation`, των αρχείων αποτελεσμάτων `results_k.txt` και του αρχείου qrels.

---

## 3.1 Δημιουργία Αρχείων Αποτελεσμάτων (`results_k.txt`)

**Στόχος:** Εκτέλεση των queries από το `queries.csv` και παραγωγή αρχείων αποτελεσμάτων σε TREC-format για k = 20, 30, 50.

### Πώς λειτουργεί
- Φορτώνονται τα queries από το CSV.
- Για κάθε query γίνεται αναζήτηση με `search_query()`.
- Για κάθε `k` δημιουργείται αρχείο `results_k.txt`.
- Κάθε γραμμή γράφεται σε TREC run format:

```python
qid Q0 docid rank score BM25
```
- Τα αρχεία αποθηκεύονται στο directory της εργασίας και επιβεβαιώνονται με μηνύματα:
`Created results file: results_20.txt`.

<p align="center">
<img src="images/generateresults.png" width="500"/>
</p>

---

## 3.2 Εκτέλεση trec_eval

**Στόχος:** Υπολογισμός των μετρικών **MAP** και **P@k** για κάθε αρχείο αποτελεσμάτων.

### Πώς λειτουργεί
- Για κάθε `results_k.txt` κατασκευάζεται εντολή:

<p align="center">
<img src="images/cmd.png" width="500"/>
</p>

- Η μέθοδος `_run_trec_eval_for_file()`:
- εκτελεί το `trec_eval.exe` μέσω subprocess,
- αναλύει το output,
- εξάγει:
  - MAP (Υπολογίζει τον μέσο όρο της Average Precision (AP) σε όλα τα queries.)
  - P@5, P@10, P@15, P@20 (Μετρά το ποσοστό σχετικών εγγράφων μέσα στα k πρώτα αποτελέσματα που επιστρέφει το σύστημα.)
- επιστρέφει τις τιμές ως dictionary.
- Όλα τα paths εμφανίζονται **σε σχετική μορφή** (π.χ. `trec_eval/trec_eval.exe`) χάρη στη μέθοδο `_rel()`.
- Παρακάτω βλέπετε τον κώδικα της `_run_trec_eval_for_file`:

```python
def _run_trec_eval_for_file(self, results_file: Path):
        # Stop if the results file does not exist
        if not results_file.exists():
            raise SystemExit(f"results file not found: {results_file}")

        # Build the trec_eval command using relative paths
        cmd = (
            f"{self._rel(self.trec_eval_bin)} "
            f"{self._rel(self.qrels_txt_path)} "
            f"{self._rel(results_file)} -m all_trec"
        )
        print("Running:", cmd)
        # Execute trec_eval as a subprocess
        proc = subprocess.run(cmd, capture_output=True, text=True)

        # Handle execution errors
        if proc.returncode != 0:
            print("--- trec_eval stderr ---")
            print(proc.stderr)
            raise SystemExit(f"trec_eval failed for {results_file} (rc={proc.returncode})")

        parsed = {}

        # Parse trec_eval output line-by-line
        for line in proc.stdout.strip().splitlines():
            parts = re.split(r"\s+", line.strip())
            if len(parts) < 3:
                continue

            metric = parts[0]    
            target = parts[1]      
            value_str = parts[-1] 

            # Only process metrics for the global target 'all'
            if target.lower() != "all":
                continue

            #MAP
            if metric.lower().startswith("map") and metric.lower() == "map":
                try:
                    parsed["MAP"] = float(value_str)
                except ValueError:
                    pass
                continue

            #P@k metrics (P_5, P_10, P_15, P_20)
            m = re.match(r"^P_(\d+)$", metric)
            if m:
                k = int(m.group(1))
                key = f"P@{k}"
                try:
                    parsed[key] = float(value_str)
                except ValueError:
                    pass
                continue

        # Return metrics
        return {
            "P@5":  parsed.get("P@5"),
            "P@10": parsed.get("P@10"),
            "P@15": parsed.get("P@15"),
            "P@20": parsed.get("P@20"),
            "MAP":  parsed.get("MAP"),
        }
```

---

## 3.3 Συγκεντρωτικός Πίνακας Αποτελεσμάτων

**Στόχος:** Οργάνωση των μετρικών σε πίνακα και αποθήκευση στο αρχείο `trec_eval_summary.txt`.

### Τι κάνει ο κώδικας
- Συγκεντρώνει τα αποτελέσματα σε `DataFrame` με στήλες:
`retrieval_k`, `P@5`, `P@10`, `P@15`, `P@20`, `MAP`.
- Εμφανίζει συνοπτικό πίνακα στο terminal.
- Αποθηκεύει τα αποτελέσματα σε TREC-style layout στο `trec_eval_summary.txt`.


| retrieval_k |  P@5 | P@10 |  P@15 | P@20 |   MAP   |
|-------------|------|------|-------|------|---------|
|     20      | 0.90 | 0.74 | 0.7133| 0.645| 0.7129  |
|     30      | 0.90 | 0.74 | 0.7133| 0.645| 0.7749  |
|     50      | 0.90 | 0.74 | 0.7133| 0.645| 0.7923  |

### Τα αποτελέσματα αυτά δημιουργούνται επίσης για τα αρχεία `results_30` και `results_50`.

---

## 3.4 Κλήσεις της Κλάσης Evaluation

Οι παρακάτω κλήσεις ενεργοποιούν **ολόκληρο** το pipeline αξιολόγησης:  
δημιουργία αρχείων αποτελεσμάτων (results_k.txt), εκτέλεση του `trec_eval` και εξαγωγή συγκεντρωτικών μετρικών.

<p align="center">
  <img src="images/evaluatecalls.png" alt="evaluation calls" width="500"/>
</p>

### Τι κάνει η κλάση `Evaluation` (σύντομα)

- **Αρχικοποίηση:**  
  Εντοπίζει τα αρχεία `qrels.csv` και `qrels.txt`, δημιουργεί TREC-format qrels αν χρειάζεται και βρίσκει το εκτελέσιμο `trec_eval.exe`.

- **Παραγωγή αποτελεσμάτων:**  
  Η `generate_file_results()` φορτώνει τα queries και δημιουργεί τα αρχεία  
  `results_20.txt`, `results_30.txt`, `results_50.txt` σε TREC run format.

- **Αξιολόγηση με trec_eval:**  
  Η `evaluate_with_trec_eval()` τρέχει το `trec_eval` για κάθε αρχείο και εξάγει τις
  μετρικές **P@5, P@10, P@15, P@20 και MAP**.

- **Τελική έξοδος:**  
  Επιστρέφεται DataFrame με όλες τις μετρικές και παράγεται το αρχείο  
  `trec_eval_summary.txt`.

### Με αυτές τις δύο κλήσεις ολοκληρώνεται ολόκληρη η διαδικασία αξιολόγησης του συστήματος BM25.

**Συνοπτικό σχόλιο.**  
Η λειτουργικότητα που περιγράφεται στην Ενότητα 3 (παραγωγή αρχείων αποτελεσμάτων, εκτέλεση του `trec_eval`, εξαγωγή μετρικών και δημιουργία συγκεντρωτικού summary) υλοποιείται εξολοκλήρου μέσα στην κλάση `Evaluation`.  
Η κλάση αναλαμβάνει πλήρως:

- τη φόρτωση των queries,  
- τη δημιουργία των αρχείων `results_k.txt`,  
- την εκτέλεση του `trec_eval.exe` με σωστή μορφοποίηση paths,  
- την εξαγωγή και οργάνωση των μετρικών P@k και MAP,  
- καθώς και τη δημιουργία του αρχείου `trec_eval_summary.txt`.

Μπορείς να δεις ολόκληρο τον κώδικα εδώ:

**[Πλήρης κώδικας της κλάσης `Evaluation` (Appendix A.2)](#appendix-evaluation)**

---

## 4️⃣ Αξιολόγηση Αποτελεσμάτων με το `trec_eval`

### 4.1 Τι είναι το `trec_eval` (σύντομα)

- Το **`trec_eval`** είναι το κλασικό εργαλείο αξιολόγησης συστημάτων IR.  
- Λαμβάνει ως είσοδο:
  - TREC-formatted αρχεία αποτελεσμάτων (`results_k.txt`)
  - Το αρχείο relevance judgments `qrels.txt`
- Υπολογίζει βασικές μετρικές:
  - **P@k (Precision at k)** – ποιο ποσοστό των top-k αποτελεσμάτων είναι σχετικά  
  - **MAP (Mean Average Precision)** – μέτρο της συνολικής απόδοσης και ταξινόμησης του συστήματος

---

### 4.2 Εκτέλεση Αξιολόγησης

- Τα αρχεία `results_20.txt`, `results_30.txt`, `results_50.txt` δημιουργήθηκαν από τη `generate_file_results()`.
- Κάθε αρχείο έχει TREC run format (`qid Q0 docid rank score BM25`) όπως αναφέραμε και πορηγουμένως.
- Η `evaluate_with_trec_eval()` εκτελεί το `trec_eval` για όλα τα k και εξάγει:
  - **P@5, P@10, P@15, P@20**
  - **MAP**
- Τα αποτελέσματα καταγράφονται στον πίνακα και αποθηκεύονται στο `trec_eval_summary.txt`.

---

### 4.3 Παρουσίαση και Ερμηνεία Αποτελεσμάτων

- Παρακάτω φαίνεται η γραφική απεικόνιση των τιμών **P@k** και **MAP** για τιμές *k = 20, 30, 50*.

- Παρατηρούμε ότι:

  - Οι τιμές **P@5 = 0.90**, **P@10 = 0.74**, **P@15 = 0.7133** και **P@20 = 0.645** παραμένουν **απόλυτα σταθερές** και για τα τρία k.  
    Αυτό δείχνει ότι το ευρετήριο επιστρέφει σχεδόν *ακριβώς τα ίδια κορυφαία σχετικά έγγραφα* στις πρώτες θέσεις ανεξάρτητα από το πλήθος των τελικών ανακτώμενων αποτελεσμάτων (k).  
    Με άλλα λόγια: η ποιότητα των **top αποτελεσμάτων** είναι ήδη πολύ υψηλή και δεν επηρεάζεται από το αν αναζητούμε 20, 30 ή 50 αποτελέσματα.

  - Αντίθετα, η τιμή **MAP** παρουσιάζει ξεκάθαρο ανοδικό μοτίβο:
    - MAP@20  → **0.7129**  
    - MAP@30  → **0.7749**  
    - MAP@50  → **0.7923**

    Όσο αυξάνεται το *k*:
    - το σύστημα επιστρέφει περισσότερα σχετικά έγγραφα (relevant documents)  
    - κάποια από αυτά εμφανίζονται σε καλύτερες σχετικές θέσεις (rank positions)  
    - άρα ο μέσος όρος precision σε όλο το ranking βελτιώνεται.

    Η MAP λαμβάνει υπόψη *όλα* τα σχετικά έγγραφα και τις θέσεις τους στο ranking.  
    Όταν αυξάνουμε το *k* και το σύστημα έχει διαθέσιμα περισσότερα σχετικά έγγραφα να εμφανίσει, η συνολική "ποιότητα ταξινόμησης" του ευρετηρίου γίνεται καλύτερη.

- Συμπέρασμα:  
  Αν και οι κορυφαίες θέσεις (P@5–P@20) παραμένουν ίδιες, καθώς το ευρετήριο εντοπίζει ήδη τα πιο σχετικά έγγραφα, όσο αυξάνεται το retrieval depth *k*, αποκαλύπτονται περισσότερα σχετικά έγγραφα σε χαμηλότερες θέσεις. Αυτό επιτρέπει στη MAP να αποτυπώσει πιο πλήρως την ποιότητα ταξινόμησης, οδηγώντας σε σταδιακή αύξησή της.

<p align="center">
  <img src="images/precision-map.png" alt="Precision and MAP metrics" width="700"/>
</p>


---

## 5️⃣ Συζήτηση και Παρατηρήσεις

Η υλοποίηση της **Φάσης 1** έδειξε ότι το σύστημα **BM25** λειτουργεί σταθερά και προβλέψιμα στη συλλογή **IR2025**.  
Οι μετρικές **P@k** δείχνουν ότι το ευρετήριο αναγνωρίζει σωστά τα πιο σχετικά έγγραφα ήδη στις πρώτες θέσεις της κατάταξης, ενώ η αύξηση της **MAP** με υψηλότερες τιμές k υποδηλώνει ότι η συνολική ταξινόμηση των εγγράφων είναι ποιοτική και αποκαλύπτεται πληρέστερα όσο βαθύτερα εξετάζουμε τη λίστα αποτελεσμάτων.

Παράλληλα, οι τιμές **MAP > 0.70** δείχνουν ότι η κλασική ανάκτηση με **BM25** είναι αποτελεσματική στη συγκεκριμένη συλλογή, παρέχοντας ένα ισχυρό **baseline** για τις επόμενες φάσεις της εργασίας.

---

## 6️⃣ Συμπεράσματα και Επόμενα Βήματα

Η Φάση 1 ολοκληρώθηκε επιτυχώς με:

- πλήρη προεπεξεργασία και καθαρισμό της συλλογής IR2025,  
- σωστή δημιουργία και διαχείριση ευρετηρίου στο Elasticsearch,  
- παραγωγή TREC-format αποτελεσμάτων για διαφορετικές τιμές k,  
- αξιολόγηση μέσω του trec_eval και ανάλυση μετρικών P@k και MAP.

Η **επόμενη** φάση της εργασίας περιλαμβάνει την υλοποίηση **Semantic Search** με χρήση ενσωματωμάτων (embeddings) και **FAISS** για Approximate Nearest Neighbor Search.

Πιο συγκεκριμένα:

- Δημιουργία embeddings για τα έγγραφα και τα queries  
- Χρήση pretrained Transformer μοντέλων (π.χ. MiniLM, mpnet)  
- Κατασκευή FAISS index για γρήγορη αναζήτηση σε μεγάλο χώρο διανυσμάτων  
- Σύγκριση BM25 vs Semantic Search  

Η Φάση 2 θα επιτρέψει να αξιολογηθεί πόσο καλύτερα (ή διαφορετικά) λειτουργεί η σημασιολογική αναζήτηση σε σχέση με το κλασικό BM25 baseline που αναπτύχθηκε σε αυτή τη φάση.


---

## Appendix

### A.1 Κλάση `Search` (Ευρετηρίαση και Αναζήτηση)
<a name="appendix-search"></a>

Η κλάση **`Search`** υλοποιεί όλο το κομμάτι της ευρετηρίασης και της αναζήτησης στο Elasticsearch.  
Αποτελεί τον βασικό μηχανισμό που διαχειρίζεται:

- Σύνδεση στο Elasticsearch
- Δημιουργία/διαγραφή ευρετηρίου (`create_index`, `delete_index`)
- Έλεγχο ύπαρξης ευρετηρίου (`exists`)
- Eισαγωγή μεμονωμένων εγγράφων (`insert_document`) ή λίστας εγγράφων (`insert_documents`),  
- Μαζική εισαγωγή εγγράφων από αρχείο JSONL (`index_documents`)
- Εκτέλεση queries και επιστροφή top-k αποτελεσμάτων (`search_query`)

Παρακάτω παρατίθεται **ολόκληρος ο κώδικας** της κλάσης, όπως χρησιμοποιείται στο project:

```python
class Search:
    def __init__(self):
        self.es = Elasticsearch("http://127.0.0.1:9200")
        client_info = self.es.info()
        print('Connected to Elasticsearch!')
        pprint(client_info.body)

    #Return instance
    def get_es(self):
        return self.es

    #Creates empty index with the parameters we feed
    def create_index(self):
        index_settings = {
            "settings": {
                "similarity": {
                    "default": {"type": "BM25", "k1": 1.4, "b": 0.7}
                },
                "analysis": {
                    "analyzer": {
                        "my_english_analyzer": {
                            "type": "custom",
                            "tokenizer": "standard",
                            "filter": [
                                "lowercase",
                                "english_stop",
                                "porter_stem"
                            ]
                        }
                    },
                    "filter": {
                        "english_stop": {
                            "type": "stop",
                            "stopwords": "_english_"
                        }
                    }
                }
            },
            "mappings": {
                "properties": {
                    "id": {"type": "keyword"},
                    "text": {"type": "text", "analyzer": "my_english_analyzer"}
                }
            }
        }

        self.es.indices.create(index='ir2025', body=index_settings)
        print("Index created with English analyzer and BM25 similarity.")

    #Insert single document
    def insert_document(self, document):
        return self.es.index(index='ir2025', document=document)

    #Insert multiple indexes
    def insert_documents(self, documents):
        operations = []
        for document in documents:
            operations.append({'index': {'_index': 'ir2025'}})
            operations.append(document)

        result = self.es.bulk(operations=operations)
        print("Insertion finished.")
        print(result)

    #Deletes index
    def delete_index(self, index_name="ir2025"):
        if self.es.indices.exists(index=index_name):
            self.es.indices.delete(index=index_name)
            print(f"Index '{index_name}' deleted successfully.")
        else:
            print(f"Index '{index_name}' does not exist.")

    #Check if index exists
    def exists(self, index_name="ir2025"):
        try:
            return self.es.indices.exists(index=index_name)
        except Exception as e:
            print(f"Error checking if index exists: {e}")
            return False

    #Helper function for bulk indexing
    def generate_actions(self, jsonl_path, index_name):
        with open(jsonl_path, encoding="utf-8") as f:
            for line in f:
                if not line.strip():
                    continue  # skip empty lines
                doc = json.loads(line)
                doc_id = str(doc["id"]).strip()
                # Ensure cleaned ID is saved back to _source
                doc["id"] = doc_id
                yield {
                    "_index": index_name,
                    "_id": doc_id,      # correct canonical ID
                    "_source": doc
                }

    #Bulk index docs
    def index_documents(self, jsonl_path, index_name):
        actions = self.generate_actions(jsonl_path, index_name)
        success, _ = helpers.bulk(self.get_es(), actions)
        print(f"Successfully indexed {success} documents into '{index_name}'")

    #Runs query
    def search_query(self, query_text, k=10, index_name="ir2025"):
        try:
            resp = self.es.search(
                index=index_name,
                query={"match": {"text": {"query": query_text}}},
                size=k
            )
            return resp["hits"]["hits"]
        except Exception as e:
            print(f"Error executing search query: {e}")
            return []
```

### A.2 Κλάση `Evaluation` (Αξιολόγηση Αποτελεσμάτων)

### A.2 Λειτουργικότητα της Κλάσης `Evaluation` (Γενική Σύνοψη)

Η κλάση **`Evaluation`** είναι υπεύθυνη για **ολόκληρη τη διαδικασία αξιολόγησης** του συστήματος ανάκτησης, από τη δημιουργία των αρχείων αποτελεσμάτων έως την εκτέλεση του `trec_eval` και τη συγκέντρωση των μετρικών.

Οι βασικές λειτουργίες της κλάσης είναι:

- **Αρχικοποίηση & Προετοιμασία Αρχείων**  
  Εντοπίζει τα paths του project, φορτώνει `qrels.csv` και δημιουργεί `qrels.txt` σε TREC format (αν δεν υπάρχει).  
  Εντοπίζει επίσης το εκτελέσιμο `trec_eval`.

- **Δημιουργία Αρχείων Αποτελεσμάτων (`results_k.txt`)**  
  Εκτελεί τα queries στο ευρετήριο και δημιουργεί τα run files για κάθε τιμή k (20, 30, 50).

- **Εκτέλεση του trec_eval**  
  Τρέχει το εργαλείο αξιολόγησης για κάθε αρχείο αποτελεσμάτων και εξάγει τις μετρικές **P@k** και **MAP**.

- **Συγκέντρωση και Οργάνωση Μετρικών**  
  Ομαδοποιεί τα αποτελέσματα σε DataFrame και προαιρετικά γράφει συγκεντρωτικό αρχείο (`trec_eval_summary.txt`).

Με αυτόν τον τρόπο, η `Evaluation` παρέχει ένα πλήρες και αυτόματο pipeline αξιολόγησης για όλα τα retrieval depths που εξετάζονται.


Παρακάτω δίνεται όλη η κλάση όπως χρησιμοποιείται στο project:

```python
class Evaluation:
    def _rel(self, p: Path):
        """Return path relative to data_dir with forward slashes."""
        try:
            rel = p.resolve().relative_to(self.data_dir.resolve())
        except Exception:
            rel = Path(p.name)
        return str(rel).replace("\\", "/")   # normalize slashes

    def __init__(
        self,
        search_client,
        data_dir: Path,
        qrels_csv_path: Path,
        qrels_txt_path: Path = None,
        trec_eval_bin: Path = None,
    ):
        self.search = search_client
        self.data_dir = Path(data_dir)
        self.qrels_csv_path = Path(qrels_csv_path)
        self.qrels_txt_path = Path(qrels_txt_path) if qrels_txt_path else self.data_dir / "qrels.txt"

        # Ensure qrels.txt exists (if not, convert from qrels.csv)
        self._ensure_trec_qrels()

        # Find trec_eval executable
        if trec_eval_bin is None:
            self.trec_eval_bin = self._find_trec_eval()
        else:
            self.trec_eval_bin = Path(trec_eval_bin)

        if not self.trec_eval_bin or not self.trec_eval_bin.exists():
            raise SystemExit(
                "trec_eval binary not found. Put trec_eval.exe in trec_eval folder "
                "or pass a valid path to the constructor."
            )

        if not self.qrels_txt_path.exists():
            raise SystemExit(f"qrels file not found: {self.qrels_txt_path}")

        print(f"Using trec_eval at: {self._rel(self.trec_eval_bin)}")
        print(f"Using qrels file:  {self._rel(self.qrels_txt_path)}")

    # qrels.csv -> qrels.txt (TREC format)
    def _fix_and_write_qrels(self):
        """Reads qrels.csv and writes qrels.txt in TREC format: qid 0 docid rel."""
        if not self.qrels_csv_path.exists():
            raise SystemExit(f"qrels.csv not found: {self.qrels_csv_path}")

        dfq = pd.read_csv(
            self.qrels_csv_path,
            sep=None,
            engine="python",
            encoding="utf-8-sig",
        )
        dfq = dfq.dropna(how="all")
        dfq = dfq.astype(str).applymap(lambda x: x.strip())
        cols = list(dfq.columns)

        # attempt to guess docid column
        res_docids = set()
        sample_results_path = self.data_dir / "results_20.txt"
        if sample_results_path.exists():
            with open(sample_results_path, encoding="utf-8", errors="replace") as f:
                for ln in f:
                    parts = re.split(r"\s+", ln.strip())
                    if len(parts) >= 3:
                        res_docids.add(parts[2])

        candidates = []
        for c in cols:
            vals = dfq[c].dropna().astype(str).str.strip().unique()[:200].tolist()
            inter = len(set(vals) & res_docids) if res_docids else 0
            num_like = sum(1 for v in vals if re.match(r"^\d+$", v))
            candidates.append((c, inter, num_like, vals[:5]))

        docid_col = max(candidates, key=lambda x: (x[1], x[2]))[0]
        qid_col = cols[0]
        rel_col = None
        for c, inter, num_like, vals in candidates:
            if c in (qid_col, docid_col):
                continue
            sample_vals = dfq[c].dropna().astype(str).str.strip().unique()[:50].tolist()
            if sample_vals and all(re.match(r"^\d+$", v) for v in sample_vals):
                rel_col = c
                break
        if rel_col is None:
            others = [c for c in cols if c not in (qid_col, docid_col)]
            rel_col = others[-1] if others else cols[-1]

        with open(self.qrels_txt_path, "w", encoding="utf-8") as out:
            for _, row in dfq.iterrows():
                qid = str(row[qid_col]).strip()
                docid = str(row[docid_col]).strip()
                rel = str(row[rel_col]).strip()
                if not qid or not docid or docid.upper() == "Q0":
                    continue
                out.write(f"{qid} 0 {docid} {rel}\n")

        print(
            f"Wrote TREC qrels -> {self._rel(self.qrels_txt_path)} "
            f"(qid_col={qid_col}, docid_col={docid_col}, rel_col={rel_col})"
        )

    def _ensure_trec_qrels(self):
        """Uses existing qrels.txt or creates it from qrels.csv."""
        if self.qrels_txt_path.exists():
            print(f"Using existing TREC qrels: {self._rel(self.qrels_txt_path)}")
            return
        print("TREC qrels not found, creating from qrels.csv ...")
        self._fix_and_write_qrels()

    # Find trec_eval binary
    def _find_trec_eval(self):
        candidates = [
            self.data_dir / "trec_eval" / "trec_eval.exe",
            self.data_dir / "trec_eval" / "trec_eval",
            self.data_dir / "trec_eval.exe",
            self.data_dir / "trec_eval",
        ]
        for p in candidates:
            if p.exists():
                return p

        p_on_path = shutil.which("trec_eval") or shutil.which("trec_eval.exe")
        if p_on_path:
            return Path(p_on_path)

        if self.data_dir.exists():
            for p in self.data_dir.rglob("trec_eval*"):
                if p.is_file():
                    return p
        return None

    # Create results_20/30/50.txt
    def generate_file_results(self, queries_path: Path, ks=(20, 30, 50)):
        df_queries = pd.read_csv(queries_path, encoding="utf-8")
        print(f"Loaded queries: {len(df_queries)}")

        for k in ks:
            results_path = self.data_dir / f"results_{k}.txt"
            with open(results_path, "w", encoding="utf-8") as f:
                for _, row in df_queries.iterrows():
                    qid = str(row.iloc[0])
                    qtext = str(row.iloc[1])
                    results = self.search.search_query(qtext, k)
                    for rank, hit in enumerate(results, start=1):
                        docid = hit["_source"]["id"]
                        score = hit["_score"]
                        f.write(f"{qid} Q0 {docid} {rank} {score:.4f} BM25\n")
            print(f"Created results file: {self._rel(results_path)}")

    # Run trec_eval on one results file
    def _run_trec_eval_for_file(self, results_file: Path):
        if not results_file.exists():
            raise SystemExit(f"results file not found: {results_file}")

        cmd = (
            f"{self._rel(self.trec_eval_bin)} "
            f"{self._rel(self.qrels_txt_path)} "
            f"{self._rel(results_file)} -m all_trec"
        )
        print("Running:", cmd)

        proc = subprocess.run(cmd, capture_output=True, text=True)

        if proc.returncode != 0:
            print("--- trec_eval stderr ---")
            print(proc.stderr)
            raise SystemExit(f"trec_eval failed for {results_file} (rc={proc.returncode})")

        parsed = {}

        for line in proc.stdout.strip().splitlines():
            parts = re.split(r"\s+", line.strip())
            if len(parts) < 3:
                continue

            metric = parts[0]
            target = parts[1]
            value_str = parts[-1]

            if target.lower() != "all":
                continue

            if metric.lower() == "map":
                try:
                    parsed["MAP"] = float(value_str)
                except ValueError:
                    pass
                continue

            m = re.match(r"^P_(\d+)$", metric)
            if m:
                k = int(m.group(1))
                try:
                    parsed[f"P@{k}"] = float(value_str)
                except ValueError:
                    pass
                continue

        return {
            "P@5":  parsed.get("P@5"),
            "P@10": parsed.get("P@10"),
            "P@15": parsed.get("P@15"),
            "P@20": parsed.get("P@20"),
            "MAP":  parsed.get("MAP"),
        }
    # Full evaluation for k = 20/30/50
    def evaluate_with_trec_eval(self, ks=(20, 30, 50), out_summary_path: Path = None):
        summary_rows = []
        for k in ks:
            results_file = self.data_dir / f"results_{k}.txt"
            metrics = self._run_trec_eval_for_file(results_file)
            row = {"retrieval_k": k}
            row.update(metrics)
            summary_rows.append(row)

        df_trec = pd.DataFrame(summary_rows).sort_values("retrieval_k").reset_index(drop=True)

        print("\n--- trec_eval summary ---")
        print(df_trec.to_string(index=False))

        if out_summary_path is not None:
            with open(out_summary_path, "w", encoding="utf-8") as f:
                for _, r in df_trec.iterrows():
                    k = int(r["retrieval_k"])
                    f.write(f"# summary for results_{k}.txt\n")
                    for kk in [5, 10, 15, 20]:
                        val = r.get(f"P@{kk}")
                        if val is None:
                            f.write(f"P_{kk}\tall\tNaN\n")
                        else:
                            f.write(f"P_{kk}\tall\t{val:.4f}\n")
                    mapv = r.get("MAP")
                    if mapv is None:
                        f.write("map\tall\tNaN\n\n")
                    else:
                        f.write(f"map\tall\t{mapv:.4f}\n\n")
            print(f"Wrote trec_eval summary file: {self._rel(out_summary_path)}")

        return df_trec
```
