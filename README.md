# Mzanzi-Hire

# MzansiHire 🇿🇦
### AI-Assisted Job Matching Platform for South African Graduates

MzansiHire is a Python-based application that helps South African graduates and young job seekers discover relevant employment opportunities. It analyses user profiles and CVs, then ranks available vacancies based on skills and job-description similarity.

## Features

- **User Profiles:** Register and manage qualifications, skills and career interests.
- **CV Processing:** Upload PDF or DOCX CVs for text extraction and skill identification.
- **Job Matching:** Rank vacancies using TF-IDF and cosine similarity.
- **Live Job Listings:** Retrieve South African vacancies through the Adzuna API.
- **Skills Gap Analysis:** Identify skills missing from relevant job opportunities.
- **CV Feedback:** Review CVs using a 12-point checklist and compare them with job advertisements.
- **Application Tracking:** Save vacancies and mark applications as submitted.

## How It Works

1. Users create a profile or upload a CV.
2. The application extracts relevant text and identifies skills.
3. Job advertisements are retrieved from Adzuna or a local dataset.
4. TF-IDF converts job descriptions and user profiles into numerical vectors.
5. Cosine similarity calculates textual similarity.
6. Vacancies are ranked and displayed to the user.

## Machine Learning

The project includes a separate notebook (`MzansiHire_ML_Experiments.ipynb`) exploring job-field classification and recommendation techniques.

**Dataset:** 6,328 original LinkedIn job postings, cleaned to 5,446 records across 16 occupational fields.

**Models evaluated:** Naive Bayes, Logistic Regression, Linear SVM and Neural Network (MLP).

**Final tuned Linear SVM results:**

| Metric | Result |
|---|---:|
| Accuracy | 68.7% |
| Macro F1-score | 0.667 |
| Macro Precision | 0.688 |
| Macro Recall | 0.658 |
| ROC-AUC | 0.920 |

The recommender achieved Precision@5 of 0.478 and Hit@5 of 81.6% using a same-field relevance proxy.

*Note: The experimental dataset is primarily US-based. These metrics do not establish real-world suitability for individual South African job seekers.*

## Technology Stack

| Technology | Purpose |
|---|---|
| Python | Application development |
| Streamlit | User interface |
| Scikit-learn | Machine learning and NLP |
| pandas | Data processing |
| SQLite | Database |
| bcrypt | Password hashing |
| pypdf / python-docx | CV text extraction |
| Adzuna API | Live job listings |

## Project Structure

```text
MzansiHire/
├── app.py
├── recommender.py
├── adzuna.py
├── cv.py
├── cvfeedback.py
├── skillgap.py
├── tracker.py
├── db.py
├── styles.py
├── about.py
├── assets/
├── data/
├── tests/
│   └── test_core.py
├── MzansiHire_ML_Experiments.ipynb
├── requirements.txt
└── README.md
```

## Installation

**1. Clone the repository**

```bash
git clone YOUR_REPOSITORY_URL
cd MzansiHire
```

**2. Create and activate a virtual environment**

```bash
python -m venv venv
venv\Scripts\activate
```

**3. Install dependencies**

```bash
pip install -r requirements.txt
```

**4. Configure Adzuna API credentials**

Create `.streamlit/secrets.toml`:

```toml
ADZUNA_APP_ID = "YOUR_APP_ID"
ADZUNA_APP_KEY = "YOUR_APP_KEY"
```

**5. Start the application**

```bash
python -m streamlit run app.py
```

Without API credentials, the application can use its local fallback dataset.

## Testing

Run the automated test suite:

```bash
python -m unittest discover -s tests -v
```

The project includes 55 automated tests using a temporary database and simulated API server.

## Security and Privacy

- Passwords are hashed using bcrypt.
- Uploaded CVs are processed in memory and not permanently stored.
- Uploaded files undergo validation.
- Personal identifiers are excluded from the job-matching process.
- API credentials are managed through Streamlit secrets.

## Limitations and Future Development

Current limitations include limited South African training data, short job descriptions, vocabulary-based skill detection and the inability of text similarity to fully assess experience or seniority.

Potential improvements include semantic embeddings, better South African job-market datasets, user-feedback-based recommendations, improved relevance evaluation and enhanced POPIA-related data controls.

## Author

**Thabang Mokobodi**  
Computer Science & Statistics Graduate | South Africa

GitHub: [Your GitHub Profile]

---

*MzansiHire is an independent portfolio project. Its recommendations provide guidance and do not guarantee employment or successful applications.*
