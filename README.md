# PCA Portfolio Analysis

This project demonstrates how to apply **Principal Component Analysis (PCA)** to S&P 500 stock returns to extract the main factors driving market movements and reduce dimensionality for portfolio analysis.

---

## Table of Contents

- [Dataset](#dataset)
- [What is PCA?](#what-is-pca)
- [Mathematical Foundation](#mathematical-foundation)
- [When to Use PCA](#when-to-use-pca)
- [Project Structure](#project-structure)
- [Key Findings](#key-findings)
- [Installation](#installation)
- [Usage](#usage)

---

## Dataset

### Data Source
- **Stock prices:** Yahoo Finance API via `yfinance` library
- **S&P 500 constituents:** Wikipedia ([List of S&P 500 companies](https://en.wikipedia.org/wiki/List_of_S%26P_500_companies))

### Data Specifications
- **Time period:** 5 years (2020-2025)
- **Frequency:** Weekly returns
- **Number of stocks:** 491 S&P 500 companies
- **Number of observations:** 252 trading weeks

### Data Collection Method
```python
# S&P 500 company list scraped from Wikipedia
url = "https://en.wikipedia.org/wiki/List_of_S%26P_500_companies"
headers = {"User-Agent": "Mozilla/5.0"}
response = requests.get(url, headers=headers)
sp500_table = pd.read_html(StringIO(response.text))[0]

# Historical price data downloaded via yfinance
data = yf.download(tickers, start=start_date, end=end_date)
```

---

## What is PCA?

Principal Component Analysis (PCA) constructs a **new basis of a vector space** consisting of **linearly independent, orthogonal vectors**, such that with as few basis vectors as possible, the **maximum variance (information) of the data** is preserved.

- **Original basis:** The original variables (stock returns)
- **New basis:** Principal components (eigenvectors)

---

## Mathematical Foundation

### Conceptual Pipeline

```
Raw Data 
  ↓
Covariance Matrix 
  ↓
Eigenvalue Decomposition 
  ↓
Principal Components (ranked by explained variance)
  ↓
Dimensionality Reduction
```

### Geometric Interpretation

**1. Original Data Space**
- 491-dimensional space (one dimension per stock)
- Each trading day = one point in this high-dimensional space
- 1,216 points scattered in 491 dimensions

**2. Covariance Matrix**
- Size: 491 × 491
- Measures how each pair of stocks co-varies
- Properties: Symmetric, positive semi-definite
- Diagonal elements: Individual stock variances
- Off-diagonal elements: Covariances between stocks

**3. Eigenvalue Decomposition**
- **Eigenvalues:** Amount of variance captured in each direction
- **Eigenvectors:** Directions of maximum variance
- Sorted by eigenvalue magnitude (largest first)
- Largest eigenvalue → PC1 (most important direction, often market factor)

**4. Principal Components**
- New coordinate system aligned with variance structure
- **PC1:** Direction of maximum variance (typically market factor)
- **PC2:** Second most variance (orthogonal to PC1)
- **PC3, PC4, ...:** Progressively less variance, all mutually orthogonal

**5. Dimensionality Reduction**
- Retain only top K components
- Discard components with small eigenvalues (noise)
- Result: 491 dimensions → K dimensions
- Trade-off: Simplicity vs. information preservation

**6. Orthogonality Property**
- All principal components are uncorrelated by construction
- Eigenvectors are mathematically orthogonal
- Result: Independent risk factors for portfolio analysis

---

## When to Use PCA

### PCA Works Well When:
- Strong correlations exist between variables
- Variance is concentrated in a few directions
- Data has underlying linear structure

### PCA Works Poorly When:
- All variables are equally important
- No clear correlation structure exists
- Information exists in non-linear relationships
- Data is sparse or has many zeros

---

## Project Structure

```
pca-portfolio-analysis/
├── data/                           # Raw and processed data files
├── notebooks/                      # Jupyter notebooks
│   ├── 01_data_collection.ipynb   # S&P 500 data retrieval
│   ├── 02_exploratory_analysis.ipynb  # EDA and correlation analysis
│   └── 03_pca_analysis.ipynb      # PCA implementation and results
├── results/                        # Output files
│   └── figures/                    # Generated visualizations
├── images/                         # Images for README
├── requirements.txt                # Python dependencies
├── .gitignore                      # Git ignore rules
└── README.md                       # This file
```

---

## Key Findings

### Return Characteristics

**Weekly Return Statistics (Sample):**
| Stock | Mean | Std Dev | Min | Max |
|-------|------|---------|-----|-----|
| AAPL | 0.37% | 3.74% | -13.55% | 13.33% |
| A | 0.12% | 3.88% | -13.44% | 15.02% |
| ABBV | 0.43% | 3.21% | -17.30% | 10.17% |

### Market Dynamics Over Time

![Mean Weekly Return & Cross-sectional Volatility](images/weekly_return_volatility.png)

**Key Observations:**
- **Cross-sectional volatility** (purple) remains relatively stable around 3-5%
- **Mean weekly returns** (pink) fluctuate around zero with high amplitude
- **Volatility spikes** visible in early 2022 and late 2025 (macro events)
- **Regime shifts** apparent: 2022 bear market shows increased dispersion
- Market exhibits both systematic movements (visible in mean) and idiosyncratic variation (cross-sectional volatility)

### Correlation Structure
The correlation analysis reveals strong co-movement patterns among S&P 500 stocks:

![Correlation Matrix - All Stocks](images/corr_matrix_allstocks.png)

![Correlation Matrix - 50 Stocks Sample](images/corr_matrix_50stocks.png)

![Distribution of Correlations](images/distribution_of_corr.png)

**Observations:**
- Strong positive correlations exist between most stocks
- Sector-based clustering is visible
- High correlation justifies PCA application

---

## Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/pca-portfolio-analysis.git
cd pca-portfolio-analysis

# Create virtual environment (optional but recommended)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

---

## Usage

Run the Jupyter notebooks in order:

```bash
jupyter notebook
```

1. **01_data_collection.ipynb** - Download S&P 500 stock data
2. **02_exploratory_analysis.ipynb** - Explore returns and correlations
3. **03_pca_analysis.ipynb** - Apply PCA and interpret results

---

## License

[Add your license here]

---

## Contact

[Add your contact information or link to your profile]