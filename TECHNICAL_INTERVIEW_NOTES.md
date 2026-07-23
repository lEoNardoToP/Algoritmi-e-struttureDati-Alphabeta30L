# Technical Interview Notes

This document explains the ideas behind the repository in interview-friendly terms. The goal is not to hide behind libraries, but to show that the mechanics are visible and explainable.

---

## 1. Reverse-mode autodiff — NeuroForge C++

Reverse-mode automatic differentiation builds a computation graph during the forward pass and walks that graph backward to compute gradients.

Why it matters:

- neural networks need gradients for training;
- backpropagation is reverse-mode autodiff applied to layered models;
- each operation stores enough local information to propagate derivatives backward;
- this is the core idea behind frameworks such as PyTorch and TensorFlow.

What I should be able to explain in an interview:

- what a computation graph is;
- why reverse mode is efficient for many-parameter / one-loss problems;
- how gradients accumulate when one value contributes to multiple downstream operations;
- why gradient checking compares analytic gradients with finite differences.

---

## 2. Binary cross-entropy and MLP training — NeuroForge C++

Binary cross-entropy measures how well a model predicts a binary label as a probability.

For a label `y` and predicted probability `p`, the loss penalizes confident wrong predictions heavily.

Why it matters:

- it is the standard loss for binary classification;
- it connects naturally to sigmoid outputs;
- it gives useful gradients when probabilities are wrong.

What I should be able to explain:

- why the output layer uses a sigmoid for binary classification;
- why wrong confident predictions are penalized more;
- how the loss gradient flows backward through weights and activations.

---

## 3. Adam optimizer — NeuroForge C++

Adam updates parameters using moving averages of gradients and squared gradients.

Core ideas:

- first moment: smoothed gradient direction;
- second moment: smoothed squared-gradient scale;
- bias correction: compensates for the fact that moving averages start at zero;
- adaptive step sizes: parameters with large gradient variance get scaled differently.

What I should be able to explain:

- why plain gradient descent can be unstable or slow;
- why Adam tracks both mean and variance-like information;
- why learning rate still matters even with Adam.

---

## 4. Deep Q-Network — DeepGrid Java

A Deep Q-Network approximates a Q-value function: `Q(state, action)`.

The model learns which action is expected to produce the best future reward.

Core loop:

1. observe state;
2. choose action using epsilon-greedy exploration;
3. receive reward and next state;
4. store transition in replay memory;
5. sample past transitions;
6. train toward Bellman targets;
7. periodically update the target network.

What I should be able to explain:

- what a Q-value represents;
- why the target is `reward + gamma * max(next_Q)`;
- why using only the latest transition makes training unstable;
- why replay buffers decorrelate samples;
- why a target network stabilizes learning.

---

## 5. Bellman targets — DeepGrid Java

A Bellman target says that the value of an action equals immediate reward plus discounted future value.

Simplified:

```text
target = reward + gamma * max_a Q(next_state, a)
```

If the next state is terminal, the future term disappears.

What I should be able to explain:

- what `gamma` controls;
- why terminal states have no future value;
- why DQN is bootstrapping from its own estimates;
- why this can be unstable without replay and target networks.

---

## 6. Minimax and alpha-beta pruning — Polyglot Alpha-Beta Arena

Minimax assumes two players:

- the AI tries to maximize the score;
- the opponent tries to minimize it.

Alpha-beta pruning avoids exploring branches that cannot change the final decision.

Core ideas:

- `alpha` tracks the best score the maximizer can guarantee so far;
- `beta` tracks the best score the minimizer can guarantee so far;
- when `beta <= alpha`, the branch cannot improve the result and can be skipped.

What I should be able to explain:

- why minimax is exhaustive game-tree search;
- why pruning changes runtime but not the correct result;
- why move ordering affects how much pruning happens;
- why deterministic tie-breaking matters for cross-language tests.

---

## 7. Why the project is polyglot

The eight-language part is not about pretending that Tic-Tac-Toe is hard.

The point is to show that one algorithmic contract can be preserved across different ecosystems:

- same CLI inputs;
- same validation rules;
- same JSON output contract;
- same expected move and score;
- same CI verification strategy.

This demonstrates careful software engineering, not just syntax translation.

---

## 8. Likely interview questions

### Explain reverse-mode autodiff in simple terms.

Forward pass computes values. Backward pass walks the recorded graph in reverse and applies the chain rule to compute gradients efficiently.

### Why is DQN less stable than supervised learning?

Because targets depend on the model's own estimates, samples are correlated, and the environment distribution changes as the policy changes.

### Why use a replay buffer?

To train on a mixed set of past transitions instead of only the most recent, highly correlated experience.

### Why use a target network?

To avoid chasing a moving target every single update. The target network changes more slowly, which stabilizes training.

### What does alpha-beta pruning guarantee?

It returns the same move as minimax, but skips branches that cannot affect the decision.

### What would you improve next?

- add benchmarks;
- add richer tests;
- add diagrams of the computation graph and DQN loop;
- add a small web visualizer;
- extend the search engine to Connect Four;
- turn the AI/security roadmap into IncidentLens.

---

## 9. Honest scope

This is a compact portfolio project, not a production ML framework.

Its value is that the important mechanics are small enough to inspect, run, explain, and extend during an interview.
