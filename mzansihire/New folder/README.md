# MzansiHire

**AI-assisted job matching for South African graduates and young job seekers.**
Upload a CV or list your skills, and MzansiHire ranks current South African vacancies by how well they fit, shows the skills you are missing, and helps you apply and keep track.

- **Live app:** [paste your Streamlit link here]
- **Team:** Sky Blue ([add member names])
- **Course:** BSc Computer Science, Artificial Intelligence module (CSIT 3rd-year project 2026)

---

## 1. The problem

Youth unemployment in South Africa is high, and graduates waste hours reading vacancies that do not fit their skills. MzansiHire reads the text of a job seeker's profile and of job adverts and matches the two.

## 2. Features

| Feature | What it does |
|---|---|
| Sign up and profile | Account with hashed password, skills, qualification, interests, profile picture |
| CV upload | Reads a PDF or DOCX in memory, detects skills and adds them to the profile (the file is not stored) |
| Job matches | Ranks live South African vacancies (Adzuna API) by TF-IDF cosine similarity, with filters |
| Skill gaps | Shows skills that top jobs mention but the profile lacks, with short-course search links |
| CV feedback | A 12-point checklist of CV good practice, plus a comparison of the CV with a pasted job advert |
| Apply and track | Apply opens the original advert; save jobs and mark them as applied |
| About page | Plain-language explanation of the project and privacy |

## 3. How the AI works

1. **Text cleaning:** lowercase, letters only, tidy whitespace (same function in the notebook and the app).
2. **TF-IDF:** `TfidfVectorizer(stop_words="english", max_features=5000)` is **fitted once** on the job postings.
3. **Matching:** a user profile is cleaned, transformed with the already-fitted vectoriser (never refitted) and compared with every job by **cosine similarity**. The top jobs are shown.
4. **Supervised experiments (notebook):** a classifier that predicts a job's field from its text, used to train, tune and test properly.

### Results (from `MzansiHire_ML_Experiments.ipynb`)

- Data: 6,328 LinkedIn job postings, cleaned to **5,446 jobs in 16 fields** (759 repeated adverts removed, label never used as an input, rare fields merged into "Other").
- Split: 70% train / 15% validation / 15% test (stratified, seed 42). The test set is used once.
- Models compared: baseline, Naive Bayes, Logistic Regression, Linear SVM, Random Forest, neural network (MLP), tuned with cross-validation.
- **Final model (tuned linear SVM) on the unseen test set:** accuracy **68.7%** (baseline 19.0%), macro-F1 **0.667**, macro precision 0.688, macro recall 0.658, ROC-AUC (one-vs-rest) **0.920**.
- **Recommender (same-field relevance proxy):** Precision@5 **0.478** against 0.094 for random jobs; Hit@5 81.6% against 37.0%.
- Re-run the notebook to reproduce every number.

## 4. Tech stack

Python 3.10+, Streamlit, scikit-learn, pandas, SQLite, bcrypt, pypdf, python-docx, Pillow, requests.

## 5. Project structure

```
app.py                 Streamlit pages and user flow
recommender.py         TF-IDF + cosine-similarity matcher
adzuna.py              Fetches and caches live South African jobs (Adzuna API)
cv.py                  CV reading (PDF/DOCX) and skill detection
cvfeedback.py          CV checklist and CV-vs-job comparison
skillgap.py            Missing skills and course links
tracker.py             Saved and applied jobs
db.py                  Users and profiles (SQLite, bcrypt)
styles.py, about.py    Look and feel, About page
assets/                Photos used by the pages
data/                  LinkedIn_RDB_three.csv(.gz) (fallback jobs) and the SQLite database (created at run time)
tests/test_core.py     55 automated tests
MzansiHire_ML_Experiments.ipynb   Data cleaning, EDA, features, models, tuning, results
manual_review_template.csv        Template for the manual relevance review
```

## 6. Run it on your computer

```bash
pip install -r requirements.txt
```

1. Put `LinkedIn_RDB_three.csv` (or `LinkedIn_RDB_three.csv.gz`) in the `data/` folder.
2. Create `.streamlit/secrets.toml` (copy `secrets.toml.example`) and add your free Adzuna keys from developer.adzuna.com:
   ```toml
   ADZUNA_APP_ID = "your-id"
   ADZUNA_APP_KEY = "your-key"
   ```
3. Start the app:
   ```bash
   python -m streamlit run app.py
   ```
4. Sign up, upload a CV, open **Job matches** and click **Load latest SA jobs**.

Without Adzuna keys the app still works, using the LinkedIn dataset as a fallback.

## 7. Run the tests

```bash
python -m unittest discover -s tests -v
```

The tests use a temporary database and a fake Adzuna server, so no internet or keys are needed.

## 8. Deploy online (Streamlit Community Cloud)

1. Put the project on GitHub (do **not** upload `.streamlit/secrets.toml` or `data/mzansihire.db`; `.gitignore` already excludes them).
2. At share.streamlit.io, create an app from the repository, branch `main`, main file `app.py`.
3. In **Advanced settings > Secrets**, paste the two Adzuna lines from step 6.
4. Deploy. Note: accounts and cached jobs are stored on the app's disk and reset when the app restarts.

## 9. Privacy and security

- Passwords are hashed with bcrypt.
- Uploaded CVs are checked (type, first bytes, size, page count), read in memory and **not stored**.
- Name, picture and email are never used to match jobs.
- API keys are kept in a secrets file and never shown in error messages.

## 10. Limitations

- The LinkedIn dataset is mostly from the USA; it is used to develop and evaluate the methods. Live South African jobs come from Adzuna.
- Job-field labels overlap and are noisy; "same field" is only a proxy for relevance.
- Adzuna descriptions are short previews, so live matching has less text to use.
- A word-based matcher cannot judge seniority. The skills list is limited, and the CV checklist is general advice, not an ATS score.
- The manual relevance review (see `manual_review_template.csv`) is still to be completed.

## 11. Next steps

South African training data, user feedback as relevance labels, sentence embeddings, seniority-aware ranking, approximate nearest-neighbour search for scale, and POPIA features (delete and export my data).

## 12. Data sources and credits

- Job postings (prototype): LinkedIn job listings dataset from Kaggle, file `LinkedIn_RDB_three.csv` ([add the exact Kaggle dataset link]).
- Live South African vacancies: **Jobs by [Adzuna](https://www.adzuna.co.za)** (used under their API terms; attribution shown in the app).
- Course links open search pages on Coursera, edX, Udemy and YouTube and are not endorsements.
