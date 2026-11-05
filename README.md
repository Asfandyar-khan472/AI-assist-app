# AI ATS Resume Checker

Upload a resume (PDF, DOCX or TXT) and get an estimated **ATS score**, a score breakdown, missing keywords, and a prioritized list of concrete fixes. Built with **Streamlit** and **Google Gemini Flash**.

## Features

- Reads PDF, DOCX and TXT resumes (up to 5 MB)
- Optional job description: paste a posting to see how well your resume matches it
- Overall ATS score plus three sub-scores: format, content, keywords
- Prioritized improvements (High / Medium / Low) with example rewrites
- Matched and missing keywords
- Suggested professional summary and stronger bullet points
- Downloadable Markdown report
- If the AI call fails, you still get the local format checks

## How the score works

| Part | Weight | Who calculates it | What it checks |
|------|--------|-------------------|----------------|
| Format & structure | 30% | Python (local rules) | Email, phone, link, standard section headings, length, bullets, action verbs, quantified results |
| Content & impact | 35% | Gemini | Clarity, relevance, achievements, grammar |
| Keywords & relevance | 35% | Gemini | Match against your job description, or against the typical needs of the detected role |

**Important:** no real ATS publishes a universal score. Treat the number as a guide for what to fix, not as a prediction of a specific employer's system.

## Project structure

```
ats-resume-checker/
├── app.py            # the whole app
├── requirements.txt  # Python dependencies
├── README.md
└── .gitignore        # keeps secrets and junk out of Git (see below)
```

## Run it locally

1. **Get a free Gemini API key** at <https://aistudio.google.com/apikey>.
2. **Create a virtual environment and install dependencies** (Python 3.10+):

   ```bash
   python -m venv .venv
   source .venv/bin/activate        # Windows: .venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. **Give the app your key** (pick one):

   - Paste it into the sidebar when the app runs, or
   - Set an environment variable:

     ```bash
     export GEMINI_API_KEY="your-key"        # Windows PowerShell: $env:GEMINI_API_KEY="your-key"
     ```

   - Or create `.streamlit/secrets.toml` (never commit this file):

     ```toml
     GEMINI_API_KEY = "your-key"
     ```

4. **Start the app:**

   ```bash
   streamlit run app.py
   ```

   It opens at <http://localhost:8501>.

## Change the Gemini model

The default is `gemini-2.5-flash`. To use another Flash model, either type its name under **Advanced** in the sidebar, or set `GEMINI_MODEL` (environment variable or Streamlit secret). Current model names are listed at <https://ai.google.dev/gemini-api/docs/models>.

## Push the project to GitHub

1. Create a `.gitignore` file in the project folder with this content:

   ```
   .venv/
   __pycache__/
   .env
   .streamlit/secrets.toml
   ```

2. Create an empty repository on GitHub (**New repository**). Do not tick "Add a README", because you already have one.
3. In your project folder, run:

   ```bash
   git init
   git add app.py README.md requirements.txt .gitignore
   git commit -m "Initial commit: AI ATS resume checker"
   git branch -M main
   git remote add origin https://github.com/<your-username>/ats-resume-checker.git
   git push -u origin main
   ```

   GitHub no longer accepts account passwords for `git push` over HTTPS. When prompted, use a [personal access token](https://github.com/settings/tokens), or sign in with GitHub Desktop, the GitHub CLI (`gh auth login`), or SSH.

4. **Never commit your API key.** If you do by accident, delete the key in Google AI Studio and create a new one. Removing it from the file is not enough because it stays in Git history.

## Deploy on Streamlit Community Cloud (free)

1. Go to <https://share.streamlit.io> and sign in with GitHub. Allow access to your repository when asked.
2. Click **Create app** and choose to deploy from an existing GitHub repo.
3. Fill in:
   - **Repository:** `<your-username>/ats-resume-checker`
   - **Branch:** `main`
   - **Main file path:** `app.py`
4. Open **Advanced settings**, and in the **Secrets** box add:

   ```toml
   GEMINI_API_KEY = "your-key"
   ```

   Optionally pick a Python version (3.11 or 3.12 works well).
5. Click **Deploy**. The first build takes a few minutes.
6. To update the app later, commit and `git push`. Streamlit redeploys automatically. To change secrets later, use the app's **Settings → Secrets**.

**Public app, shared key:** if you set `GEMINI_API_KEY` as a secret, every visitor uses your quota. If you do not want that, leave the secret out and let visitors paste their own key in the sidebar, or set usage limits for your key in Google AI Studio / Google Cloud.

## Troubleshooting

| Problem | Fix |
|---------|-----|
| "Gemini rejected the API key" | Check the key, and that it has no extra spaces or quotes. |
| "The model name was not found" | Fix the name under **Advanced** in the sidebar. |
| "Rate limit or quota reached" | Wait a minute, or check quota in Google AI Studio. |
| "Almost no readable text was found" | Your file is probably a scan or image. Export a text-based PDF or DOCX. |
| Missing sections or bullets in the format checks | Use standard headings (Experience, Education, Skills) and real bullet characters. |
| `ModuleNotFoundError` on deploy | Make sure `requirements.txt` is in the repository root and was pushed. |

## Privacy

Your resume text is sent to Google's Gemini API for analysis, and nothing is stored by this app beyond your current browser session. Under Google's terms, content sent through free-tier Gemini API usage may be used to improve their products, so check the current terms before uploading real resumes, especially other people's.

## Limitations

- The section, bullet and action-verb checks are heuristics. Unusual layouts, multi-column PDFs and creative templates can confuse them, and real ATS parsers struggle with those layouts too.
- AI feedback can be wrong or generic. Review every suggestion, and never add skills or numbers that are not true.
- Scanned or image-only resumes cannot be analyzed.
