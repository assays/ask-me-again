# AMA: Ask Me Again

> Jeremiah was a bullfrog. Never understood a single word he said.

A skill for agents that run unattended. When a decision model's answer is unsettled, the question
or the state was wrong. The agent fixes one, asks again, and only then interrupts its human: once,
in a digest.

Again means differently. Sending the same question twice is the one thing it forbids.

## What it does

- Turns "what should I do?" into judgments a decision model can answer, and keeps the decision in
  a rule the agent writes down first.
- Reads 0.7 or more as yes, 0.3 or less as no, and the band between as unsettled.
- On unsettled, repairs in order: the state, then the scope of the question, then its wording. One
  change per round, two rounds at most, every round logged.
- Keeps one-way actions, wider permissions and undecided behaviour with the human, without asking
  the model.
- Holds what is left and sends one digest: the question, the options with their cost, a
  recommendation, and the numbers.

## What it does not do

- It cannot produce evidence. With nothing new to add to the state, it still asks you.
- It does not make the model less noisy near the line. The example in the skill file shows the
  same kind of case decided opposite ways a day apart.
- It has been run as relayed instructions across several agent sessions, not yet for long as a
  loaded skill file.

## Needs

- An agent harness that loads skill files (built with oh-my-pi; the file is plain Markdown).
- A decision model that returns calibrated probabilities for typed questions (built with
  TypeSafe's Jev).
- A place to log decisions.

## Install

Copy the `ama` folder into the folder your harness reads skills from, for example
`~/.agents/skills/ama/` or `.claude/skills/ama/`. Keep the name `ama`, with `SKILL.md` directly
inside it.

## How it relates to `ask`

Rook's `ask` gets one good answer from a person once you have decided to ask. AMA decides whether
to interrupt the person at all.

## License

MIT.
