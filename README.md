
# STX-Fundara – A Crowdfunding Smart Contract on Stacks

STX-Fundara is a decentralized crowdfunding platform implemented in Clarity for the Stacks blockchain. It allows users to submit fundraising projects, contribute STX tokens, vote on fund withdrawals, and request refunds if goals aren’t met. The contract ensures transparent, community-driven decision-making with built-in safeguards around contributions, voting, and project management.

---

## 📋 Features

* ✅ **Submit Projects:** Users can create new crowdfunding projects with a goal and deadline.
* 💸 **Contribute Funds:** Backers can contribute STX tokens to active projects before the deadline.
* 🗳️ **Vote for Withdrawal:** Contributors vote on whether a project creator can withdraw funds after the deadline.
* 🔒 **Fund Withdrawal Conditions:** Funds are only released if:

  * Goal is met
  * Deadline and voting period have passed
  * At least 10 contributors voted
  * At least 60% of the votes are in favor
* 🔁 **Request Refunds:** If the project fails (goal not met or vote fails), contributors can get their money back.
* 🛑 **Cancel Projects:** Creators can cancel their projects if no contributions have been made.
* 🕓 **Extend Deadlines:** If at least 75% of the goal is raised, the creator can request up to 3 deadline extensions (max 30 days each).

---

## 📦 Smart Contract Structure

### 🔐 Constants

Defines error codes, thresholds, and configuration limits:

| Constant                     | Description                                   |
| ---------------------------- | --------------------------------------------- |
| `ERR_*`                      | Error constants for better debugging          |
| `EXTENSION_THRESHOLD`        | 75% goal required to allow deadline extension |
| `MAX_EXTENSION_DAYS`         | Maximum 30 days per extension                 |
| `VOTING_PERIOD_DAYS`         | 7 days voting window post-deadline            |
| `MIN_VOTE_THRESHOLD_PERCENT` | At least 60% of votes must be in favor        |
| `MIN_VOTE_COUNT_THRESHOLD`   | At least 10 votes required for quorum         |

---

### 🗃️ Data Structures

#### `projects` Map

Stores metadata about each project:

```clojure
{
  project-id: uint,
  name: string-ascii(50),
  creator: principal,
  goal: uint,
  deadline: uint,
  total-raised: uint,
  is-active: bool,
  extensions-used: uint,
  vote-end-time: uint,
  total-votes: uint,
  votes-in-favor: uint
}
```

#### `contributions` Map

Tracks how much each user contributed to a specific project.

#### `votes` Map

Tracks whether a contributor voted in favor or against fund withdrawal.

#### `project-nonce` Variable

Auto-incremented project ID.

---

## 📂 Public Functions

### 1. `submit-project(name, goal, deadline)`

* Submits a new project.
* Validates goal, deadline, and name.

### 2. `contribute(project-id, amount)`

* Allows users to contribute STX.
* Requires project to be active and before deadline.

### 3. `vote(project-id, in-favor)`

* Contributors vote on fund withdrawal.
* Only one vote per contributor.

### 4. `withdraw-funds(project-id)`

* Project creators withdraw if all conditions met.
* Transfers total raised STX to creator.

### 5. `refund(project-id)`

* Contributors refund if goal/vote fails after vote period.

### 6. `cancel-project(project-id)`

* Project creator cancels if no contributions.

### 7. `extend-deadline(project-id, new-deadline)`

* Project creator requests deadline extension.
* Allowed only if 75% of goal is raised.
* Max 3 extensions, 30 days each.

---

## 🔍 Read-Only Functions

| Function                                    | Description                               |
| ------------------------------------------- | ----------------------------------------- |
| `get-project(project-id)`                   | Fetch project details                     |
| `get-contribution(project-id, contributor)` | Get user’s contribution                   |
| `get-vote(project-id, voter)`               | Get user’s vote                           |
| `get-voting-status(project-id)`             | Get vote progress & whether threshold met |

---

## ⚖️ Voting Logic Summary

To release funds to project creators:

* Voting starts after project deadline.
* Voting ends after `deadline + 7 days`.
* At least **10 votes** must be cast.
* At least **60% of votes must be in favor**.
* Only **contributors** can vote.
* Only **one vote per contributor**.

---

## 🛑 Refund Logic Summary

Contributors can refund if:

* Project did **not reach the goal**, or
* **Voting quorum not reached**, or
* **Less than 60% voted in favor**.

---

## 🔄 Deadline Extension Summary

* Creators can **extend deadlines** up to 3 times.
* Each extension maxes out at **30 days (4320 blocks)**.
* Only allowed if **≥75% of goal is raised**.

---

## 🧪 Example Workflow

1. Alice submits a project to raise 1000 STX in 2 weeks.
2. 10 users contribute 1100 STX.
3. After the deadline, contributors vote:

   * 8 vote in favor (80%)
   * 2 vote against
4. Vote period ends (7 days later).
5. Alice withdraws the funds.
6. If vote fails, contributors refund their STX.

---

## 🚀 Deployment Considerations

* Ensure correct block-to-time assumptions (e.g., 144 blocks/day).
* Consider wrapping this contract with a UI/frontend for ease of use.
* Voting, extensions, and funds rely on transparent community coordination.

---

## ✅ Requirements

* Clarity 2.0 compatible
* STX token support
* Deployed on a Stacks-compatible network

---

## 🧾 License

MIT License — Open source and freely available for modification and commercial use.

---
