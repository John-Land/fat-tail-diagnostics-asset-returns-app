# Historic Asset Return Fat Tail Diagnostics Tool

A quantitative analysis application built with Streamlit, Plotly, SciPy, and NumPy.

This platform evaluates the empirical return distributions of financial assets, replacing standard Gaussian (Normal) assumptions with Extreme Value Theory (EVT) and heavy-tailed analytics. By implementing parametric model fitting (Student-t, Generalized Pareto Distribution) alongside robust empirical diagnostics (Zipf, Maximum-to-Sum, and Mean Excess limits), this tool provides a rigorous framework for identifying, classifying, and measuring structural tail risk in physical markets.

\---

## 📐 Theoretical Framework: Extremistan vs. Mediocristan

Classical financial engineering fundamentally assumes that asset returns reside in "Mediocristan"—a domain governed by the Law of Large Numbers and the Central Limit Theorem, where extreme events dampen out exponentially and risk can be fully described by a finite variance ($\\sigma^2$).

In reality, financial markets reside in "Extremistan"—a domain characterized by power-law dynamics, volatility clustering, and catastrophic jumps. In these environments, standard assumptions fail:

1. **Infinite Moments:** Financial asset returns often lack finite fourth moments (kurtosis) and occasionally lack finite second moments (variance). In these cases, historical sample standard deviations are mathematically meaningless and highly unstable.
2. **Asymmetric Tail Decay:** The probability of market crashes (left tail) and parabolic squeezes (right tail) decay at completely different rates, requiring independent, asymmetric evaluation frameworks.
3. **Pre-Asymptotic Environments:** Even when moments technically exist, the convergence rate is so agonizingly slow that empirical samples completely misrepresent true long-term risk limits.

This tool systematically diagnoses these properties to determine exactly how "fat" an asset's return distribution truly is.

\---

## 🧠 Comprehensive Model Hierarchy \& Mathematical Formulations

The platform analyzes asset returns using two distinct but complementary analytical approaches: **Parametric Model Fitting** and **Empirical Tail Diagnostics**.

### 1\. Parametric Modeling: Student-t Distribution

Standard normal distributions impose an impossibly thin exponential boundary on tail events. The Student-t distribution introduces the Degrees of Freedom parameter ($\\nu$ or $df$) to control the probability mass located in the tails.

**Mathematical Definition:**
The Probability Density Function (PDF) is given by:

$$
f(x) = \\frac{\\Gamma((\\nu+1)/2)}{\\sigma \\sqrt{\\nu \\pi} \\Gamma(\\nu/2)} \\left(1 + \\frac{(x-\\mu)^2}{\\nu \\sigma^2}\\right)^{-(\\nu+1)/2}
$$

**Diagnostic Significance:**

* $\\nu \\ge 30$: Distribution converges to Gaussian (Thin tails).
* $\\nu \\le 4$: The 4th moment (kurtosis) becomes infinite. Variance exists, but outlier events dominate structural tail risk.
* $\\nu \\le 2$: The 2nd moment (variance) becomes strictly infinite.

### 2\. Parametric Modeling: Generalized Pareto Distribution (POT)

Extreme Value Theory (EVT) dictates that exceedances over a sufficiently high threshold ($u$) follow a Generalized Pareto Distribution (GPD). This platform uses the Peaks-Over-Threshold (POT) method, isolating the top and bottom 5% of daily returns.

**Mathematical Definition:**
For exceedances $y = x - u$ where $x > u$, the GPD is defined as:

$$
G\_{\\xi, \\beta}(y) = 1 - \\left(1 + \\frac{\\xi y}{\\beta}\\right)^{-1/\\xi}
$$

**Diagnostic Significance (The Tail Index $\\xi$):**

* $\\xi \\le 0$: Thin tails (Normal/Exponential decay bounds).
* $0 < \\xi < 0.5$: Heavy tails with power-law decay. Structural jumps are far more likely than standard distributions assume.
* $\\xi \\ge 0.5$: Infinite variance. Historical maximums do not statistically bound future extreme shocks.

\---

### 3\. Empirical Diagnostics

When dealing with finite real-world data, parametric fits can be unstable. We must empirically diagnose the tail behavior using three robust visual heuristics.

#### A. Zipf Plot (Log-Log Survival Function)

Instead of a standard cumulative distribution, this plots the absolute threshold $K$ against the probability of exceeding it, $P(|X| > K)$, on a double-logarithmic scale.

$$
P(|X| > K) \\approx K^{-\\alpha} \\implies \\log P(|X| > K) \\approx -\\alpha \\log K
$$

* **Thin Tails:** The markers plunge almost vertically downward, proving outliers face a strict probabilistic ceiling.
* **Fat Tails:** The markers form a straight, shallow, downward-sloping diagonal. The slope is the negative tail exponent ($-\\alpha$). An $\\alpha \\le 4.0$ indicates infinite kurtosis.

#### B. Maximum-to-Sum Plot

A direct, visual stress-test of moment finiteness. It plots historical time $t$ against the ratio of the single maximum absolute observation to the sum of all absolute observations for moments $p \\in {1, 2, 3, 4}$.

$$
R\_n^{(p)} = \\frac{\\max\_{i=1..n} |X\_i|^p}{\\sum\_{i=1}^n |X\_i|^p}
$$

* **Thin Tails:** The maximum is swallowed by the sum. The ratio smoothly drops to 0.
* **Fat Tails:** If a moment is infinite, a single outlier permanently dominates the sample. The ratio will jump erratically or hover indefinitely above 0. A final ratio $> 0.05$ indicates an unstable/infinite moment.

#### C. Mean Excess Plot (Expected Shortfall Acceleration)

Measures the expected severity of a deviation *given* that an absolute threshold $K$ has already been breached.

$$
e(K) = \\mathbb{E}\[|X| - K \\mid |X| > K]
$$

* **Thin Tails:** Slopes downward (slope $< -0.05$). Gravity pulls extremes aggressively back to the mean.
* **Fat Tails:** Slopes upward (slope $> 0.05$). Extremes accelerate—the deeper you go into a tail, the worse the expected residual severity becomes.

\---

## 🔬 Data-Driven Interpretation Engine

The application includes an automated logic engine that synthesizes the above mathematical properties to deliver a definitive "Fat" or "Thin" tail classification for the analyzed ticker.

**Fat Tail Trigger Conditions:**
The engine flags the underlying asset as "Fat Tailed" if *any* of the following conditions are met:

1. **Student-t df:** $\\le 4.0$
2. **GPD Shape ($\\xi$):** $> 0$ for either tail
3. **Zipf Exponent ($\\alpha$):** $< 4.0$ for either tail
4. **Max-to-Sum (4th Moment):** Final ratio $> 0.05$
5. **Mean Excess Slope:** $> 0.05$ for either tail

\---

## 🛠️ Installation \& Setup

### Prerequisites

* Python 3.10+
* Git

### Local Deployment

1. Clone the repository:

```bash
   git clone \[https://github.com/yourusername/historic-return-fat-tail-diagnostics.git](https://github.com/yourusername/historic-return-fat-tail-diagnostics.git)
   cd historic-return-fat-tail-diagnostics
   ```

2. Create and activate a virtual environment:

```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\\Scripts\\activate
   ```

3. Install required dependencies:

```bash
   pip install -r requirements.txt
   ```

4. Launch the Streamlit dashboard:

```bash
   streamlit run app.py
   ```

\---

## 📦 Dependencies

* **Streamlit:** Interactive web interface architecture and state management.
* **NumPy:** Vectorized arrays, mathematical limits, and logarithmic scale processing.
* **Pandas:** Time series processing and historical cross-joining logic.
* **SciPy:** Maximum Likelihood Estimation optimization for parametric distributions (Student-t, GPD).
* **Plotly:** Interactive probability diagnostics, histograms, and log-log coordinate charts.
* **yfinance:** Live market data extraction mapped natively to underlying asset proxies.

