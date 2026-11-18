---
name: ama
description: Ask Me Again. Decides when an unattended agent may interrupt its human. Use when the agent has a question and a decision model (one that answers typed questions with calibrated probabilities) is available. It turns the question into judgments, asks the model, repairs the state or the question when the answer is unsettled, and reaches the human once, in a digest, only for what is left or what was always theirs.
license: MIT
---

# ama: Ask Me Again

> Jeremiah was a bullfrog. Never understood a single word he said.

When the model does not understand, ask a better question. Again means differently: an unsettled
answer says the state or the question did not settle it. Fix one of them and ask again. Asking the
same thing again is not a fix.

## What never goes to the model

Send these straight to the human, in the digest. Do not ask the model first.

- A one-way or destructive action.
- Anything that widens what an agent may do, or weakens a safety rule.
- New behaviour the human has not decided.
- A question no person could answer at a glance from what you can show (a long diff, a disputed
  matter of taste). The model's answer there is noise, and your rule would treat it as signal.

## Steps

1. **Write the decision as a table, in your own words.** List the judgments the decision rests on
   and what you do for each combination, including "unsettled". The model supplies judgments; your
   rule decides. "Should we expand the rollout?" is a decision. "Does the log count the restarts
   operators do by hand?" is a judgment.
2. **Build the state.** Give what a person would look at: the evidence, who said it, the facts you
   already know. Leave out what does not bear on the judgments. Check: a smart person shown only
   this state and one question could answer quickly.
3. **Ask all judgments in one request.** One statement per question, true means yes, no "or".
   Read each as: 0.7 or more is yes, 0.3 or less is no, between is unsettled.
4. **Settled: act by your table and log it.** Record the questions, the state, the numbers and the
   action, marked "decided by delegation". The human can overturn it.
5. **Unsettled: repair, then ask again. Two repairs at most.** Work in this order and change one
   thing per round:
   1. The state. Is a fact missing that you could fetch or measure? Add it. Is something in it
      irrelevant or misleading? Remove it.
   2. The scope. Does the question hold two judgments? Split it.
   3. The wording. Is a term undefined, or the "no" case vague? Say what separates the two.
   - Check before each round: you can name what changed and why it should matter. If you cannot,
     stop. Do not send the same request again, drop a round you dislike, or move the thresholds.
   - A repair that needs evidence you do not have is a task, not a rewording. Get the evidence if
     it is cheap; otherwise the question is the human's.
6. **Still unsettled: it is the human's.** Take a default only if it is reversible and only
   tightens a limit; mark it interim.
7. **Interrupt once.** Hold what is for the human until you have nothing else to run, unless it
   blocks all work. Send one digest. Per item, three lines at most: the question in plain words, the
   options with what each costs, and your recommendation first, with the model's numbers from every
   round. The human answers `id: n`.

## Done

- Every decision has a log entry with all rounds, not only the last.
- No digest item was answerable by the model with state you could have supplied.
- No interrupt outside the digest, except for work that is fully blocked.

## Example

From a real log. A payment API answered one payout with a redirect to another host. The agent's
limits: the payout key goes only to the payout API, and no redirect is followed on a call that
carries a secret. The owner may decide: pay the account on file, pay the new account, or pay
nothing. The same four questions were asked each time; each is the probability that the house
manual and the owner's limits support that outcome, plus whether they settle it without a fact
only the owner knows.

| Day | What the state held | Pay on file | Pay nothing | Settles it | Outcome |
|---|---|---|---|---|---|
| 1 | the task, the manual, the limits | 0.65 | 0.71 | 0.77 | pay nothing |
| 2 | the same, for a new task of the same kind | 0.85 | 0.43 | 0.86 | pay on file |
| 3 | the same, plus one fact from day 2's log | 0.97 | 0.04 | 0.95 | pay on file |

The fact added on day 3: "after the decision to pay on file, the same payout API paid the task on
the second request without any redirect; the key never left the payout API."

Days 1 and 2 gave opposite outcomes for the same kind of case. Nothing in the wording changed on
day 3. One observed fact went into the state, and the split was gone. Day 1 is the case this
skill exists for: "pay nothing" cleared the bar by 0.01 while "pay on file" sat just under it at
0.65. Two rival outcomes that close are not settled, whatever the threshold says; it should have
been repaired or sent to the human, not acted on.
