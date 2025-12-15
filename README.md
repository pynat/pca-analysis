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
- 252 points scattered in 491 dimensions

**2. Covariance Matrix**
- Size: 491 × 491
- Measures how each pair of stocks co-varies
- Properties: Symmetric, positive semi-definite
- Diagonal: Individual stock variances | Off-diagonal: Pairwise covariances

**3. Eigenvalue Decomposition**
- **Eigenvalues:** Amount of variance captured in each direction
- **Eigenvectors:** Directions of maximum variance
- Sorted by eigenvalue magnitude (largest first)
- Largest eigenvalue → PC1 (most important direction, typically market factor)

**4. Principal Components**
- New coordinate system aligned with variance structure
- **PC1:** Direction of maximum variance (market factor)
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

![Mean Weekly Return & Cross-sectional Volatility](images/weekly_return.png)

**Key Observations:**
- **Cross-sectional volatility** (purple) remains stable around 3-5%
- **Mean weekly returns** (pink) fluctuate around zero with high amplitude
- **Volatility spikes** visible in early 2022 and late 2025 (macro events)
- **Regime shifts** apparent: 2022 bear market shows increased dispersion
- Market exhibits both systematic movements (mean) and idiosyncratic variation (cross-sectional volatility)

---

### Correlation Structure

The correlation analysis reveals strong co-movement patterns among S&P 500 stocks, validating the application of PCA for dimensionality reduction.

#### Correlation Matrices

![Correlation Matrix - All Stocks](images/corr_matrix_allstocks.png)
*Full correlation matrix showing sector clustering patterns*

![Correlation Matrix - 50 Stocks Sample](images/corr_matrix_50stocks.png)
*Detailed view: Block-diagonal structure indicates sector groupings*

**Key Insights:**
- **Sector clustering:** Stocks within the same industry show higher correlations (darker blocks)
- **Market co-movement:** Light purple baseline confirms broad systematic risk
- **Diversification opportunities:** Low-correlation pairs (lighter areas) signal hedging potential
- **Risk concentration:** High-correlation clusters highlight vulnerability during market stress

#### Distribution of Pairwise Correlations

![Distribution of Correlations](images/distribution_of_corr.png)

**Statistical Summary:**

| Metric | Value | Interpretation |
|--------|-------|----------------|
| **Mean** | 0.284 | Moderate positive correlation indicates systematic market risk |
| **Median** | 0.280 | Close to mean → symmetric distribution |
| **Std Dev** | 0.140 | Sufficient variation for strategic pair selection |
| **Pairs >0.7** | 643 (0.5%) | Very few extreme correlations → diversification is effective |

**Key Takeaways:**
- **Moderate co-movement:** On average, two random stocks move together 28.4%
- **Stable structure:** Standard deviation of 0.14 indicates consistent correlation patterns
- **Normal distribution:** Predictable patterns enable reliable risk modeling
- **Diversification viable:** Only 0.5% of pairs are highly correlated (>0.7)

---

### Why PCA for This Data?

The correlation structure makes PCA particularly effective for this dataset:

**Rationale:**
1. **Dimensionality reduction:** Compress 491 stocks → few principal components while preserving variance
2. **Moderate correlation (0.284):** Indicates co-movement but not redundancy—ideal for PCA
3. **Factor identification:** Extract common drivers (market factor, sector trends) from return patterns
4. **Noise separation:** Distinguish systematic risk factors from stock-specific noise
5. **Computational efficiency:** Enable faster portfolio optimization on reduced feature space
6. **Stable structure:** Consistent correlations (σ=0.14) yield robust principal components

**Expected Outcomes:**
- PC1 will likely capture broad market movements (~30-40% variance)
- Subsequent PCs will represent sector-specific or style factors
- 10-20 components should explain 80%+ of total variance

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