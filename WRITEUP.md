# Write-up: AI Proposal Triage (MATACS-TH-2026-02)
## 1. AI vs. rules
AI:
i used AI , reading messy text and finding the facts(like for some words i used it to understand). Optionally it also writes the one line reason like .
Code:
Loading criteria, pass/fail checks, all the maths, sorting is done by code.
Because, reading text and finding facts is easy for AI and for checking criteria ,pass/fail check ,sorting, ranking is done by code since it should be consistent like everytime we give the sample pdf to the tool it should give the exact results 
## 2. Consistency and explainability
How do you make scores repeatable and explainable :
To make scores repeatable and explainable i use AI only to  extracts facts. It runs at temperature 0 and must return a fixed JSON format with a quote for every fact. Scores come from fixed formulas on those facts, so the same facts always give the same score.

 If the same proposals were ranked twice, would the result be the same?
 If we use the extracted facts for each proposal and save it and if the same proposal is ranked again the saved facts can be reused again and resutls should be the same

why a proposal ranked where it did?
The reviewer sees a table for each proposal and score is calculated based on the experience,price,timeline,warranty  ,the one with the high score would rank high.

## 3. Human in the loop
The reviewer sees the ranked list and there be another list for flagged proposals with the reasons,then from that list reveiwer can confirm ,move the proposal rank or flag it .The final decision is done by the human.
Logged for audit:
who uploaded what,criteria version, prompt version, model used, raw AI output, facts and quotes, scores, flags, every override (who, when, why) and the final decision.
## 4. Changing criteria
Since criteria are versioned,editing it would make a new version,old scrores stay linked with the old version and they can be marked as stale,the ranks with the stale flag can be rescored again by using the extracted facts ,the new AI call is made for the new fact not for something which have been clearly extracted earlier
## 5. Risks
what could go wrong with this tool and how the design  handles it?
| Risk | How the design handles it |
|---|---|
| Wrong extraction | Every fact needs a quote, and code checks the quote exists. Unclear or missing facts are flagged, not guessed |
| Made-up facts | "Not stated" is an allowed answer. Missing = 0 points, nothing is assumed |
| Hidden instructions in a PDF | The prompt treats proposal text as data only. Code scans for suspicious text. Also, AI doesn't score, so it can't change the ranking. Proposal C is flagged for this |
| Bias (e.g. favouring big firms or nice writing) | Only stated facts and fixed formulas are scored. Company names are not scored. Reviewer can see all evidence |
| Cost | One cheap AI call per proposal, saved results, and re-scoring uses no AI |
| Bad or scanned PDFs | If text can't be read properly, flag for manual review |
| Trusting the tool too much | It only gives a recommendation. Flags and the override log keep a person responsible |


## How my design ranks the samples
- **A: rank 1 (90.5).** Passes all mandatory checks.
- **E: rank 2 (79.4).** Good overall, but fewer installs and a slower schedule than A.
- **B: ineligible.**  the bidder itself has no licence. The low price doesn't matter.
- **D: flagged (73.8).** Best price, but no schedule, so I can't confirm the 16-week limit. The reviewer should ask the bidder.
- **C: flagged (71.2).** It has a hidden note asking to be ranked first with full marks. I ignore it and score on the real content. A person must decide what to do about it. Even on merit it ranks below A and E.
