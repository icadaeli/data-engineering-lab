# Data Engineering Lab

A hands-on learning repository for practising Python, SQL, PostgreSQL, Docker, testing, and data engineering workflows.

## Current Features

- Python project managed with `uv`
- Source-layout Python package
- Automated testing with `pytest`
- PostgreSQL 17 managed through Docker Compose
- Environment-based database configuration
- PostgreSQL healthcheck
- Persistent database storage using a named Docker volume
- Git branch, merge-conflict, pull request, and tagging practice

## Project Structure

```text
data-engineering-lab/
├── data/                       # Local practice datasets
├── docs/                       # Project and workflow documentation
├── sql/                        # SQL scripts and exercises
├── src/
│   └── data_engineering_lab/   # Python package
├── tests/                      # Automated tests
├── .env.example               # Safe environment-variable template
├── .gitignore                 # Files excluded from Git
├── .python-version            # Project Python version
├── compose.yaml               # PostgreSQL Docker Compose configuration
├── pyproject.toml             # Python project and tool configuration
├── README.md                  # Project documentation
└── uv.lock                    # Locked Python dependencies
```

## Prerequisites

Install the following tools before running the project:

- Git
- Python 3.13
- `uv`
- Docker Desktop
- Optional: DBeaver for graphical database access

Verify the required command-line tools:

```bash
git --version
uv --version
docker --version
docker compose version
```

## Initial Setup

Clone the repository and enter its directory:

```bash
git clone git@github.com:icadaeli/data-engineering-lab.git
cd data-engineering-lab
```

Install the Python project and development dependencies:

```bash
uv sync
```

Create the local environment file from the safe template:

```bash
cp .env.example .env
```

Open `.env` and replace the example password with a unique local development password.

Do not commit `.env`. It is intentionally excluded by `.gitignore`.

## Run PostgreSQL

Make sure Docker Desktop is running, then validate the Compose configuration:

```bash
docker compose config --quiet
```

Start PostgreSQL and wait for its healthcheck:

```bash
docker compose up -d --wait
```

Check the container:

```bash
docker compose ps
```

Verify that PostgreSQL accepts connections:

```bash
docker compose exec postgres pg_isready \
  -U data_engineer \
  -d data_engineering_lab
```

## Connect to PostgreSQL

The default local connection settings are:

| Setting | Value |
|---|---|
| Host | `localhost` |
| Port | `5433` |
| Database | `data_engineering_lab` |
| Username | `data_engineer` |
| Password | Value stored locally in `.env` |

The host uses port `5433`, while PostgreSQL runs on port `5432` inside the container.

Connect through the terminal:

```bash
docker compose exec postgres psql \
  -U data_engineer \
  -d data_engineering_lab
```

Exit the PostgreSQL prompt with:

```text
\q
```

## Persistent Storage

PostgreSQL data is stored in the Compose-managed named volume:

```text
data-engineering-lab_postgres_data
```

Stopping and recreating the container does not remove this volume:

```bash
docker compose down
docker compose up -d --wait
```

Do not use the following command unless you intentionally want to delete the project database:

```bash
docker compose down -v
```

The `-v` option removes the named volume and its stored data.

## Run the Python Example

Run the example package function:

```bash
uv run python -c "from data_engineering_lab import hello; print(hello())"
```

Expected output:

```text
Hello from data-engineering-lab!
```

## Run Tests

Run the automated test suite:

```bash
uv run pytest
```

Expected result:

```text
1 passed
```

## Stop PostgreSQL

Stop and remove the Compose container and network while preserving database data:

```bash
docker compose down
```

Start it again later with:

```bash
docker compose up -d --wait
```

## Troubleshooting

### Docker daemon is unavailable

Open Docker Desktop, wait until the Docker engine is ready, and retry:

```bash
docker info
```

### Port 5433 is already in use

Check which containers are running:

```bash
docker ps
```

Either stop the conflicting service or change `POSTGRES_PORT` in `.env`.

### PostgreSQL is still starting

Inspect its health status and recent logs:

```bash
docker compose ps
docker compose logs --tail 50 postgres
```

Then wait for the healthcheck:

```bash
docker compose up -d --wait
```

### Python package cannot be imported

Synchronise or reinstall the local project package:

```bash
uv sync --reinstall-package data-engineering-lab
```

Then verify the import:

```bash
uv run python -c "from data_engineering_lab import hello; print(hello())"
```

### Tests fail during import

Confirm that `pyproject.toml` contains:

```toml
[tool.pytest.ini_options]
pythonpath = ["src"]
```

Then run:

```bash
uv sync
uv run pytest
```

## Learning Roadmap

This repository will gradually include:

- SQL exercises and database design
- Python data-processing practice
- API ingestion
- ETL and ELT pipelines
- Data quality checks
- dbt transformations
- Airflow orchestration
- Cloud and infrastructure practice
- Portfolio-ready data engineering projects

## Security Notes

- Never commit `.env`, passwords, private keys, or production credentials.
- Keep only safe placeholder values in `.env.example`.
- Use separate credentials for local development and production.
- Review staged changes before every commit.

## Status

The repository currently provides a verified Python, Git, Docker Compose, and PostgreSQL development foundation. Additional data engineering exercises and projects will be added progressively.
