# Project Minerva — Week 5: Multi-Agent Collective Intelligence

This project extends the **Week 4 Multi-Agent Bidding Environment** by introducing a **Quantized Simplex Gossip (QSG)** communication phase before every bidding round. Rather than immediately committing to a bid, agents first exchange their intended bids and confidence scores with a neighboring agent through multiple gossip iterations before making a final decision.

The primary objective is to investigate whether increasing communication among agents improves **collective reasoning** or eventually leads to **memetic drift**, where consensus emerges through repeated copying instead of independent reasoning.

---

# Setup

## Requirements

```
groq
langgraph
langchain-groq
python-dotenv
numpy
matplotlib
scipy
```

## Installation

```bash
pip install groq langgraph langchain-groq python-dotenv numpy matplotlib scipy
```

## API Key

Create a `.env` file inside the project directory.

```text
GROQ_API_KEY=your_key_here
```

## Running the Notebook

Open **week_5.ipynb** and execute every notebook cell sequentially.

### Step 1

Runs one complete simulation consisting of:

- 5 AI agents
- 10 bidding rounds
- 3 gossip sub-rounds before every bid

For every round, the notebook logs:

- Round number
- Threshold value
- Initial intended bid of every agent
- Updated bid after gossip
- Confidence score
- Final consensus bid
- Eliminated agent (if any)

---

### Step 2

Runs the complete experiment for three different group sizes:

- N = 3
- N = 5
- N = 10

Each configuration is executed over **10 random seeds (0–9)**.

The notebook automatically computes:

- Consensus Bid
- Mean Accuracy
- Standard Deviation
- Bid Variance
- Bid Entropy
- Confidence Trend
- Drift Metrics

All experimental outputs are saved to:

```
results.json
```

The implementation uses:

```
llama-3.1-8b-instant
```

through the **LangChain-Groq** integration.

---

# Methodology

## Week 4 Extension

The complete Week 4 bidding environment is preserved.

Each game consists of:

- 10 rounds
- Threshold increases by 3 every round
- Agents reason independently
- Lowest bidder below threshold is eliminated

Week 5 introduces an additional **communication phase** before every bid.

---

## Gossip Phase

Each round follows the pipeline:

```
Independent Reasoning
        ↓
Generate Intended Bid
        ↓
Generate Confidence Score
        ↓
3 Gossip Sub-rounds
        ↓
Neighbour Influence
        ↓
Updated Bid
        ↓
Week 4 Bidding Environment
```

Each agent communicates with **exactly one neighbour**.

The neighbour topology is generated as a randomly shuffled **fixed ring**, which remains unchanged throughout the entire game.

---

## Gossip Updates

During every gossip sub-round, each agent receives:

- Neighbour's intended bid
- Neighbour's confidence score

The LLM then updates its own belief and outputs:

- Updated Bid
- Updated Confidence
- One-line Reasoning

If invalid JSON is generated, the previous bid and confidence are retained automatically.

---

# Results

## Step 2 — Sweep Summary 

| N | Mean Accuracy | Std Accuracy | Round-10 Bid Variance |
|---|--------------:|-------------:|----------------------:|
| 3 | 0.93 | 0.05 | 1.84e3 |
| 5 | 0.95 | 0.04 | 2.67e3 |
| 10 | 0.91 | 0.08 | 4.11e3 |

---

## Drift–Selection Crossover

```
N = 10
```

The drift-selection crossover is identified as the group size exhibiting the **highest Round-10 consensus bid variance**, indicating that repeated neighbour influence begins to dominate independent reasoning.

---

# Generated Figures

## 1. Drift Selection Plot

**File:** `drift_selection_plot.png`

Displays:

- Mean Accuracy ± Standard Deviation
- Round-10 Consensus Bid Variance

against group size.

---

## 2. Consensus Trajectory

**File:** `consensus_trajectory.png`

Shows the average consensus bid across all rounds for each value of **N**.

---

## 3. Confidence Trend

**File:** `confidence_trend.png`

Illustrates how average agent confidence changes after repeated gossip updates.

---

## 4. Bid Entropy

**File:** `bid_entropy.png`

Measures the diversity of final bids among agents.

- Higher entropy → greater disagreement
- Lower entropy → stronger consensus

---

## 5. Round-10 Bid Distribution

**File:** `round10_bid_distribution.png`

Displays the distribution of the final consensus bid across all random seeds for every tested group size.

---

# Analysis

The purpose of this experiment is to determine whether agreement among agents emerges through **genuine collective reasoning** or through **repeated propagation of neighbouring decisions**.

Initially, every agent independently generates its intended bid using:

- Current resources
- Threshold value
- Game history
- Assigned persona

During the gossip phase, agents repeatedly exchange only two pieces of information:

- Intended Bid
- Confidence Score

Unlike centralized consensus algorithms, agents never observe the entire population simultaneously. Information propagates locally through the fixed ring topology, allowing neighbour influence to accumulate over successive gossip rounds.

As the number of participating agents increases, early high-confidence decisions have a greater opportunity to spread throughout the network. This phenomenon is quantified using the **Round-10 Consensus Bid Variance** measured across different random seeds.

The **Drift–Selection Crossover** is defined as the group size exhibiting the largest Round-10 variance. At smaller group sizes, consensus is expected to arise primarily from independent reasoning and contextual decision making. At larger group sizes, repeated neighbour interactions increasingly amplify early stochastic decisions, causing consensus to depend more on information propagation than original reasoning.

Qualitative inspection of the reasoning traces stored in **results.json** further supports this analysis by showing whether agents justify their updated bids using:

- Historical game context
- Resource management
- Threshold reasoning

or whether they increasingly reference neighbouring bids and confidence values, indicating the onset of memetic drift.

---

# Deliverables

- `week_5.ipynb` — Complete implementation of the gossip-based multi-agent simulation
- `results.json` — Raw experimental results including bids, confidences and reasoning traces
- `drift_selection_plot.png`
- `consensus_trajectory.png`
- `confidence_trend.png`
- `bid_entropy.png`
- `round10_bid_distribution.png`
- `README.md`