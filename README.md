# LLM-Assisted Exploratory Data Analysis

A Python prototype that uses an LLM to turn a CSV file into a set of exploratory analyses, visualizations, and a narrative Markdown report. It combines deterministic data profiling with LLM-guided question generation and analysis-code generation.

## What it does

Given a CSV file, the script:

1. Reads the file and inspects its encoding, columns, data types, example values, summary statistics, and missing values.
2. Requests a set of data-specific analysis questions from an LLM.
3. Uses the LLM to suggest visualizations and generate Python code for each analysis.
4. Executes the generated analysis code, saves plots, and asks the model to summarize the resulting findings.
5. Writes the generated narrative and plot references to a `README.md` in the current working directory.

The analyses depend on the dataset and may include correlation, regression, clustering, outlier exploration, or time-based trends. This is an exploratory assistant, not an autonomous system for validating scientific conclusions.

## Example outputs

The repository includes sample reports and plots generated from different datasets:

- **[Goodreads](goodreads/README.md)** — book ratings, rating counts, outlier exploration, regression, and clustering.
- **[Happiness](happiness/README.md)** — life-satisfaction indicators, economic relationships, time trends, and clustering.
- **[Media](media/README.md)** — rating distributions, language comparisons, time trends, and clustering.

These are illustrative outputs. Treat generated interpretations as hypotheses to inspect, not verified causal conclusions.

## Tech stack

- Python, Pandas, NumPy, SciPy, Statsmodels, scikit-learn
- Matplotlib and Seaborn for plots
- LLM API calls for analysis-question generation, code generation, and narrative summaries
- The current script is configured to call the `gpt-4o-mini` model through an API proxy

## Run it

### Requirements

- Python 3.10 or newer
- An API-proxy token available in the `AIPROXY_TOKEN` environment variable
- `uv` is recommended because the script declares its Python dependencies in its header

Set your token in the shell:

```bash
export AIPROXY_TOKEN="your-token"
```

Do not commit the token to the repository or paste it into source code.

### Execute the script

Run the script from a **separate output directory**. It writes a `README.md` and numbered PNG plots to the current working directory; running it from the repository root could overwrite this project's README.

```bash
git clone https://github.com/frostysparkle/p2-tds.git
mkdir -p csv-analysis-output
cd csv-analysis-output
uv run ../p2-tds/autolysis.py /absolute/path/to/your-data.csv
```

Replace `/absolute/path/to/your-data.csv` with the path to your CSV file. Adjust the path to `autolysis.py` if you cloned the repository somewhere else. The generated report and plots will appear in `csv-analysis-output`.

## Limitations and safe use

- **Inspect outputs.** LLM-generated code and narrative summaries can be incorrect, brittle, or dependent on the prompt and dataset. Verify calculations and interpretations before relying on them.
- **Use a copy of your data.** The cleaning prompt asks the generated code to save the cleaned data under its original filename, so keep the source file backed up.
- **Avoid sensitive datasets.** Dataset summaries and samples are sent to the configured external API, and the generated Python is executed with `exec()`. Do not run this prototype on confidential data or in an environment where generated code can access sensitive files or credentials.
- **Review API transport before use.** The current script's configured proxy URL uses HTTP. Replace it with a trusted HTTPS endpoint before using any real credential in an environment where network traffic may be observed.
- **Reproducibility is limited.** Because analysis questions, code, and summaries are generated at runtime, the exact analyses and results may vary between runs.

## Project structure

```text
p2-tds/
├── autolysis.py          # CSV profiling, LLM orchestration, analysis and reporting
├── goodreads/            # Example report and plots
├── happiness/             # Example report and plots
└── media/                 # Example report and plots
```

## Future improvements

- Replace unrestricted code execution with a constrained set of reviewed analysis functions.
- Preserve the source file and write cleaned data to a new path.
- Add tests and fixed baselines for common analyses.
- Validate generated code and statistics before including them in reports.
- Use a configurable HTTPS API endpoint and improve error handling for malformed model responses.
