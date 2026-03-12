# SmartSettle — Payment Routing Optimizer

## Overview
SmartSettle is a payment routing optimization system that assigns incoming transactions to different settlement channels while minimizing total system cost. The system considers channel fees, processing latency, channel capacity, transaction priority, and delay penalties.

The optimizer selects the most suitable channel for each transaction while ensuring that deadlines are satisfied and channel capacity limits are not violated.

---

## Algorithm

The system uses a **greedy scheduling approach**.

1. All transactions are loaded from the CSV file.
2. Each transaction is assigned a priority score:

Score = Priority × (Amount / MaxDelay)

3. Transactions are sorted in descending order of this score so that more urgent transactions are processed first.
4. For each transaction:
   - A preferred order of channels is selected based on urgency.
   - The algorithm searches for the earliest available time slot in each channel.
   - The routing cost is calculated:

Cost = ChannelFee + (PenaltyFactor × Amount × Delay)

5. If the routing cost is lower than the failure cost, the transaction is scheduled.
6. If no channel can process the transaction before its deadline, it is marked as **failed**.

---

## Cost Model

### Routing Cost
Cost = Fee + DelayPenalty

DelayPenalty = PenaltyFactor × Amount × Delay

### Failure Cost
FailureCost = FailureFactor × Amount

The algorithm only schedules a transaction if the routing cost is cheaper than the failure cost.

---

## Complexity Analysis

Let **n** be the number of transactions.

Sorting transactions requires:

O(n log n)

For each transaction, the algorithm checks a constant number of channels (3) and searches for available time slots.

Therefore, the practical time complexity is approximately:

O(n log n)

---

## Parameter Choices

- **PenaltyFactor (0.001)**  
  Encourages minimizing delays for high-value transactions.

- **FailureFactor (0.5)**  
  Makes failing a transaction expensive, encouraging routing whenever possible.

- **Channel Preferences**  
  High-priority transactions prefer faster channels, while lower-priority transactions prefer cheaper channels.

---

## Input

CSV file containing:

tx_id, amount, arrival_time, max_delay, priority

---

## Output

The system generates a JSON file containing:

- transaction assignments
- estimated total system cost