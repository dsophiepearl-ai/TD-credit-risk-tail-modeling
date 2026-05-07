# Credit Risk in the Tail

What if the models used to set bank capital buffers systematically underestimate risk exactly when it matters most?

---

## Project Overview

Canada remains an example worldwide in terms of risk management, with a controlled banking system left unscathed after the 2008 subprime crisis.  

But what if another threat is emerging—this time from equity market concentration, directly impacting banks’ capital buffers?

Equity markets have become increasingly concentrated, with a small number of large-cap stocks driving a significant share of index performance. While this boosts returns in bullish regimes, it also introduces systemic fragility: a localized shock (e.g., earnings miss, policy tightening) can trigger a broader liquidity-driven cascade across the economy.

This exposes a structural issue in how banks measure risk.

Under Basel III, capital buffers are determined using **VaR at the 99.9% level**. However, as highlighted by Brereton et al. (2012), standard credit risk models:

- assume **tail independence**
- severely **underrepresent extreme events**

The Vasicek model introduces correlation through a macroeconomic factor, but the **Gaussian assumption implies thin tails**, meaning extreme joint defaults are treated as nearly impossible.

> “Defaults do not occur independently of one another, but rather tend to cluster.”  
> — Brereton et al. (2012)

---

A second issue arises from estimation.

Monte Carlo simulations struggle with extreme quantiles:

- even with 10,000 simulations → **0 or 1 extreme event observed**
- increasing simulations is computationally expensive
- sampling remains concentrated in **non-tail regions**

> “The efficient estimation of credit risk measures is often computationally expensive…”  
> — Brereton et al. (2012) :contentReference[oaicite:1]{index=1}

---

### Approach

To address these issues:

1. Improve **model realism** → Student-t distribution (fat tails)
2. Improve **estimation** → Importance Sampling
3. Compare **stability of VaR across simulations**

---

# Findings

---

## 1. Gaussian underestimates risk

The Vasicek model introduces dependence through a macro factor:

\[
A_i = \sqrt{\rho} Z + \sqrt{1-\rho} \epsilon_i
\]

- \(Z\) → macro factor  
- \(\epsilon_i\) → firm-specific shock  
- \(\rho\) → sensitivity to economy  

→ correlation exists under normal conditions  

---

### Issue: distribution of \(Z\)

\[
Z \sim \mathcal{N}(0,1)
\]

- symmetric  
- **thin tails**

→ extreme macro shocks are nearly impossible  

---

### Consequence

- insufficient default clustering  
- artificially thin loss distribution  

---

### Evidence

![Model Comparison](./Risk2.png)

- VaR (99.9%)
  - Gaussian: 79M  
  - Student-t: 191M  

- ES (99.9%)
  - Gaussian: 95M  
  - Student-t: 316M  

- Defaults (99.9%)
  - Gaussian: 176  
  - Student-t: 426  

---

### Insight

- Gaussian captures correlation in normal regimes  
- fails in extreme scenarios  

→ ~2.5x increase in capital under realistic tails  

> The structure is correct — the distribution is not.

---

## 2. Monte Carlo struggles in the tail

At the 99.9% level:

- only **0.1% of simulations matter**
- 100,000 runs → ~100 tail observations  

---

### Evidence

![Monte Carlo Instability](./Risk1.png)

- Mean VaR: ~80.1M  
- Std: 1.19M  
- Range: 77.4M → 81.9M  

---

### Insight

- same model → different VaR  
- high sensitivity to randomness  

> The model may be right — but the measurement isn’t.

---

## 3. Importance Sampling fixes estimation

Importance sampling shifts the sampling distribution:

- \(f\): true distribution → \(N(0,1)\)  
- \(g\): shifted distribution → \(N(\mu_{\text{shift}},1)\), \(\mu < 0\)  

---

### Reweighting

\[
w(z) = \frac{f(z)}{g(z)}
\]

- extreme events → oversampled → lower weight  
- normal events → undersampled → higher weight  

→ more tail observations without bias  

---

### Evidence

![VaR Stability](./Risk3.png)

- Mean VaR:
  - Standard MC: ~80.1M  
  - IS: ~80.3M  

- Std:
  - Standard MC: 1.19M  
  - IS: 0.47M  

- Range:
  - Standard MC: 77.4M → 81.9M  
  - IS: 79.2M → 81.45M  

---

### Insight

- mean unchanged → same risk  
- variance ↓ ~2.5x → stable estimator  

> Same model, same risk — but one method actually lets you measure it reliably.

---

# Final Takeaway

- Gaussian → underestimates extreme co-movements  
- Monte Carlo → struggles to observe them  
- Importance Sampling → makes them measurable  

---

# Conclusion

The most accurate way to estimate credit risk and determine appropriate capital buffers is:

- **Student-t distribution** → realistic tail behavior  
- **Importance Sampling** → stable estimation  

---

# Final Insight

- Student-t → fixes the **model**  
- Importance Sampling → fixes the **estimation**  

---

## References

Brereton, T. J., Kroese, D. P., & Chan, J. C. (2012).  
*Monte Carlo Methods for Portfolio Credit Risk*.  
Australian National University.
