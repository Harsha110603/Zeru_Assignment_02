# 📊 Wallet Risk Scoring using Compound V2 Protocol

This project analyzes the on-chain behavior of Ethereum wallets interacting with the **Compound V2 protocol** to assign a **risk score between 0 and 1000**. A higher score indicates more reliable financial activity (e.g., regular repayments, no liquidations), while a lower score suggests riskier behavior (e.g., frequent liquidations, no repayments).

---

## 🔍 Objective

- Retrieve transaction history for each wallet from **Compound V2**
- Extract key risk-related behavioral features
- Compute a normalized risk score for each wallet
- Export results as a structured CSV

---

## 🛠️ Technologies Used

- **Python**
- **Pandas**, **NumPy**
- **Covalent API** for on-chain data
- **Compound V2** cToken contract addresses

---

## 🗃️ Data Collection

- **Covalent API** is used to query transaction data from Ethereum Mainnet.
- We filter transactions by known **Compound V2 contracts** (e.g., cDAI, cUSDC, cETH).
- The API is queried wallet-by-wallet, respecting rate limits via timed delays.
- Transactions are analyzed for function calls like `borrow`, `repay`, and `liquidate`.

---

## 📊 Feature Engineering

For each wallet, we compute the following features:

| Feature         | Description |
|----------------|-------------|
| `tx_count`      | Total number of transactions with Compound V2 |
| `borrow_tx`     | Number of times the wallet borrowed assets |
| `repay_tx`      | Number of times the wallet repaid a loan |
| `liquidations`  | Number of times the wallet was liquidated |
| `unique_tokens` | Number of unique Compound cTokens interacted with |
| `net_activity`  | A simple calculated metric: `borrow_tx - repay_tx - liquidations` |

---

## 🧮 Risk Scoring Logic

Each feature is normalized using **Min-Max scaling**. Then, we compute a weighted sum:

```python
score = (
    tx_count       * 0.2  +
    borrow_tx      * 0.25 +
    repay_tx       * 0.15 +
    (1 - liquidation_score) * 0.25 +
    unique_tokens  * 0.1  +
    net_activity   * 0.05
) * 1000
