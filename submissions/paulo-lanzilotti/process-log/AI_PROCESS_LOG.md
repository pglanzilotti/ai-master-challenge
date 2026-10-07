# AI Process Log — Challenge 003

## Why this document exists

The challenge requires evidence of how AI was used to reach the solution. This narrative is the primary process-log evidence. It records the decomposition, the important corrections, the human decisions, and the implementation iterations.

## Tools

- **Codex:** explored the repository and dataset, reasoned about scoring, implemented the local app, and checked the browser flow.
- **Impeccable:** loaded the existing product/design context and guided the operate-mode interface review.
- **Design Taste:** supplied the initial design read and anti-slop constraints for the seller tool.
- **Image generation:** produced a Queue Mode concept with the one-client modal and three action buttons.

## Decomposition

1. Read the challenge instructions and identified the minimum deliverable: a working seller tool using real data, explainable prioritization, setup documentation, limitations, and a mandatory process log.
2. Inspected the four CRM tables and tested whether company attributes could carry the score. The account join was too sparse to make accounts the primary signal.
3. Compared historical closed-deal durations with current open deals. The key finding was that `Engaging` alone is not intent: current deals in that stage were much older than the historical closing cycle.
4. Designed a score with separate intent, fit/value, and confidence concepts.
5. Built the seller-facing queue first, then added persistent workflow events, ownership rules, cadence, and review states.
6. Added a compact explanation of the score, then added filters by seller, manager, region, and stage.
7. Designed Queue Mode as a focus modal and connected its actions to the same server-side interaction API.
8. Prepared this submission package against the official fork/branch/folder template without opening or pushing a Pull Request.

## Important corrections

The first direction treated all `Engaging` deals as hot. Dataset exploration showed that 81% of currently `Engaging` opportunities had already exceeded the maximum historical closed-deal cycle of 138 days. The momentum component therefore became time-aware: recent `Engaging` deals score higher, while very stale deals require revalidation.

The initial queue concept sorted by score only. That would allow a seller to ignore due returns and would make `No Answer` and `Skip` indistinguishable. The implementation now follows this order:

- review cases first;
- due and eligible work by next due date;
- Priority Score as the tie-breaker;
- future scheduled returns after currently actionable work.

`No Answer` counts as an attempt and uses a short retry cadence. `Skip` postpones without counting a call. Repeated no-answer or skip behavior enters review. `Contacted` requires a note and outcome, and the seller can record an agreed follow-up date.

The first UI explanation exposed raw component values. That was reduced to a collapsed “Como foi priorizada” disclosure with four plain-language reasons, keeping the decision visible without turning the seller screen into an analytics dashboard.

## Human judgment

The human contribution was the product framing and operating model: this is a sales prioritization queue, not a purchase-probability model. The score weights, the interpretation of stale `Engaging` deals, the sticky owner rule for Round Robin, the three action semantics, the Queue Mode interaction, and the decision to defer mobile polish were all deliberate product decisions.

## Iteration and validation

The app was iterated from data inspection to a two-column queue, then to persistent seller workflow, then to Queue Mode. The final flow was checked in a browser with an isolated SQLite database: the three primary actions advanced the queue, Contacted saved notes, positive outcomes exposed optional follow-up dates, negative outcomes entered review, and notes remained visible after reload. The application syntax check passed with `node --check app.js`.

## Evidence note

The official submission guide accepts a written narrative as a process log, so this file is intentionally self-contained. The Queue Mode concept generated during the UI iteration is included at [`screenshots/queue-mode-concept.png`](./screenshots/queue-mode-concept.png). A direct ChatGPT conversation export was not generated automatically in this workspace; it can be added under `chat-exports/` later as an optional supplement. No PR or remote push has been made.
