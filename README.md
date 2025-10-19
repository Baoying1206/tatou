# Tatou
A web platform for PDF watermarking.  
This project is intended for pedagogical use and contains known security vulnerabilities.  
Do **not** deploy it on an open or public network.

## Overview

Tatou is a Flask-based secure PDF watermarking system designed for the Software Security course at Stockholm University.  
It integrates the RMAP (Roger Michael Authentication Protocol) to enable secure communication and watermark sharing between servers.  
The system provides REST APIs for uploading, creating, and reading watermarked PDF files.

## Features

- RMAP-based authentication between Tatou instances
- Multiple watermarking techniques:
  - Invisible text watermark
  - XMP metadata watermark
  - Attachment-based watermark
- PDF upload, watermark embedding, and extraction
- Dockerized environment with MySQL/MariaDB backend
- Test coverage and mutation testing using `pytest` and `mutmut`

## Instructions

The following instructions assume you are using a Bash terminal on a Linux machine.  
If you are using another system, you may need to adapt the commands.

---

### Clone the repository

```bash
git clone https://github.com/nharrand/tatou.git
cd tatou

Note that you should probably fork the repo and clone your own repo.
```
---

### Run Python unit tests

```bash
cd tatou/server

# Create a python virtual environement
python3 -m venv .venv

# Activate your virtual environement
. .venv/bin/activate

# Install the necessary dependencies
python -m pip install -e ".[dev]"

# Run the unit tests
python -m pytest -v
```

### Generate coverage report
```bash
pytest --cov=server/src --cov-report=html
open htmlcov/index.html
```

### Run mutation tests
Create a setup.cfg file at the project root with:
```bash
[mutmut]
runner = pytest -q
tests_dir = server/test
paths_to_mutate = server/src
use_coverage = true
timeout = 15
exclude = server/test*,server/static*,server/storage*,server/logs*
Then run:
mutmut run
mutmut results
mutmut html
open html/index.html
```
---

### Deploy
From the root of the directory:
```bash
# Create a file to set environment variables like passwords.
cp sample.env .env

# Edit .env and pick the passwords you want.

# Rebuild the docker image and deploy the containers.
docker compose up --build -d

# Monitor logs in real time.
docker compose logs -f

# Test if the API is up.
http -v :5000/healthz

# Open your browser at 127.0.0.1:5000 to check if the website is up.
```

### Environment variables
Your .env file should include:
```bash
DB_HOST=127.0.0.1
DB_PORT=3306
DB_NAME=tatou
DB_USER=group8
DB_PASSWORD=<your-password>
TATOU_XMP_SECRET=test-secret-key
MAP_SERVER_KEYS_DIR=/app/secrets/server
MAP_CLIENT_KEYS_DIR=/app/secrets/clients
```
---
