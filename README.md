# 🚖 NYC Taxi & Weather Data Engineering Pipeline (11M+ Rows)

Ένα ολοκληρωμένο **End-to-End Batch ETL Pipeline** που υλοποιήθηκε σε περιβάλλον **Databricks** με τη χρήση **PySpark** και **SQL**, επεξεργαζόμενο πάνω από 11 εκατομμύρια εγγραφές πραγματικών δεδομένων.

## 🏗️ Αρχιτεκτονική (Medallion Architecture)
Το project ακολουθεί τα βιομηχανικά πρότυπα της αρχιτεκτονικής Medallion για τη διαχείριση Big Data:

1. **Bronze Layer (Raw Data):** Ingestion raw αρχείων Parquet (δεδομένα κίνησης ταξί της Νέας Υόρκης) και αρχείων CSV (ιστορικά δεδομένα καιρού μέσω του Open-Meteo API).
2. **Silver Layer (Cleaned & Enriched):** Data cleaning, φιλτράρισμα 3.2 εκατομμυρίων λανθασμένων ή ελαττωματικών εγγραφών (αρνητικά ποσά, λανθασμένες ημερομηνίες) και schema enforcement μέσω PySpark.
3. **Gold Layer (Analytics / Insights):** Εκτέλεση distributed join των δύο πηγών με βάση την ημερομηνία και αποθήκευση σε μορφή Delta Table για business ανάλυση.

## 🛠️ Τεχνολογίες & Εργαλεία (Tech Stack)
- **Γλώσσες:** Python, SQL, PySpark SQL
- **Πλατφόρμα:** Databricks Community Edition (Serverless Compute)
- **Μορφές Αρχείων:** Parquet, CSV, Delta Lake (Delta Tables)

## 📊 Κύρια Συμπεράσματα (Key Insights)
Μέσω SQL queries στο Gold layer, αναλύθηκε η επίδραση του καιρού στη συμπεριφορά των καταναλωτών και στα φιλοδωρήματα (tips):
- ☀️ **Στεγνός Καιρός:** Μέσο Tip **$3.64**
- 🌦️ **Ελαφριά Βροχή:** Μέσο Tip **$3.62**
- 🌧️ **Έντονη Βροχή:** Μέσο Tip **$3.57**

*Συμπέρασμα:* Αντίθετα με την αρχική υπόθεση, η έντονη βροχή μειώνει ελαφρώς το φιλοδώρημα, πιθανώς λόγω της αυξημένης κυκλοφοριακής συμφόρησης και των καθυστερήσεων στους δρόμους της Νέας Υόρκης, που προκαλούν εκνευρισμό στους επιβάτες.

## 💻 Δείγμα Κώδικα (PySpark Data Cleaning)
```python
# Εφαρμογή φίλτρων ποιότητας δεδομένων στο Silver Layer
taxis_silver_df = taxis_df.filter(
    (col("tpep_pickup_datetime") >= "2026-01-01 00:00:00") & 
    (col("tpep_pickup_datetime") <= "2026-03-31 23:59:59") & 
    (col("trip_distance") > 0) & 
    (col("total_amount") > 0) &
    (col("passenger_count") > 0)
)
