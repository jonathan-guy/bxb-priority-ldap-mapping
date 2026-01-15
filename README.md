# BxB Priority Analyzer

Analyze employee annual summaries to identify which BxB (Block by Block) priority each person primarily focused on in the most recent quarter.

## Purpose

Automatically classify employees by their primary BxB priority focus based on their annual summary documents, then write the results back to a tracking Google Sheet.

---

## Data Sources

### Input: Employee Summary Documents
- **Location**: Google Drive (`.md` files)
- **Index Sheet**: [Employee Summary Links](https://docs.google.com/spreadsheets/d/1WOtLJbR4RBX_FjrlqpYPlnYOIyEVeOcoxzBpqBKLeGY/edit?gid=521409188#gid=521409188)
- **Format**: Markdown files containing annual summaries for each employee

### Reference: BxB Priorities
- **Source**: [BxB Priorities Document](https://docs.google.com/document/d/1uQp-27I8GqfuSjGdvoDoxREkiCCMes6lRY7tkihCDw0/edit?tab=t.0)
- **Structure**:
  - **Vector 1**: e.g., "Automate Block's Product"
  - **Vector 2**: (additional priorities)
  - **Vector 3**: (additional priorities)
  - **Constants**: Ongoing foundational work

### Output: Google Sheet
- **Destination**: Same tracking sheet (new column)
- **Content**: Primary BxB priority for each employee based on Q4 focus

---

## Workflow

```
┌─────────────────────┐     ┌─────────────────────┐     ┌─────────────────────┐     ┌─────────────────────┐
│  1. Read Index      │ ──▶ │  2. Fetch MD Files  │ ──▶ │  3. Analyze Content │ ──▶ │  4. Write Results   │
│  (Google Sheet)     │     │  (Google Drive)     │     │  (Python/LLM)       │     │  (Google Sheet)     │
└─────────────────────┘     └─────────────────────┘     └─────────────────────┘     └─────────────────────┘
```

### Step 1: Read Index Sheet
- Connect to Google Sheets API
- Read the employee list with links to their `.md` summary files
- Extract Google Drive file IDs from the links

### Step 2: Fetch MD Files
- Connect to Google Drive API
- Download each employee's `.md` summary file
- Parse the markdown content

### Step 3: Analyze Content
- Load the BxB priorities reference (Vector 1, 2, 3, Constants)
- For each employee summary:
  - Focus on the most recent quarter section
  - Identify work themes and accomplishments
  - Match against BxB priority descriptions
  - Determine the primary priority focus

### Step 4: Write Results
- Add a new column to the Google Sheet (e.g., "Q4 2025 BxB Priority")
- Write the identified priority for each employee
- Include confidence level or notes if applicable

---

## Quick Start

```bash
# 1. Clone the repository
git clone https://github.com/jonathan-guy/bxb-priority-analyzer.git
cd bxb-priority-analyzer

# 2. Set up Python environment
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# 3. Configure credentials (see Authentication section)
# Place credentials.json in project root

# 4. Run the analyzer
python scripts/analyze.py
```

---

## Authentication

This project requires Google API access for both Sheets and Drive.

### Google Cloud Setup

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project (or use existing)
3. Enable the following APIs:
   - Google Sheets API
   - Google Drive API
4. Create OAuth 2.0 credentials (Desktop application)
5. Download `credentials.json` to the project root

### First Run

On first run, the script will:
1. Open a browser for Google OAuth consent
2. Cache the token in `.credentials/` for future runs

---

## Project Structure

```
bxb-priority-analyzer/
├── README.md
├── requirements.txt
├── .gitignore
├── credentials.json          # OAuth credentials (gitignored)
│
├── scripts/
│   ├── analyze.py            # Main entry point
│   ├── fetch_summaries.py    # Download MD files from Drive
│   ├── classify_priority.py  # Analyze content & match priorities
│   └── write_results.py      # Update Google Sheet
│
├── config/
│   └── bxb_priorities.yaml   # BxB priority definitions
│
├── raw/                       # Downloaded MD files (gitignored)
│   └── *.md
│
├── output/                    # Analysis results (gitignored)
│   └── classifications.csv
│
└── .credentials/              # OAuth tokens (gitignored)
```

---

## Configuration

### Environment Variables

Create a `.env` file:

```bash
# Google Sheet IDs
EMPLOYEE_INDEX_SHEET_ID=1WOtLJbR4RBX_FjrlqpYPlnYOIyEVeOcoxzBpqBKLeGY
EMPLOYEE_INDEX_GID=521409188

# Optional: OpenAI API key for LLM-based classification
OPENAI_API_KEY=your-key-here
```

### BxB Priorities Config

The `config/bxb_priorities.yaml` file defines the priorities to match against:

```yaml
vector_1:
  - name: "Automate Block's Product"
    keywords: ["automation", "self-service", "reduce manual"]
    description: "Work focused on automating Block's products"

vector_2:
  # ... additional priorities

vector_3:
  # ... additional priorities

constants:
  # ... ongoing foundational work
```

---

## Classification Approach

The analyzer uses a combination of:

1. **Keyword Matching**: Scan for priority-specific terms
2. **Section Analysis**: Focus on Q4/recent quarter sections
3. **LLM Classification** (optional): Use GPT-4 for nuanced understanding

The output includes:
- Primary BxB priority
- Confidence score (high/medium/low)
- Supporting evidence (key phrases matched)

---

## Output

Results are written to the Google Sheet as a new column:

| Employee Name | ... existing columns ... | Q4 2025 BxB Priority |
|---------------|--------------------------|----------------------|
| Jane Smith    | ...                      | Vector 1: Automate Block's Product |
| John Doe      | ...                      | Vector 2: ... |

---

## TODO

- [ ] Set up Google API credentials
- [ ] Parse BxB priorities document into config
- [ ] Implement MD file fetching from Drive
- [ ] Build classification logic
- [ ] Test with sample summaries
- [ ] Write results back to Sheet

---

## License

Internal use only.
