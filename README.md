# Flipkart Review Scraper

A small Flask web app that takes a product search term, scrapes customer reviews of the top Flipkart result, shows them in a results page and stores them in MongoDB. Configured for AWS Elastic Beanstalk.

## What it does
1. `/` shows a search form.
2. `POST /review` builds `https://www.flipkart.com/search?q=<term>`, picks the first product box, opens the product page and parses review blocks with BeautifulSoup (name, rating, heading, comment).
3. Writes a `<term>.csv` locally, inserts all reviews into MongoDB (`review_scrap` db, `review_scrap_data` collection) and renders `results.html`.

## Tech stack
Python 3.7 (Elastic Beanstalk platform), Flask 1.1.2, Flask-Cors, BeautifulSoup4, requests, PyMongo, gunicorn; HTML/CSS templates.

## Structure
```
application.py            # Flask app (exposes `application` for Elastic Beanstalk)
templates/                # base, index, results
static/css/               # style.css, main.css
requirements.txt
.ebextensions/python.config   # WSGIPath: application:application
.elasticbeanstalk/config.yml  # app name "reviewflask", us-east-1, Python 3.7
```

## Run locally
```bash
pip install -r requirements.txt
python application.py        # serves on 127.0.0.1:8000
```
The MongoDB connection string in `application.py` is hard-coded with a `<password>` placeholder; replace it (ideally read from an environment variable) before use. Deploy with `eb init` / `eb deploy` using the included config.

## Limitations
- Relies on Flipkart's obfuscated CSS class names (e.g. `_1AtVbE`, `_16PBlm`), which change often, so the scraper will likely be broken today.
- Only the first page of reviews of one product; broad `except` blocks hide errors; unused debugging `print(prod_html)`.
- Pinned 2020-era dependencies; Mongo credentials are embedded in source rather than configured.
