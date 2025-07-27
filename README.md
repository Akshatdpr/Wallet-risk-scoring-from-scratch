# Wallet-risk-scoring-from-scratch

# Wallet Risk Assessment – Compound v2

This project analyzes on-chain wallet activity related to the Compound v2 protocol in order to compute and justify wallet-level risk scores. The objective is to create a scalable, explainable pipeline for evaluating wallet behavior and detecting high-risk patterns using DeFi transaction data.

---

##  Files Overview

| File Name | Purpose |
|-----------|---------|
| `compound_v2_transactions.csv` | Raw transaction data from the Compound v2 protocol |
| `Wallet id - Sheet1.csv`       | List of wallet addresses to be analyzed |
| `wallet_risk_scores.csv`       | Final computed risk scores per wallet |
| `assignment2.ipynb`            | Jupyter notebook containing the full data processing and scoring pipeline |

---

##  Data Collection Method

- **Source**: Ethereum blockchain data focused on Compound v2.
- **Method**:
  - Used Etherscan API to gather both normal and internal transactions.
  - Filtered transactions based on contract interactions involving known Compound v2 tokens.
  - Extracted fields like `functionName`, `value`, `gas`, and `timestamp` for each wallet.

- **Input files**:
  - `compound_v2_transactions.csv` – full transaction dataset.
  - `Wallet id - Sheet1.csv` – filter for specific wallet addresses.

- **Scalability**: Easily extendable to other DeFi protocols or updated Compound versions by modifying API filters.

---

##  Feature Selection Rationale

From the transaction data, we extracted wallet-level features that help infer risk:

- **Transaction Behavior**:
  - Total transactions (`tx_count`)
  - Function-specific activity (e.g., `mint`, `borrow`, `repayBorrow`, `redeem`)
- **Economic Exposure**:
  - Total and average value sent/received
  - Net value (inflow vs. outflow)
- **Operational Characteristics**:
  - Number of unique contracts interacted with
  - Self-transfer count (possible mixing/obfuscation)
  - Average gas usage

These features were chosen based on their interpretability and relevance to typical DeFi risk behaviors.

---

##  Scoring Method

- **Normalization**: Applied `MinMaxScaler` to scale all features between 0 and 1.
- **Weighting Scheme**:
  - A linear weighted sum was applied to normalized features to compute a composite score:

    ```
    Risk Score = 1000 × (
        0.15 × tx_count +
        0.20 × avg_gas_price +
        0.25 × send_receive_ratio +
        0.30 × net_outflow +
        0.10 × self_transfers
    )
    ```

- **Output**:
  - `wallet_risk_scores.csv` contains final scores per wallet.
  - Verified that scores are consistent and reproducible.

---

##  Justification of Risk Indicators Used

Each risk indicator reflects behaviors commonly associated with malicious or high-risk wallets:

| Feature | Why It Matters |
|--------|----------------|
| `tx_count` | High frequency can indicate bot-like or automated behavior |
| `avg_gas_price` | Higher gas use may reflect urgent/arbitrage/exploit attempts |
| `send_receive_ratio` | Imbalanced flow may indicate capital drain or exit scams |
| `net_outflow` | Persistent negative balance flow suggests unsustainable behavior |
| `self_transfers` | May signal obfuscation techniques (e.g., mixers or relays) |

These indicators are interpretable and align well with both economic and behavioral risk patterns on-chain.

---

##  Summary

This project delivers a transparent, reproducible, and scalable framework to evaluate wallet risk using real DeFi transaction data. The design ensures:

- **Clarity**: Each step (from feature selection to scoring) is easy to audit.
- **Justification**: Indicators are grounded in practical, on-chain behaviors.
- **Scalability**: The same pipeline can adapt to other protocols or evolve into a full ML-based risk engine.

