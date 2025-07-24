# StackCredit

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Clarity](https://img.shields.io/badge/Clarity-3.0-blue.svg)](https://docs.stacks.co/clarity)
[![Stacks](https://img.shields.io/badge/Stacks-Layer%202-orange.svg)](https://stacks.co)

> A trustless, Bitcoin-native credit scoring and lending platform built on Stacks Layer 2

## 🌟 Overview

StackCredit revolutionizes DeFi lending by introducing a sophisticated credit scoring mechanism that reduces over-collateralization for creditworthy borrowers. Users build reputation through successful loan repayments, unlocking better terms and lower collateral requirements over time. The protocol is fully decentralized, transparent, and secured by Bitcoin's robust infrastructure through Stacks Layer 2.

## ✨ Key Features

- **🎯 Dynamic Credit Scoring**: Credit scores range from 50-100, improving with successful loan repayments
- **📉 Adaptive Collateral**: Lower credit scores require higher collateral, incentivizing good behavior
- **💰 Credit-Based Interest Rates**: Better credit scores unlock lower interest rates
- **🔄 Multi-Loan Support**: Users can maintain up to 5 active loans simultaneously
- **⚡ Automatic Default Handling**: Smart contract automatically handles defaults with score penalties
- **🔍 Transparent History**: Complete on-chain loan history and reputation tracking

## 🏗️ Architecture

### Credit Scoring System

The protocol implements a dynamic credit scoring system where:

- **Initial Score**: New users start with a minimum score of 50
- **Score Range**: 50 (minimum) to 100 (maximum)
- **Loan Eligibility**: Minimum score of 70 required for loan approval
- **Score Updates**:
  - Successful repayment: +2 points
  - Default: -10 points

### Collateral Requirements

Collateral requirements are dynamically calculated based on credit scores:

```clarity
;; Higher credit score = Lower collateral requirement
collateral-ratio = 100 - (score * 50 / 100)
required-collateral = (loan-amount * collateral-ratio) / 100
```

### Interest Rate Calculation

Interest rates are inversely correlated with credit scores:

```clarity
;; Higher credit score = Lower interest rate
interest-rate = base-rate - (score * 5 / 100)
```

## 🚀 Quick Start

### Prerequisites

- [Clarinet](https://github.com/hirosystems/clarinet) v2.0+
- Node.js v18+
- TypeScript

### Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/philly-zion/StackCredit.git
   cd StackCredit
   ```

2. **Install dependencies**

   ```bash
   npm install
   ```

3. **Run tests**

   ```bash
   npm test
   ```

4. **Check contract syntax**

   ```bash
   clarinet check
   ```

### Development Workflow

1. **Watch mode for continuous testing**

   ```bash
   npm run test:watch
   ```

2. **Generate test coverage report**

   ```bash
   npm run test:report
   ```

3. **Interactive REPL session**

   ```bash
   clarinet console
   ```

## 📋 Usage Examples

### Initialize Credit Profile

```clarity
;; First-time users must initialize their credit profile
(contract-call? .stack-credit initialize-score)
```

### Request a Loan

```clarity
;; Request 1000 STX loan with 500 STX collateral for 5000 blocks
(contract-call? .stack-credit request-loan u1000 u500 u5000)
```

### Repay a Loan

```clarity
;; Repay 250 STX towards loan ID 1
(contract-call? .stack-credit repay-loan u1 u250)
```

### Check Credit Score

```clarity
;; Get user's credit profile
(contract-call? .stack-credit get-user-score 'SP1ABC...)
```

## 🔧 Contract Functions

### Public Functions

| Function | Description | Parameters |
|----------|-------------|------------|
| `initialize-score` | Creates initial credit profile | None |
| `request-loan` | Request a new loan | `amount`, `collateral`, `duration` |
| `repay-loan` | Make loan repayment | `loan-id`, `amount` |
| `mark-loan-defaulted` | Mark overdue loan as defaulted (admin) | `loan-id` |

### Read-Only Functions

| Function | Description | Returns |
|----------|-------------|---------|
| `get-user-score` | Get user's credit profile | Credit score and stats |
| `get-loan` | Get loan details | Loan information |
| `get-user-active-loans` | Get user's active loans | List of loan IDs |
| `get-system-stats` | Get protocol statistics | System metrics |

## 📊 Data Structures

### UserScores Map

```clarity
{
  score: uint,           ;; Current credit score (50-100)
  total-borrowed: uint,  ;; Lifetime STX borrowed
  total-repaid: uint,    ;; Lifetime STX repaid
  loans-taken: uint,     ;; Total loans taken
  loans-repaid: uint,    ;; Total loans repaid
  last-update: uint,     ;; Last update block height
}
```

### Loans Map

```clarity
{
  borrower: principal,    ;; Loan recipient
  amount: uint,          ;; Principal amount
  collateral: uint,      ;; Collateral locked
  due-height: uint,      ;; Due block height
  interest-rate: uint,   ;; Interest rate %
  is-active: bool,       ;; Active status
  is-defaulted: bool,    ;; Default status
  repaid-amount: uint,   ;; Amount repaid
}
```

## 🛡️ Security Features

- **Access Control**: Administrative functions restricted to contract owner
- **Input Validation**: Comprehensive parameter validation for all functions
- **Overflow Protection**: Safe arithmetic operations throughout
- **State Consistency**: Atomic operations ensure data integrity
- **Collateral Security**: Automatic collateral locking and release

## 🧪 Testing

The project includes comprehensive test coverage using Vitest and Clarinet SDK:

```bash
# Run all tests
npm test

# Run tests with coverage
npm run test:report

# Watch mode for development
npm run test:watch
```

Test files are located in the `tests/` directory and cover:

- Credit score initialization and updates
- Loan creation and validation
- Repayment processing
- Default handling
- Edge cases and error conditions

## 📈 Economic Model

### Credit Score Mechanics

- **Starting Score**: 50 (minimum for platform access)
- **Loan Threshold**: 70 (minimum for loan eligibility)
- **Maximum Score**: 100 (best terms available)
- **Improvement Rate**: +2 points per successful repayment
- **Penalty Rate**: -10 points per default

### Risk Parameters

- **Maximum Active Loans**: 5 per user
- **Maximum Loan Duration**: ~1 year (52,560 blocks)
- **Base Interest Rate**: 10% (adjusted by credit score)
- **Collateral Range**: 50-100% of loan amount

## 🚦 Error Codes

| Code | Error | Description |
|------|-------|-------------|
| u1 | ERR-UNAUTHORIZED | Unauthorized operation |
| u2 | ERR-INSUFFICIENT-BALANCE | Insufficient balance |
| u3 | ERR-INVALID-AMOUNT | Invalid amount specified |
| u4 | ERR-LOAN-NOT-FOUND | Loan does not exist |
| u5 | ERR-LOAN-DEFAULTED | Loan is in default |
| u6 | ERR-INSUFFICIENT-SCORE | Credit score too low |
| u7 | ERR-ACTIVE-LOAN | Too many active loans |
| u8 | ERR-NOT-DUE | Loan not yet due |
| u9 | ERR-INVALID-DURATION | Invalid loan duration |
| u10 | ERR-INVALID-LOAN-ID | Invalid loan identifier |

## 🔮 Future Enhancements

- **Multi-Asset Support**: Support for additional cryptocurrencies
- **Governance Token**: Community governance for protocol parameters
- **Insurance Pool**: Decentralized insurance for lender protection
- **Credit Score NFTs**: Transferable credit reputation tokens
- **Automated Liquidations**: Smart contract-based liquidation engine
- **Cross-Chain Integration**: Bridge to other blockchain networks

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

### Development Setup

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Install dependencies (`npm install`)
4. Make your changes
5. Add tests for new functionality
6. Ensure all tests pass (`npm test`)
7. Commit your changes (`git commit -m 'Add amazing feature'`)
8. Push to the branch (`git push origin feature/amazing-feature`)
9. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **Stacks Foundation** for the robust Layer 2 infrastructure
- **Hiro Systems** for Clarinet development tools
- **Bitcoin Community** for the foundational security layer
- **DeFi Innovators** for inspiring decentralized finance solutions

## 📞 Contact & Support

- **GitHub Issues**: [Report bugs or request features](https://github.com/philly-zion/StackCredit/issues)
- **Documentation**: [Full technical documentation](https://docs.stackcredit.xyz)
- **Community**: [Join our Discord](https://discord.gg/stackcredit)
- **Twitter**: [@StackCreditDeFi](https://twitter.com/StackCreditDeFi)

---

**⚠️ Disclaimer**: This is experimental software. Please conduct thorough testing and auditing before deploying to mainnet. DeFi protocols carry inherent risks including smart contract vulnerabilities, market volatility, and regulatory uncertainty.
