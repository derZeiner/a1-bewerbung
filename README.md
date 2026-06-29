# SMS Spam Analyse

Diese Abgabe enthält eine klassische lokale ML/Data-Science-Lösung für Aufgabe 4.1 und ein GenAI-/LLM-Embedding-Clustering für Aufgabe 4.2.

## Setup

Die vorhandene `.venv` war in dieser Umgebung nicht zuverlässig startbar. Empfohlen ist deshalb eine frische virtuelle Umgebung:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

Danach Jupyter starten:

```powershell
jupyter notebook
```

Falls `python` auf den WindowsApps-Stub zeigt, zuerst Python 3.11+ regulär installieren oder den Python Launcher verwenden:

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

## Ausführungsreihenfolge

1. `data_exploration.ipynb`
   - liest `data/00_raw/SMSSpamCollection` explizit als UTF-8,
   - prüft Anzahl, Klassenverteilung, Missing Values, Targets, Encoding und Duplikate,
   - dekodiert HTML-Entities, entfernt Duplikate nach Nachrichtentext,
   - schreibt `data/01_cleaned/sms_cleaned.csv`.

2. `04_1_classic_ml.ipynb`
   - beantwortet die Fragen zu häufigsten Wörtern, HAM/SPAM-Wortwahl und Wortgruppen,
   - trainiert und evaluiert lokale klassische Modelle,
   - verwendet keine externen ML- oder LLM-Dienste.

3. `04_2_genai_clustering.ipynb`
   - erzeugt SMS-Embeddings über eine externe OpenAI-kompatible Embeddings-API,
   - cached Embeddings lokal unter `data/01_cleaned`,
   - clustert die SMS semantisch mit KMeans und interpretiert die Cluster.

## Aufgabe 4.1: lokal und klassisch

Verwendete Bibliotheken:

- `pandas`, `numpy` für Datenanalyse
- `scikit-learn` für Tokenisierung, Vektorisierung, Modelle und Metriken
- `matplotlib`, `seaborn` für Visualisierungen

Textrepräsentationen:

- `CountVectorizer` für Häufigkeitsanalysen und `MultinomialNB`
- `TfidfVectorizer` für lineare Modelle, damit sehr häufige Wörter weniger stark dominieren

Verglichene Modelle:

- `DummyClassifier` als Baseline
- `MultinomialNB` als klassische Text-Baseline
- `LogisticRegression` jeweils mit Count-Features und TF-IDF
- `LinearSVC` jeweils mit Count-Features und TF-IDF
- optional `DistilBERT`/BERT-ähnlicher Transformer als Deep-Learning-Vergleich
- optional LSTM auf tokenisierten SMS als sequenzieller Neural-Baseline

Die `Dummy majority baseline` sagt immer die Mehrheitsklasse HAM voraus. Deshalb sind SPAM-Precision, SPAM-Recall und SPAM-F1 dort absichtlich 0; sie zeigt, wie wenig reine Accuracy bei unausgeglichenen Klassen aussagt.

Der klassische Teil bleibt lokal und schnell ausführbar. Die Deep-Learning-Zellen in `04_1_classic_ml.ipynb` sind als zusätzliche Experimente gekennzeichnet; sie benötigen `torch` und `transformers` und laufen mit CUDA, falls verfügbar. xLSTM wird im Notebook als Option eingeordnet, aber nicht als harte Dependency verwendet, weil es kein stabiler Standardbestandteil der üblichen Python-ML-Bibliotheken ist.

`04_1_classic_ml.ipynb` nutzt für Modellwahl und Deep Learning einen stratified Train/Validation/Test Split. Validation wählt das beste klassische Modell und steuert Early Stopping bei LSTM/DistilBERT; der Test-Split wird erst am Ende für die finale Bewertung verwendet.

Am Ende von `04_1_classic_ml.ipynb` wird eine gemeinsame Test-Vergleichstabelle erzeugt. Sie enthält alle klassischen Modelle sowie LSTM und DistilBERT, sofern die jeweiligen Deep-Learning-Zellen ausgeführt wurden.

Für den DistilBERT-Download kann optional ein Hugging-Face-Token gesetzt werden. Ohne Token funktioniert der Download meist trotzdem, aber mit niedrigeren Rate Limits:

```text
HF_TOKEN=hf_...
```

Falls Jupyter eine `IProgress not found`-Warnung von `tqdm` zeigt, `ipywidgets` aus `requirements.txt` installieren und den Kernel neu starten.

## Aufgabe 4.2: externe Embeddings

Default-Konfiguration:

- API: OpenAI-kompatible Embeddings-API
- Modell: `text-embedding-3-small`
- API-Key: `OPENAI_API_KEY`
- Optionaler kompatibler Endpoint: `OPENAI_BASE_URL`

Beispiel `.env`:

```text
OPENAI_API_KEY=sk-...
OPENAI_EMBEDDING_MODEL=text-embedding-3-small
```

Das Notebook lädt `.env` automatisch. Falls ein anderer OpenAI-kompatibler Provider genutzt wird:

```text
OPENAI_API_KEY=...
OPENAI_BASE_URL=https://...
OPENAI_EMBEDDING_MODEL=...
```

Embeddings werden als `.npy` gecacht. Wenn der Cache existiert, werden keine neuen API-Calls ausgeführt.

## Daten

Rohdaten werden nicht überschrieben. Der bereinigte Datensatz liegt unter:

```text
data/01_cleaned/sms_cleaned.csv
```

Spalten:

- `label`: `ham` oder `spam`
- `target`: `0` für HAM, `1` für SPAM
- `message`: UTF-8-korrekt gelesener SMS-Text

Der Clean-CSV ist dedupliziert. Aktueller Stand: 5.159 Zeilen, davon 4.517 HAM und 642 SPAM.
