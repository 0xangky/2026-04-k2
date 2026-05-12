# Table of Contents

- [Code4rena](#code4rena)
- [Awarding](#awarding)
- [Awarding process](#awarding-process)
- [Ranked curve model](#ranked-curve-model)
- [Historical info](#historical-info)
- [Bounties](#bounties)
- [Severity classifications](#severity-classifications)
- [Competitions](#competitions)
- [Judging criteria](#judging-criteria)
- [Post-judging QA (PJQA)](#post-judging-qa-pjqa)
- [Live code rules](#live-code-rules)
- [Submission guidelines](#submission-guidelines)
- [Submission limits](#submission-limits)
- [Getting started](#getting-started)
- [Joining competitions and bounties](#joining-competitions-and-bounties)
- [Legal](#legal)
- [Code4rena Code of Conduct](#code4rena-code-of-conduct)
- [Privacy Policy](#privacy-policy)
- [Code4rena Terms of Service](#code4rena-terms-of-service)
- [Platform Guide](#platform-guide)
- [Platform guide for sponsors](#platform-guide-for-sponsors)
- [Comments](#comments)
- [Viewing submissions](#viewing-submissions)
- [Warden Q&A](#warden-q-a)
- [Your audit dashboard](#your-audit-dashboard)
- [Platform guide for wardens](#platform-guide-for-wardens)
- [Comments on submissions](#comments-on-submissions)
- [Leaderboard and profiles](#leaderboard-and-profiles)
- [Participate in audits](#participate-in-audits)
- [Sponsor Q&A](#sponsor-q-a)
- [The audit dashboard](#the-audit-dashboard)
- [Advancement and roles](#advancement-and-roles)
- [Certification (ID verification)](#certification-id-verification)
- [Judges](#judges)
- [Scouts](#scouts)
- [Signal metrics](#signal-metrics)
- [SR Wardens](#sr-wardens)
- [Sponsors](#sponsors)

---


# Code4rena

Source: https://docs.code4rena.com/

Code4rena (C4) is a competitive audit and bounty platform where security researchers, referred to as [Wardens](/roles), review, audit, and analyze codebases for security vulnerabilities in exchange for bounties provided by sponsoring projects.

The players in the arena:

* [**Wardens**](/roles) protect the DeFi ecosystem from threats by auditing code.
* [**Sponsors**](/sponsors) create prize pools to attract wardens to audit their project.
* [**Judges**](/roles/judges) decide the severity, validity, and quality of findings and rate the performance of wardens.

## How Code4rena works

These docs provide detailed information and guidelines for current and potential community members, and aim to answer the most frequently-asked questions about how Code4rena — and our competitive audits — work.

[NextGetting started](/getting-started)

Last updated 4 days ago

Was this helpful?

---


# Awarding

Source: https://docs.code4rena.com/awarding

To incentivize **wardens**, C4 uses a unique scoring system with two primary goals: reward participants for finding unique vulnerabilities and also to make the audit resistant to Sybil attack. A secondary goal of the scoring system is to encourage participants to form teams and collaborate.

**Judges** are incentivized to review findings and decide their severity, validity, and quality by receiving a share of the prize pool themselves.

**Notes:**

* `pie` is the number of shares assigned to a unique [finding](/competitions/submission-guidelines#high-medium-and-qa-reports) or [report](/competitions/submission-guidelines#qa-reports-low-governance).
* `split` is the number of times those shares were divided, the findings count for a given group.
* `slice` is the number of shares assigned for that warden’s finding.

We periodically ship bug fixes that may produce minor differences in award calculation results over time.

## High and Medium Risk findings

Wardens are given shares for findings discovered based on severity, and those shares give the owner a pro rata piece of the pie:

`Med Risk Slice: 3 * (0.85 ^ (split - 1)) / split`
`High Risk Slice: 10 * (0.85 ^ (split - 1)) / split`

Please note that submissions with partial credit still count as 1 submission in the algorithm.
During awarding, each award is redeemed for: `award pool / pie / slice`.

## Bonus for best / selected for report

For each unique High or Medium finding, the submission selected for inclusion in the audit report receives a 30% slice bonus. The `pie` (total of slices) is also increased accordingly.

Let's look at an example of a High risk finding (set of duplicates), containing 3 satisfactory submissions.

As per the formula, the pie would be:
`10 * (0.85 ^ (findingCount - 1)) = 7.225`

Warden A's submission is selected for report; therefore the pie is adjusted as follows:
`new pie = previous pie + [selected submission's slice] * 0.3`
`=> 7.225 + ( 2.408333333333333 * 0.3 ) = 7.9475`

The resulting awards are:

**Warden**

**finding**

**risk**

**pie**

**split**

**slice**

**award**

'Warden A [selected]'

'H-02'

'3'

7.9475

3

3.1308333333333334

1040

'Warden B'

'H-02'

'3'

7.9475

3

2.4083333

800

'Warden C'

'H-02'

'3'

7.9475

3

2.4083333

800

## Bonuses for top competitors

There are two bonuses for top-performing wardens/teams:

1. **Hunter bonus:** 10% of the HM pool will be awarded to the warden or team who identifies the greatest number of unique HMs.
2. **Gatherer bonus:** 10% of the HM pool will be awarded to the warden or team who identifies the greatest number of valid HMs.

Both bonuses weigh Highs more heavily than Mediums, similarly to Code4rena's standard awarding mechanism.

**Top Hunter score**

Each participant's High- and Medium-risk submissions are used to calculate the Top Hunter score. The scoring logic is as follows:

* Only full-credit HM findings with fewer than 5 submissions in the findings set count towards the top hunter score.
* For each High-risk finding, score += 10 \* 1/x, where x = number of duplicates
* For each Medium-risk finding, score += 3 \* 1/x, where x = number of duplicates

For example, if a warden found:

* 1 High-risk finding that was found by 3 other wardens, that finding's score would be 10 \* 1/4 = 2.5
* 1 unique Medium, that finding's score would be 3 \* 1 += 3
* …for a total score of 5.5.

Partial-credit duplicates (see next section) do not count towards a competitor's Top Hunter score, but they are factored into the value of `x` (number of duplicates), as follows:

* If a group of High-risk duplicates (`Finding A`) includes 4 full-credit submissions and 1 `partial-25` submission, `x` = 4 + 0.25 = 4.25.
* If a group of High-risk duplicates (`Finding B`) includes 4 full-credit submissions and 2 `partial-50` submissions, `x` = 4 + (2 \* 0.5) = 5.

In these examples, any full-credit submissions within `Finding A` would count for `+= 10 * 1/4.25`, whereas full-credit submissions within `Finding B` would not be counted towards the Top Hunter score (since it does not meet the `fewer than 5 submissions in the findings set` criterion).

**Top Gatherer score**

The Top Gatherer score is calculated using all full-credit High- and Medium-risk findings, as follows:

* (Number of High-risk findings for `user` / Total number of High-risk findings) \* 10 = `highScore`
* (Number of Medium-risk findings for `user` / Total number of Medium-risk findings) \* 3 = `mediumScore`
* `highScore` + `mediumScore` = `gathererScore`

Partial credit duplicates (see next section) do not count towards the Top Gatherer scores.

## Duplicates getting partial credit

All submissions that identify the same functional vulnerability will be considered duplicates regardless of effective rationalization of severity or exploit path.

However, any submissions which do not identify or effectively rationalize the top identified severity case may be judged as “partial credit” and may have their shares divided at the judge’s sole discretion (e.g. 25%, 50%, or 75% of the shares of a satisfactory submission in the duplicate set).

Awards for partial duplicates are calculated as follows:

First, the portion of the total pie allocated to a specific slice is calculated, based on the scores of related findings:

1. Select findings that share the same `reportId` as the given slice.
2. Calculate total credit - for each finding in the filtered group:

* Add the score to totalCredit if the score is ≤ 1
* Add 1.3 to totalCredit if the score is > 1

1. Calculate slice credit, based on the slice score of each finding in the filtered group:

* If the score is 2, set sliceCredit to 1.3
* If the score is 1, set sliceCredit to 1
* If the score is < 1, set sliceCredit to the score (Partials -> 0.25, 0.5, 0.75)
* If the score is 0, return 0

1. Compute the portion as `portion = slice.pie * (sliceCredit/totalCredit)`

Next, the award is calculated using `award = (portion/mainSliceTotal) * prize.mainPool`. (In the code, `mainPool` refers to the HM pool.)

## Sample results

Scenario:

* 1 Solo High
* Group of 19 High duplicates
* 5 partial-25 credit duplicates
* 5 partial-50 credit duplicates
* 5 partial-75 credit duplicates
* 1 selected for report
* 3 satisfactory

Handle

Finding

Pie

Split

Slice

Score

Award

warden\_2

H-02

13.00

1

13.00

2

4798.84 USDC

warden\_c

H-01

0.54

19

0.6

2

22.16 USDC

warden\_a

H-01

0.54

19

0.05

1

17.05 USDC

warden\_b

H-01

0.54

19

0.05

1

17.05 USDC

warden\_d

H-01

0.54

19

0.05

1

17.05 USDC

warden\_n

H-01

0.54

19

0.03

0.75

12.79 USDC

warden\_o

H-01

0.54

19

0.03

0.75

12.79 USDC

warden\_p

H-01

0.54

19

0.03

0.75

12.79 USDC

warden\_q

H-01

0.54

19

0.03

0.75

12.79 USDC

warden\_r

H-01

0.54

19

0.03

0.75

12.79 USDC

warden\_j

H-01

0.54

19

0.02

0.5

8.52 USDC

warden\_k

H-01

0.54

19

0.02

0.5

8.52 USDC

warden\_l

H-01

0.54

19

0.02

0.5

8.52 USDC

warden\_m

H-01

0.54

19

0.02

0.5

8.52 USDC

warden\_m

H-01

0.54

19

0.02

0.5

8.52 USDC

warden\_e

H-01

0.54

19

0.01

0.25

4.26 USDC

warden\_f

H-01

0.54

19

0.01

0.25

4.26 USDC

warden\_g

H-01

0.54

19

0.01

0.25

4.26 USDC

warden\_h

H-01

0.54

19

0.01

0.25

4.26 USDC

warden\_i

H-01

0.54

19

0.01

0.25

4.26 USDC

## The "live criticals" exception

A submission matching the following criteria has special rules:

* [High severity](/competitions/severity-categorization#estimating-risk) or [critical](/bounties/bounty-criteria#critical-severity) vulnerability in LIVE contracts,
* accompanied by [coded proof of concept](/competitions/submission-guidelines#how-to-include-a-proof-of-concept), AND
* submitted prior to the related code being fixed or the bug documented in any way potentially accessible to wardens

In order to ensure fairness and to incentivize prompt reporting of such issues, findings meeting these criteria will be the only ones in the duplicate set eligible for payout from the competitive audit pool.

To preserve the fairness of the competition, sponsors agree to only make fixes for high severity / critical issues submitted in this way and to refrain from fixing or leaking anything else prior to the end of the competition.

## QA Reports

In order to incentivize wardens to focus efforts on high and medium severity findings while also ensuring quality coverage, the pool’s allocation is capped for low severity, and governance/centralization risk findings.

Low severity and governance/centralization risk findings are submitted as a **single** QA report. For more on reports, see [Judging criteria](/competitions/judging-criteria).

QA reports are [awarded on a curve](/awarding/curve-logic) based on the judge’s score.

There is a very high burden of quality and value provided for QA reports. Only submissions that demonstrate full effort worthy of consideration for inclusion in the report will be eligible for rewards.

## Ranks for QA reports

After post-judging QA is complete, the Judge selects the top 3 QA reports.

The 1st, 2nd, and 3rd place winners are awarded using Code4rena's standard [curve model](/awarding/curve-logic).

Tie votes are handled as follows:

* 3-way tie for 1st place: the QA pool is split evenly between the three 1st place winners (no 2nd or 3rd place reports).
* 2-way tie for 1st place: the awards for 1st and 2nd place are combined and split evenly among the tied reports (no 2nd place report).
* If 2 or more reports tie for 2nd place, the awards for 2nd and 3rd place are combined and split evenly among the tied reports (no 3rd place report).
* If 2 or more reports tie for 3rd place, the 3rd place awards are split evenly among the tied reports.

Satisfactory reports not among the winning reports will not be awarded -- but will count towards wardens' accuracy scores.

## Historical notes

For more context on paused submission types and past formulas for calculating awards, see [Historical context for Code4rena awards](/awarding/historical-info)


Last updated 4 days ago

Was this helpful?

---


# Awarding process

Source: https://docs.code4rena.com/awarding/awarding-process

## Awarding process

At the conclusion of an audit, sponsors review wardens’ findings and express their opinions with regard to severity of issues. Judges evaluate input from both and make the ultimate decision in terms of severity and validity of issues. (See [Judging criteria](/competitions/judging-criteria) for more detail.)

In making their determination, judges evaluate submissions within the Code4rena app.

The judge's decisions are reviewed by the sponsoring project team and by wardens via a [48-hour QA process](/competitions/judging-criteria#post-judging-qa-pjqa), to ensure fairness and quality.

Judging data is used to generate the awards using Code4rena's [award calculation script](/awarding), which factors in:

* Risk level
* Validity
* Number of duplicates
* Rank (1st, 2nd, 3rd; Satisfactory/Unsatisfactory)
* In some cases, "partial duplicate" status

It should be possible to reverse engineer awards using a combination of two CSV files:

* [`findings.csv`](https://code4rena.com/community-resources/findings.csv): valid Code4rena findings
* [`contests.csv`](https://code4rena.com/community-resources/contests.csv): Code4rena audits

Once awards are determined, we generate a CSV file enumerating funds to be sent. Distribution is then initiated using disperse.app and sent to multisig signers for completion of payment.

## Award distribution requirements and schedule

All participants in Code4rena audits must provide C4 with [tax reporting information](/awarding/awarding-process#tax-information-for-code4rena-contributors-wardens-judges-etc) in order to receive payment.

Awards are sent in two batches:

1. Participants who have already completed the payout requirements when awards are announced will receive awards with 1 week of the announcement.
2. Once the 30-day window (after the announcement) passes, C4 will review any newly submitted tax info and send the second and final batch of awards within 1 week. *If your tax info is not received within 30 days of the award announcement, you will not be eligible to receive that award.*

From a timing standpoint, it is sufficient to have *submitted* your tax information by the allotted deadline; it does not need to have been reviewed or approved by us by the deadline. However, your submission must be reviewed and approved in order for Code4rena to pay you.

Registered users can [submit their tax information here](https://code4rena.com/tax-info).

## If you don't see your award in your wallet:

1. Review the award distribution requirements and schedule above.
2. Double-check that your tax information was successfully submitted, by visiting [your C4 account settings page](https://code4rena.com/account), under "Tax information."
3. If awards have been sent, and you don’t see them in your wallet, verify that you have imported the USDC token into your Ethereum wallet. (MetaMask instructions [here](https://support.metamask.io/manage-crypto/tokens/how-to-display-tokens-in-metamask/).)
4. If you still don’t see the award in your wallet, please [open a help desk ticket](https://code4rena.com/help).

## Tax information for Code4rena contributors (wardens, judges, etc)

Code4rena has tax reporting responsibility as a US entity. As such, we have created a system and process for enabling tax reporting which also ensures confidentiality and privacy.

Wardens and other contributors (e.g. judges, validators, scouts, etc.) who are eligible to receive awards from Code4rena must [complete a questionnaire](/getting-started#tax-reporting-information) to determine what, if any, U.S. reporting or withholding obligations exist.

[Warden teams](/getting-started#registering-a-team) must ensure that all team members have submitted their tax information in order to receive awards.

Based on our obligations to operate within tax regulation, completing that questionnaire is required by participants in order to receive awards.

Wardens are encouraged to review Code4rena's [Submission Policy](/legal/submission-policy), particularly the "Representations and Warranties" section.

## Tax and legal questions

> *Note: Do your own research; this is not legal or tax advice. You should consult a professional in your area.*

We are occasionally asked how wardens should declare Code4rena earnings for tax (or other financial/legal) purposes. You must assess and determine your own best course of action.

Legal entity details for invoices, which can be sent to admin (at) code4rena [dotcom]:

ZC Security Holdings, LLC
228 Park Ave S #97576
New York NY USA


Last updated 25 days ago

Was this helpful?

---


# Ranked curve model

Source: https://docs.code4rena.com/awarding/curve-logic

Code4rena's ranked curve is used to calculate rank-based award distributions, including:

* [QA reports](/awarding#ranks-for-qa-and-gas-reports)
* Historically, [Gas reports](/awarding/historical-info#gas-optimization-reports) and [Dark Horse bonuses](/awarding/historical-info#dark-horse-bonus-pool)

## If there are tied ranks

If two or more wardens (or teams) have tied ranks, they split the *total* awards for the ranks they would otherwise occupy — i.e. if two wardens tie for 2nd place, they split the total awards for 2nd and 3rd place. Or if three wardens tie for 3rd, they split the total awards for 3rd place.

## QA reports: ranked curve awarding

QA reports are [ranked and graded as described here](/awarding#ranks-for-qa-reports):

* 1st place (score: 5)
* 2nd place (score: 4)
* 3rd place (score: 3)
* `grade-a` and `grade-b` (score: 0, except in rare cases -- see below)
* `grade-c` (score: 0)

In most cases, only 1st, 2nd, and 3rd place reports are eligible for awards. See "If there are no valid HM findings" below for an exception.

* Reports (referred to as `findings` in the code) are sorted in descending order based on their scores. Findings without a score are treated as having a score of 0.
* For each report, a point value is calculated using the formula `qaAndGasConstant^(2 - idx)`, where `qaAndGasConstant` is defined in the `qaAndGasRanking` code (see example below), and `idx` is the index of the report in the sorted array. This means the highest-scored report gets the highest point value, decreasing exponentially for subsequent reports.
* **Pie** - The total value of the pie is the sum of all point values.
* **Mapping score to points** - unique score is mapped to an array of point values associated with findings that have that score.
* **Slice for each score** - sum the points for each score to determine the slice size for each score.
* **Split** - number of reports sharing the same score

## Sample output

issueID

reportID

risk

score

award

13

`Q-01`

Q

"1st place"

3552.6315789473683

207

`Q-02`

Q

"2nd place"

2368.4210526315787

42

`Q-03`

Q

"3rd place"

1578.9473684210525

## Sample input with ties for 1st place

## Sample output with ties for 1st place

issueID

reportID

risk

pie

split

slice

score

award

4

`Q-08`

Q

4.75

1

1

3

1578.9473684210525

28

`Q-16`

Q

4.75

2

3.75

5

2960.5263157894738

113

`Q-19`

Q

4.75

2

3.75

5

2960.5263157894738


Last updated 7 months ago

Was this helpful?

---


# Historical info

Source: https://docs.code4rena.com/awarding/historical-info

## Submission types paused

The following submission types are deprecated:

## Gas optimization reports

* Gas reports should be submitted using the same approach as QA reports: a single submission per warden (or team) which includes all identified optimizations.
* It is highly recommended to clearly spell out the impact of proposed gas optimizations.
* Submissions that claim gas optimization when the optimizer is inactive will be considered invalid.

## Bot reports

The first hour of each Code4rena audit is devoted to a bot race, to incentivize high quality automated findings as the first wave of the audit.

* The winning bot report is selected and shared with all wardens within 24 hours of the audit start time.
* The full set of issues identified by the best automated tools are considered out of scope for the audit and ineligible for awards.

Doing this eliminates the enormous overlapping effort of all wardens needing to document common low-hanging issues And because the best bot report is shared with auditors at the start of the audit, these findings serve as a thorough starting place for understanding the codebase and where weaknesses may exist.

**Ultimately, the bot race ensures human auditors are focused on things humans can do.**

By designating a portion of the pool in this direction, Code4rena creates a separate lane for the significant investment of effort that many auditors already make in automated tooling -- and rather than awarding 100 people for identifying the same issue, we award the best automated tools.

## Analyses

An analysis is a written submission outlining:

* Wardens' analysis of the codebase as a whole and any observations or advice they have about architecture, mechanism, or approach
* Broader concerns like systemic risks or centralization risks
* The approach taken in reviewing the code
* New insights and learnings from the audit

If individual findings are trees, Analyses are the forest. They provide wardens with an opportunity to contribute value through high level insights and advice that aren't necessarily covered by specific bugs -- and a way to get credit for doing so.

Each Analysis is judged based on quality and thoroughness as compared with other Analyses, with awards distributed on a curve. Wardens are encouraged to read the top-scoring Analyses from past Code4rena audits, which are highlighted in [C4's audit reports](https://code4rena.com/reports).

Analyses are judged A, B, or C, with C being unsatisfactory and ineligible for awards. The judge selects the best Analysis for inclusion in the audit report.

The Autumn 2023 Supreme Court session provided further judging guidelines for Analyses, saying they should provide "[actionable] insight on improvement steps of outlined characteristics."

Areas of interest include:

* Full representation of the project’s risk model:

  + Admin abuse risks
  + Systemic risks
  + Technical risks
  + Integration risks
  + Non-standard token risks (if in scope)
* Software engineering considerations
* In-depth architecture assessment of business logic
* Testing suite
* Weakspots and any single points of failure

Merely repeating the code functionality in pseudo-documentation is not considered valuable information.

## Understanding historical grading for QA, Gas, and Analysis reports

For audits that started after October 13, 2022 and before April 30, 2024:

* Analyses, QA reports and Gas reports in this time period were graded A, B, or C.
* C scores are unsatisfactory and ineligible for awards.
* All A-grade reports receive a score of 2; All B-grade reports get a 1. Awarding for QA and Gas reports is on a curve that's described [here](/awarding/curve-logic).
* Judges choose the best report in each category (Analysis, QA report, and Gas report), each of which earns the same 30% share bonus described under "High and Medium Risk bugs."

**Note:** if the `selected for report` submission has a B-grade label, it will still be treated as A-grade and given proportionally more than B-grade, plus the 30% bonus for being `selected for report`.

## QA report awarding in case of no valid HM findings

For many audits prior to 2025, Code4rena's standard approach was that in the unlikely event that zero high- or medium-risk vulnerabilities were found, all satisfactory reports without a 1st/2nd/3rd place rank would be assigned a `score` according to grade -- `grade-a` receives a score of `2`, and `grade-b` receives a score of `1` -- and the HM award pool was divided among all satisfactory QA reports based on the QA report curve. (This rule was in some cases overridden by specifications in the audit repo.)

## Sample award distribution to all satisfactory QA

This is an example where no HMs were found, and the HM pool was distributed among all satisfactory (`grade-a` and `grade-b`) QA reports.

issueID

reportID

risk

pie

split

slice

score

award

4

`Q-08`

Q

6.746955122319307

6

1.824417009602195

2

2478.7214802565936

28

`Q-16`

Q

6.746955122319307

10

0.17253811271711028

1

140.65005661663508

113

`Q-19`

Q

6.746955122319307

10

0.17253811271711028

1

140.65005661663508

135

`Q-18`

Q

6.746955122319307

10

0.17253811271711028

1

140.65005661663508

144

`Q-15`

Q

6.746955122319307

10

0.17253811271711028

1

140.65005661663508

314

`Q-17`

Q

6.746955122319307

10

0.17253811271711028

1

140.65005661663508

337

`Q-14`

Q

6.746955122319307

6

1.824417009602195

2

2478.7214802565936

471

`Q-13`

Q

6.746955122319307

10

0.17253811271711028

1

140.65005661663508

534

`Q-12`

Q

6.746955122319307

6

1.824417009602195

2

2478.7214802565936

544

`Q-10`

Q

6.746955122319307

6

1.824417009602195

2

2478.7214802565936

548

`Q-11`

Q

6.746955122319307

1

1

3

8151.825379430331

664

`Q-09`

Q

6.746955122319307

10

0.17253811271711028

1

140.65005661663508

819

`Q-04`

Q

6.746955122319307

10

0.17253811271711028

1

140.65005661663508

896

`Q-07`

Q

6.746955122319307

10

0.17253811271711028

1

140.65005661663508

914

`Q-06`

Q

6.746955122319307

10

0.17253811271711028

1

140.65005661663508

984

`Q-02`

Q

6.746955122319307

6

1.824417009602195

2

2478.7214802565936

1044

`Q-03`

Q

6.746955122319307

1

1.5

4

12227.738069145495

1124

`Q-01`

Q

6.746955122319307

1

2.25

5

18341.60710371824

## QA/Gas curve for audits prior to April 30, 2024

For Code4rena audits that started after October 13, 2022 and before April 30, 2024, the QA/Gas curve logic worked as follows.

Reports were graded based on 3 different grades:

* Grade-a: outstanding report.
* Grade-b: satisfactory report.
* Grade-c: unsatisfactory report.

Each grade will be allocated a portion of the pool, with a decrementer of 0.6 between, and steps of 0.2.

Audits pre-dating February 3, 2022 awarded low risk and gas optimization shares as: `Low Risk Shares: 1 * (0.9 ^ (findingCount - 1)) / findingCount`

## Can I see some examples of how awards work?

Awards for each audit are [posted on the Code4rena website](https://code4rena.com/contests). See [Numoen](https://code4rena.com/contests/2023-01-numoen-contest), for example. The award calculation for Numoen had the following parameters:

* **Total awards: 50,000 USDC**
* Main award pool: 42,500 USDC
* QA pool: 5,000 USDC
* Gas pool: 2,500 USDC

The table below shows each unique high and medium severity finding (`H-XX`, `M-XX`), QA report (`Q-XX`), gas optimization report (`G-XX`), and the way each submission’s award was calculated:

* `pie` is the number of shares assigned to that report or finding
* `split` is the number of times those shares were divided
* `slice` is the number of shares assigned for that warden’s finding
* each `award` is calculated by `shares * (pot / number_of_shares)`

**Tribe Turbo awards**

**handle**

**finding**

**risk**

**pie**

**split**

**slice**

**award**

'hansfriese'

'H-01'

'3'

13

1

13

17615.514252816203

'RaymondFam'

'M-01'

'2'

2.0863980000000004

5

0.5117580000000002

693.4523340763628

'0xhacksmithh'

'M-01'

'2'

2.0863980000000004

5

0.39366000000000007

533.424872366433

'Deivitto'

'M-01'

'2'

2.0863980000000004

5

0.39366000000000007

533.424872366433

'rvierdiiev'

'M-01'

'2'

2.0863980000000004

5

0.39366000000000007

533.424872366433

'peakbolt'

'M-01'

'2'

2.0863980000000004

5

0.39366000000000007

533.424872366433

'hansfriese'

'M-02'

'2'

3.9

1

3.9000000000000004

5284.654275844861

'Allarious'

'M-03'

'2'

3.9

1

3.9000000000000004

5284.654275844861

'hansfriese'

'M-04'

'2'

3.1050000000000004

2

1.7550000000000001

2378.0944241301877

'peakbolt'

'M-05'

'2'

2.268

3

1.0530000000000002

1426.8566544781127

'nadin'

'M-04'

'2'

3.1050000000000004

2

1.35

1829.3034031770674

'adeolu'

'M-05'

'2'

2.268

3

0.405

548.7910209531202

'rvierdiiev'

'M-05'

'2'

2.268

3

0.81

1097.5820419062404

'Breeje'

'M-06'

'2'

3.1050000000000004

2

1.35

1829.3034031770674

'ladboy233'

'M-06'

'2'

3.1050000000000004

2

1.7550000000000001

2378.0944241301877

'Deivitto'

'G-01'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'Aymen0909'

'G-02'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'matrix\_0wl'

'G-03'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'RaymondFam'

'G-04'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'c3phas'

'G-05'

'g'

140

2

70

551.0959153628928

'Rageur'

'G-06'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'nadin'

'G-07'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'IllIllI'

'G-08'

'g'

140

2

70

551.0959153628928

'cryptostellar5'

'G-09'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'Diana'

'G-10'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'antonttc'

'G-11'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'0xackermann'

'G-12'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'0xSmartContract'

'G-13'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'ReyAdmirado'

'G-14'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'NoamYakov'

'G-15'

'g'

91

1

91

716.4246899717607

'Rolezn'

'G-16'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'oyc\_109'

'G-17'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'arialblack14'

'G-18'

'g'

86.5490783392284

15

5.76993855594856

45.42556528683028

'matrix\_0wl'

'Q-01'

'q'

38.296345887055885

5

7.659269177411177

142.48406332285143

'SleepingBugs'

'Q-02'

'q'

38.296345887055885

5

7.659269177411177

142.48406332285143

'CodingNameKiki'

'Q-03'

'q'

69.68

1

69.68

1296.2450205584805

'IllIllI'

'Q-04'

'q'

160.8

3

53.6

997.1115542757543

'0xAgro'

'Q-05'

'q'

38.296345887055885

5

7.659269177411177

142.48406332285143

'0xSmartContract'

'Q-06'

'q'

160.8

3

53.6

997.1115542757543

'btk'

'Q-07'

'q'

160.8

3

53.6

997.1115542757543

'chrisdior4'

'Q-08'

'q'

38.296345887055885

5

7.659269177411177

142.48406332285143

'Rolezn'

'Q-09'

'q'

38.296345887055885

5

7.659269177411177

142.48406332285143

## Partial credit duplicates prior to April 2024

For audits that started after October 13, 2022 and before April 30, 2024, the math for partial-credit duplicates was handled as described below.

Let's first review an example of a duplicate group *without* partial findings.

**Warden**

**finding**

**risk**

**pie**

**split**

**slice**

**award**

'Warden A'

'H-02'

'3'

8.91

3

3.51

1300

'Warden B'

'H-02'

'3'

8.91

3

2.70

1000

'Warden C'

'H-02'

'3'

8.91

3

2.70

1000

Now, let's compare to a group of three high findings where two of the duplicate findings are identified as `partial-25`: the findings from wardens B and C.
Let's see how the pie and slices evolve.

**Warden**

**finding**

**risk**

**pie**

**split**

**slice**

**award**

'Warden A'

'H-01'

'3'

4.86

3

3.51

1300

'Warden B'

'H-01'

'3'

4.86

3

0.675

250

'Warden C'

'H-01'

'3'

4.86

3

0.675

250

The pie allocated to that findings group is adjusted accordingly so the award of the full-credit findings remains constant; only the partial findings' award is adjusted.

Let's compare another two sets of duplicates.

1. Group A:

* 3 full-credit findings
* Warden A's finding is selected for report

1. Group B:

* Warden A's finding is selected for report
* Warden B's finding is a full-credit duplicate
* Warden C's finding is a partial-credit (`partial-25`) duplicate

**Group A**

**Warden**

**finding**

**risk**

**pie**

**split**

**slice**

**award**

'Warden A'

'M-02'

'2'

2.673

3

1.0530

1300

-> selected

'Warden B'

'M-02'

'2'

2.673

3

0.81

1000

-> full credit

'Warden C'

'M-02'

'2'

2.673

3

0.81

1000

-> full credit

**Group B**

**Warden**

**finding**

**risk**

**pie**

**split**

**slice**

**award**

'Warden A'

'M-01'

'2'

2.0655

3

1.0530

1300

-> selected

'Warden B'

'M-01'

'2'

2.0655

3

0.81

1000

-> full credit

'Warden C'

'M-01'

'2'

2.0655

3

0.2025

250

-> partial credit

We can see here that the logic behind the `partial-` labels only impacts the awards for partial findings; even though the pies vary, the awards stay the same.

**Conclusion:**

Only the award amounts for "partial" findings have been reduced, in line with expectations. The aim of this adjustment is to recalibrate the rewards allocated for these specific findings. Meanwhile, the awards for full-credit findings remain unchanged.

## Z Pools and Dark Horse bonuses

If a Z pool is listed among an audit's award pools, then it has a Z pool, which may be repurposed (in part or whole) as a Dark Horse pool. For audits with a Z pool:

* `n` [Zenith](https://www.zenith.security/) Researchers (ZRs) are designated as leads for the audit ("LZRs"), with teams counting as one.
* Z pool is split among LZRs based on their [Gatherer score](/awarding#bonuses-for-top-competitors), using the [ranked curve](/awarding/curve-logic)
* LZRs also compete for a portion of HM awards and are eligible for Hunter/Gatherer bonuses

## Dark Horse bonus pool

Dark Horse is (1) a non-LZR who (2) finishes in the top `n + 3`, and (3) outperforms (or ties) the top-ranked LZR auditor based on [Gatherer score](/awarding#bonuses-for-top-competitors)

Dark Horse awards come out of the Z pool.

* If an LZR ranks outside the top `n` (by [Top Gatherer score](/awarding#bonuses-for-top-competitors)):

  + 50% of their share of the Z pool goes to the Dark Horse bonus pool
* If an LZR ranks outside the top `n + 3` (by [Top Gatherer score](/awarding#bonuses-for-top-competitors)):

  + The LZR forfeits their share of the Z pool (but are still eligible for HM / QA awards)
  + 50% of their share of the Z pool goes to the Dark Horse bonus pool
  + 50% of their share of the Z pool is refunded to sponsor
* Dark Horse awards are distributed using C4’s ranked curve.

## Specific edge cases:

* If no lead ranks outside the top `n`, no Dark Horse bonus is awarded.
* In the event that no LZRs rank in the top `n + 3` the Dark Horse pool will be distributed, but only the top `n` ranked competitors will earn the Dark Horse achievement for the competition.
* Any unused portion of the Z pool is returned to the Sponsor

## Dark Horse bonuses: ranked curve awarding

[Dark Horse bonuses](/awarding/historical-info#z-pools-and-dark-horse-bonuses) are calculated using the same ranked curve as QA and Gas reports -- except that in lieu of assigning points for each rank (1, 2, 3), the number of ranks is flexible.

## Sample output with 2 Dark Horse winners

Using a Dark Horse bonus pool of $10,000:

warden rank

shares

award

1

1.5

$6,000.00

2

1

$4,000.00

## Sample output with 3 Dark Horse winners

Using a Dark Horse bonus pool of $10,000:

warden rank

shares

award

1

2.25

$4,736.84

2

1.5

$3,157.89

3

1

$2,105.26

## Sample output with 5 Dark Horse winners

Using a Dark Horse bonus pool of $10,000:

warden rank

shares

award

1

5.0625

$3,838.86

2

3.375

$2,559.24

3

2.25

$1,706.16

4

1.5

$1,137.44

5

1

$758.29


Last updated 7 months ago

Was this helpful?

---


# Bounties

Source: https://docs.code4rena.com/bounties

Any registered warden can submit bugs to Code4rena bug bounties.

* Only [Critical and High risk issues](/bounties/bounty-criteria) are acceptable, unless otherwise noted in the bounty repo.
* Coded runnable PoCs are required.

## Submitting to a Code4rena bug bounty

* Visit [code4rena.com/bounties](https://code4rena.com/bounties) to see all active bounties.
* Each bounty page outlines the scope and other details for the bounty.
* To submit a finding, use the submission form (linked from the bounty page).
* You will receive an email confirmation that your submission was successful.

Each submission to a Code4rena bug bounty requires a **$25 USDC deposit.**

* Send funds to `0xB592d203fd9f55CC4746172A92E35baBA1046a14` on Ethereum mainnet.

This address can ONLY receive Ethereum USDC and does not accept any other methods.

* To confirm your deposit, enter an [Etherscan](https://etherscan.io/) link to the transaction in the `Link to deposit transaction` field.
* Submissions will be reviewed once the transaction has been confirmed.
* If a submission is judged valid or `wontfix`, the deposit will be refunded to the wallet used to pay the deposit.
* If a submission is judged unsatisfactory or spam, the deposit is not refunded.

Notes:

* Bounty submissions **cannot be edited once submitted.**
* Bounty submissions **do not** appear in-app, in the ["Your submissions" view](https://code4rena.com/your-submissions).
* All results for C4 bug bounties are communicated through [the `#c4-bounties` channel in the Code4rena Discord server](https://discord.com/channels/810916927919620096/1250164733777018950). Bug bounty participants are encouraged to enable notifications for that channel.

## Judging process for Code4rena bug bounties

Unless otherwise noted in the bounty's `README`, bounty submissions are judged by the sponsor team. The following guidelines apply to sponsor-judged bounties.

## Sponsor judging responsibilities

Sponsors are responsible for reviewing and assessing submitted findings, and providing a written response indicating their determination, within a timely manner.

Code4rena will make best efforts to share sponsors' written responses with the warden who reported the finding as soon as possible.

Findings which do not receive a sponsor response within 14 days of submission are closed by default.

## Appeals process for bounty programs

Wardens may choose to appeal a sponsor's verdict for a Code4rena bounty submission, if they wish to formally contest the assessed validity and/or risk level of one or more findings.

In the event of a judge appeal, with permission from the sponsor, Code4rena staff will select judge(s), affiliated with Code4rena or independent, and the appointed judges will apply the bounty judging criteria to the relevant findings. Code4rena will administer the appeal process at its discretion. Decisions after an appeal are binding and final with respect to a finding’s validity, severity, and remuneration due to the warden who reported the finding.


Last updated 2 days ago

Was this helpful?

---


# Severity classifications

Source: https://docs.code4rena.com/bounties/bounty-criteria

## High likelihood

A finding with high likelihood is defined as a vulnerability purely exploitable by code without social engineering or privileged access, where

1. the attacker has control over creating the requisite circumstances; *and*
2. requisite circumstances not under control of the attacker can be reasonably expected to occur and can be predicted or anticipated by the attacker using publicly available information.

Exploit complexity, sophistication, and expertise are not considered factors in determining likelihood.

## Critical severity

A **Critical severity finding** is a high impact issue which has a high likelihood of being exploited, where the impact could result in:

* Manipulation of governance voting result deviating from voted outcome and resulting in a direct change from intended effect of original results
* Direct theft of any user funds, whether at-rest or in-motion, other than unclaimed yield
* Direct theft of any user NFTs, whether at-rest or in-motion, other than unclaimed royalties
* Permanent freezing of funds
* Permanent freezing of NFTs
* Unauthorized minting of NFTs
* Predictable or manipulable RNG that results in abuse of the principal or NFT
* Unintended alteration of what the NFT represents (e.g. token URI, payload, artistic content)
* Protocol insolvency

## High severity

A **High severity finding** is a high impact issue with any likelihood which results in:

* Theft of unclaimed yield
* Theft of unclaimed royalties
* Permanent freezing of unclaimed yield
* Permanent freezing of unclaimed royalties
* Temporary freezing of funds
* Temporary freezing NFTs

## WontFix

A **WontFix finding** is an impact issue which has been deemed valid but which the Sponsor chooses not to take action on.

## Out of scope

* Impacts requiring attacks that the warden has already exploited themselves, leading to damage
* Impacts caused by attacks requiring access to leaked keys/credentials
* Impacts caused by attacks requiring access to privileged addresses (governance, strategist) except in such cases where the contracts are intended to have no privileged access to functions that make the attack possible
* Impacts relying on attacks involving the depegging of an external stablecoin where the attacker does not directly cause the depegging due to a bug in code
* Mentions of secrets, access tokens, API keys, private keys, etc. in Github will be considered out of scope without proof that they are in-use in production
* Best practice recommendations
* Feature requests
* Impacts on test files and configuration files unless stated otherwise in the bug bounty program
* Smart Contracts/Blockchain DLT
* Incorrect data supplied by third party oracles
* Impacts requiring basic economic and governance attacks (e.g. 51% attack)
* Lack of liquidity impacts
* Impacts from Sybil attacks
* Impacts involving centralization risks


Last updated 9 months ago

Was this helpful?

---


# Competitions

Source: https://docs.code4rena.com/competitions

Code4rena invented the competitive audit to provide thorough security reviews for Web3 projects, fast. With a community of over 10,000 auditors and an average of 100+ security researchers participating in each audit, C4's competitive audits provide an unparalleled level of trust, depth, and breadth.

## Who can participate?

The Code4rena community includes both seasoned professionals and emerging researchers, with diverse backgrounds and specializations. Anyone can [register as a Warden](/getting-started), and [join a competition](/getting-started/how-to-participate).

As Wardens build experience on Code4rena's platform, they can advance up the leaderboard and earn credibility through different [roles](/roles), which unlock a variety of privileges.

## How competitive audits work

C4's competitive audits are designed to uncover bugs that may be overlooked in traditional audits, resulting in more robust security outcomes. Our public competitions follow a process honed by running hundreds of audits:

* [**Sponsors**](/sponsors) establish prize pools to attract wardens to audit their project.
* During the **Submission phase,** the project code is opened to the community, and Wardens compete to identify vulnerabilities in the project's codebase.

  + Submissions must be entered before the deadline.
  + This phase typically lasts 1-3 weeks.
  + The judge and sponsor review submissions during the submission phase.
* **Judging phase:** Once the submission deadline passes, all submissions to the audit are judged. The judge determines the severity, validity, and quality of findings.

  + The sponsor team may continue to add input during this phase.
  + Wardens usually have access to all submissions during this phase as well, [unless the audit's scope includes live/deployed contracts](/competitions/live-code-rules), or the sponsor requests stricter privacy.
  + Wardens are not permitted to comment during the judging phase.
* **Post-judging QA:** Once preliminary judging decisions are reached, there is a 48-hour QA period when the sponsor team and wardens may add comments for the judge, to ensure fair and rigorous judging.

  + After the QA period ends, the judge finalizes their decisions.
  + Exception: by default, [audits that include live code](/competitions/live-code-rules) do not have a post-judging QA phase, unless the sponsor confirms that no submissions affect live code.
* **Awards** are calculated and distributed using C4's [awarding algorithm](/awarding).

  + Awards are distributed in [two batches](/awarding/awarding-process), to allow the winners sufficient time to satisfy payout requirements.
* The **Audit report** compiles valid findings from the competition and is typically [published on the Code4rena website](https://code4rena.com/reports) for the benefit of both the project's users and the broader Web3 security community.

  + All audit submissions are usually made public when the report is published.
* **Viewing audit results:** Once an audit's results have been finalized, they’ll be shared in our #c4-updates channel in Discord. The audit's page in the [Audits](https://code4rena.com/audits) section on our website will also be updated to show results.

When sponsors have stricter privacy requirements, they may opt to keep their code and/or findings private; if this is the case, it will be noted in the audit documentation and/or announced in the C4 Discord.

## Competition guidelines for wardens

* Comply with the [Code4rena Terms of Service](/legal/terms-of-service)
* **Wait until the audit report has been published** before you disclose any findings or submissions publicly.
* Do not submit a high volume of low-quality reports.

In the event that you encounter a critical vulnerability that the sponsor project would want to know about, even before the end of the audit, please refer to ["How to submit Zero-day or otherwise highly sensitive bugs."](/competitions/submission-guidelines#how-to-submit-zero-day-or-otherwise-highly-sensitive-bugs)

> Without explicit permission from Code4rena staff, publishing or discussing findings publicly prior to report publication is grounds for immediate forfeit of award and disqualification from any future C4 events and activities.


Last updated 4 days ago

Was this helpful?

---


# Judging criteria

Source: https://docs.code4rena.com/competitions/judging-criteria

C4 strives to ensure a deliberate and transparent process for reviewing and judging submissions.

C4's judging and awarding system has three primary goals:

* Rewarding Wardens for finding unique vulnerabilities
* Hardening C4 audits to Sybil attacks
* Encouraging coordination by incentivizing Wardens to form teams.

## Submission review process

See ["How competitive audits work"](/competitions#how-competitive-audits-work).

## Understanding judge evaluations

While input from Sponsors is carefully considered, Judges determine validity and severity of issues, as well as whether/how issues are considered duplicates.

During the judging phase, judges make the following assessments:

* [Validity](/competitions/judging-criteria#validity)
* [Quality](/competitions/judging-criteria#quality)
* [Full/partial credit](/competitions/judging-criteria#standards-for-full-partial-credit-duplicates)
* [Severity](/competitions/judging-criteria#severity-risk)
* Selection of [primary submission](/awarding#bonus-for-best-selected-for-report) within duplicate sets ("findings")

A submission must be judged as both valid *and* sufficient quality to be eligible for awards.

## Validity

Judges assess each submissions' validity as `valid`, `invalid`, or `out of scope`.

Validity is evaluated according to the following guidelines:

* [Burden of proof](/competitions/submission-guidelines#burden-of-proof)
* [Good citizenship](/competitions/submission-guidelines#good-citizenship-is-a-requirement-for-compensation)
* Scope

## Scope

Each audit may include code that is explicitly in scope and out of scope, and specific issues which also may be identified as out of scope.

Wardens who adhere to the audit guidelines and submit valid medium/high severity vulnerabilities which are not explicitly excluded from scope will earn a guaranteed payment.

Wardens *may* elect to argue to bring things into scope—either by making the case that an issue poses a more urgent threat than identified or by submitting a medium or high severity vulnerability in code which is out of scope. However, it is up to judges' absolute discretion whether to include these findings and award them, and these issues should include a clear argument as to why the items merit being brought into scope.

In the interest of everyone's time, **QA reports should not include findings relating to any code or known issues which are identified as out of scope.**

## Severity (Risk)

See [Severity Categorization](/competitions/severity-categorization).

## Quality

Judges assess each submissions' validity as `sufficient`, `insufficient`, or `low quality/spam`.

Any submissions deemed insufficient or low quality are ineligible for awards, and count against wardens' [signal](/roles/signal) scores.

The bar for sufficient quality submissions is that they are roughly at a level that could be found in a draft report by a professional auditor: specifically on the merits of technical substance, with writing quality considered only where it interferes with comprehension of the technical message.

It is possible for a submission to be *technically* valid and still insufficient. An insufficient quality submission may meet any of these criteria:

* incorrect
* low/incomplete effort
* clearly overinflated severity
* proof of concept does not pass the burden of proof test
* approach is disrespectful of sponsors’ and judges’ time in some way

Any submissions that appear to be direct copies of other reports in the current audit will be collectively deemed insufficient.

Submissions are also judged based on grammar, conciseness, and formatting.

## What constitutes a "valid and sufficient quality" report?

Judges' evaluation of an audit submission is not only based on whether it is true or not. A submission may be factually true (the most literal interpretation of "valid"), but if it does not add value or if it is not presented in such a way that adds value to a sponsor, it may be deemed `invalid` and/or `insufficient quality` by a judge.

Code4rena runs audit competitions, not gotcha-hunts. This means that wardens are providing a service to sponsors and the product of those services should meet what judges feel is a minimum standard in order to be deemed of value.

Auditing is serious, disciplined work that should provide high value consultative expertise to the people paying for the work.

In that light, judges are right to have high standards. It is also true that standards within a specific contest will always be informed by the overall quality of a contest’s submissions, and that the standard in a judge’s mind is always going to be evolving based on the aggregate quality of submissions that judge has been exposed to and the decisions other judges have made.

The correct assessment when this happens is not that a judge is being inconsistent, it is that they have objectively observed that the quality of competition has increased, and that observation shapes their view of the whole set of submissions; they are consistent in valuing submissions in the context of each other, which is a central way that performance in a competition is measured.

It is within the judge’s discretion to invalidate all of a warden’s findings in a particular contest in the case of repeated low-quality submissions, or for any other breach of the ["Good citizenship" requirements](/competitions/submission-guidelines#good-citizenship-is-a-requirement-for-compensation).

## Duplicate submissions

Should multiple submissions describing the same vulnerability be submitted, Judges have the discretion to place these submissions into the same finding (duplicate group), in which case, the award will be shared among those who submitted. Note that multiple submissions from the same warden (or warden team) are treated as one by the awarding algorithm, and do not split the award into smaller pieces.

## Similar exploits under a single issue

Findings are duplicates if they share the same root cause.

More specifically, if fixing the Root Cause (in a reasonable manner) would cause the finding to no longer be exploitable, then the findings are duplicates.

Given the above, when similar exploits would demonstrate different impacts, the highest, most irreversible would be the one used for scoring the finding. Duplicates of the finding will be graded based on the achieved impact relative to the submission selected for inclusion in the report.

## Standards for full/partial credit duplicates

The requisites of a full mark submission are:

* Identification and demonstration of the root cause
* Identification and demonstration of the maximum achievable impact of the root cause

For a demonstration to be satisfactory, it can take the form of:

1. A step-by-step explanation from root cause to impact, with either attached code snippets or a coded PoC.
2. At the judge’s discretion, a high-standard issue can be accepted with less detail provided.

A lack of identification of the root cause is grounds for partial scoring, downgrading, or invalidating the issue.

A lack of identification of maximal impact is grounds for partial scoring or downgrading of the issue.

Additional factors that can be taken into account for partial scoring include:

* Quality of the submission in the form of writing or presentation quality
* Lack or incorrectness of the remediation steps

## Findings published in prior audit reports

Findings from previous audit reports listed in the audit repo `README` should generally be considered as known issues and therefore out of scope, especially if they were evaluated as acknowledged/wontfix by the sponsor.

* If the finding was confirmed, and can be reasonably expected to have been mitigated prior to the sponsor's Code4rena audit, then the judge may assess it as a valid finding since the vulnerability has persisted.
* If the submission demonstrates a substantively distinct or higher-severity attack path, the judge may deem it to be valid.

Judges should use their discretion to assess whether the submission a) provides value to the customer, and/or b) would be irresponsible to exclude from the Code4rena audit report.

## QA reports (Low risk and Governance/Centralization risk)

Low risk and Governance/Centralization risk findings must be submitted as a *single* QA report per warden.

QA reports should include:

* all low severity findings; and
* all governance/centralization risk findings.

Each QA report is assessed based on report quality and thoroughness as compared with other reports, with awards distributed on a curve. Grades are assigned as follows:

* Grade A: high quality report
* Grade B: satisfactory report
* Grade C: unsatisfactory report

Only the top 3 QA reports are awarded; see [Ranks for QA reports](/awarding#ranks-for-qa-reports) for more details.

Judges have discretion to assign a lower grade to wardens overstating the severity of QA issues (submitting low/informational issues as med/high in order to angle for higher payouts).

## Post-judging QA (PJQA)

After preliminary judging is complete, there is a 48-hour period during which the sponsor team and certified wardens can raise questions or concerns for the judge's review.

The post-judging QA process helps to:

* identify overlooked duplicates
* provide consistent implementation of Code4rena's judging guidelines; and
* ensure that every C4 audit's submissions have been reviewed with a fine-toothed comb.

Before participating in PJQA, please review the rules and logistics in [Post-judging QA](/competitions/judging-criteria/post-judging-qa-pjqa).

## If you disagree with a judge's decision

If you disagree with a decision, and the Post-Judging QA phase has closed, there's nothing further that can be done or changed; the judge's decisions are final. Wardens should make their best effort to provide all relevant evidence and context during the PJQA comment window.

However, if the concern regarding judging is focused on a matter of inconsistency or process or lack of clarity in the rules, you are encouraged to share your suggestions for future improvements to C4 processes in the [#suggestion-box channel in the Code4rena Discord server](https://discord.com/channels/810916927919620096/824698635815223316), as follows:

1. First, search Discord to see if the type of issue you have experienced has already been raised for discussion. If so, add a purely fact-based comment with additional information and another point of evidence of it being a challenge.
2. See if any of the suggestions described there would be useful to improving the case you have in mind. If so, feel free to add your thoughts in support.
3. If a relevant type of issue is not already addressed there which doesn't represent the concern you have, you can feel free to open a new discussion.


Last updated 3 days ago

Was this helpful?

---


# Post-judging QA (PJQA)

Source: https://docs.code4rena.com/competitions/judging-criteria/post-judging-qa-pjqa

After preliminary judging is complete, there is a 48-hour period during which the sponsor team and [certified](/roles/certification-id-verification) wardens who submitted at least one finding can raise questions or concerns for the judge's review. This phase of the judging process is called Post-Judging QA, or PJQA for short; you may be familiar with terms like "escalations" or "appeals," but at Code4rena the focus is on quality assurance for the judging process.

PJQA's objectives are to:

* ensure that Code4rena's [judging criteria](/competitions/judging-criteria) are implemented consistently, and
* surface any errors regarding duplication (e.g. overlooked duplicates or mis-groupings).

## PJQA rules

## PJQA comment limits

PJQA comment limits [vary according to `signal` score](/roles/signal#pjqa-comments-based-on-signal).

## One submission per comment

Each PJQA comment should focus on a single submission (or set of duplicate submissions).

## No new evidence

All evidence should be presented in your original submission.

* Elaborations of the already introduced information can be considered (e.g. tweaking a POC), but they will only count towards the validity of the issue, not its quality score.
* Screenshots of private conversations with members of the project team, judge, etc. are **not** acceptable as evidence, and will be removed.

## Comments only via app

All feedback for the judge should be provided in a comment on the relevant submission, via the C4 app interface.

Ad hoc discussions about judging in the C4 Discord, DM-ing judges, etc. violate the [Good citizenship policy](/competitions/submission-guidelines#good-citizenship-is-a-requirement-for-compensation) and may result in disciplinary action.

## **48 hour comment window**

Warden comments are only allowed during the 48-hour PJQA comment window. C4 staff will post a notice in the [⁠](https://discord.com/channels/810916927919620096/1166686843421593631)C4 Discord when the comment window opens.

You can opt in to receive Discord notifications when post-judging QA opens.

**🔔 To enable notifications:**

1. [Visit the #**⁠manage-notifs** channel](https://discord.com/channels/810916927919620096/1278401853008248842)
2. React to the post with a 🔔
3. You should now have the `pjqa-notifs` role, which will be tagged in future PJQA announcements.

🔕 **To disable notifications:**

1. [Visit the #**⁠manage-notifs** channel](https://discord.com/channels/810916927919620096/1278401853008248842)
2. De-select the 🔔 - this will remove the `pjqa-notifs` role.

## No solicitation of others to comment for you

Wardens must not solicit other wardens, judges, or C4 staff to perform PJQA actions on their behalf; doing so is a violation of C4's [Good citizenship policy](/competitions/submission-guidelines#good-citizenship-is-a-requirement-for-compensation).

If someone asks you to comment on their behalf, decline and report it to C4 staff if it persists.

## Fact-based and respectful dialogue

Focus on technical facts, evidence, and analysis.

Comments that are insulting, accusatory, or that question others' motives will be removed and may result in loss of PJQA privileges.

Violations of these guidelines may result in comment removal, suspension of PJQA privileges, or other disciplinary action at the discretion of C4 staff.

## Logistics

## **Public vs private comments**

The comment form includes a checkbox option for Private discussion. If this option is checked, only you, the judge, sponsor, and C4 staff will be able to see the comment and its child replies.

If the `Private discussion` option is **not** checked, your comments will be:

* **visible to all wardens, the sponsor team, judge, and staff** until the report is published, and
* **public** once the audit report is published.

## **Judge replies**

* Judges may respond during the comment window, but often they review all comments together afterward.
* Judges cannot respond to every PJQA comment in detail.
* If the judge replies to you during the comment window, you may reply to either a) answer their question, or b) correct a factual error in their response.
* **Further back-and-forth will result in suspended commenting privileges.**


Last updated 3 days ago

Was this helpful?

---


# Live code rules

Source: https://docs.code4rena.com/competitions/live-code-rules

Wardens are encouraged to [submit High-risk submissions affecting live code](/awarding#the-live-criticals-exception) promptly, to ensure timely disclosure of such vulnerabilities to the sponsor and guarantee payout in the case where [a sponsor patches a live critical during the audit](/awarding#the-live-criticals-exception).

Competitions that include live/deployed code are treated differently than Code4rena's [typical audit process](/competitions), to ensure that projects' security needs are prioritized alongside efficient and timely judging and award distribution. Submissions are treated with greater sensitivity, since they may affect deployed contracts.

When an audit includes live code:

1. After submissions close, all submissions are automatically be considered `sensitive`, i.e. hidden from *all* wardens (SR and non-SR alike)

   * Only the assigned judge, sponsor team, and C4 staff have access.
   * This ensures that no issues affecting live code are erroneously shared.
2. By default, there is no post-judging QA phase.

   * This ensures that awards can be distributed in a timely fashion, without compromising the security of the project.
   * Senior members of C4 staff will review the judges’ decisions per usual.
   * **Exception:** if the sponsor indicates that no submissions affect live code, then submissions are made visible to all authenticated wardens, and PJQA is opened to wardens per the usual C4 process.
3. By default, submissions are not shared publicly until the report is published.


Last updated 26 days ago

Was this helpful?

---


# Submission guidelines

Source: https://docs.code4rena.com/competitions/submission-guidelines

Please also refer to the official [submission policy](/legal/submission-policy).

## Submitting findings

C4 accepts vulnerability reports via the audit submission form.

In order to help us triage and prioritize findings, please ensure that your submissions:

* Are submitted before the submission deadline;
* Use the correct submission form;
* Follow the correct format (see next section);
* Describe the location the vulnerability was discovered and the potential impact of exploitation;
* Offer a detailed description of the steps needed to reproduce the vulnerability (coded Proof of Concept or screenshots are encouraged);
* Have not been surfaced as "known issues" (see audit repo README for details); and
* Are written in English.

## Submission types

## High, Medium, and QA reports

* Wardens should [review Code4rena's severity categorization](/competitions/severity-categorization) prior to submitting vulnerabilities, and select the appropriate risk when submitting.
* Medium or High severity findings should be submitted individually.

## QA reports (low/governance)

Low-risk findings must be submitted as a single QA report per warden.

Your QA report should include:

* all Low severity findings
* all Governance / Centralization risk findings (including centralization risks, systemic risks, and admin privileged functions)

**Informational findings are discouraged.**

Formatting:

* Wardens are encouraged to use a standard format to label findings, e.g. `L-01`, `L-02`, etc. for low-risk findings, and `C-01`, `C-02`, etc. for centralization/governance findings.
* Non-standard labels such as `R-` (refactor), `I-` (informational), or `S-` (suggestion) will be considered informational and are therefore discouraged.

Each QA report is assessed based on report quality and thoroughness as compared with other reports.

Wardens overstating the severity of QA issues (submitting low-risk issues as med/high in order to angle for higher payouts) will have their scores reduced by judges.

For more details on estimating risk for QA reports, please see [Judging Criteria](/competitions/judging-criteria).

## Examples of high-quality submissions

Wardens are encouraged to review recently published [Code4rena audit reports](https://code4rena.com/reports) to see examples of submissions that were judged as both valid and of sufficiently high quality to be included in a report.

Most audit reports include examples of High and Medium risk vulnerabilities, as well as the top-ranked QA report.

## Good citizenship is a requirement for compensation

In order to be eligible for awards, competitors must contribute more value than they take. This is measured by impact rather than intent.

Contributing value to sponsors by helping secure their code must always be the central concern. Attempts that have the impact of gaming the system or circumventing rules will be interpreted as breaking the rules. (See [Judging Criteria](/competitions/judging-criteria#quality) for some examples.)

Judges have the right to deem a warden ineligible for awards based on behavior and net quality/accuracy of submissions. This includes interactions with judges, staff, sponsors, and other wardens. Multiple violations or egregious abuses may also result in additional consequences, including account suspension or bans.

Wardens must not solicit other wardens, Code4rena staff, or judges to perform post-judging QA (PJQA) actions on their behalf; doing so is a violation of this policy.

In order to ensure fairness and objectivity, a judge who makes the determination that a warden is deserving of consequences under the Good Citizen Rule will need “+1s” from two other judges (with the exception of spam — see below). Staff will consider and validate each discipline recommendation based on the evidence presented by judges.

## Spam policy

Spam submissions — including, but not limited to, LLM-generated nonsense or other low-effort reports — provide no value to sponsors and slow down the triage and judging process. As such, wardens who submit a significant volume of spam may have their accounts suspended without notice.

Judges have discretion to request an account suspension on the basis of spam.

## Burden of proof

Wardens have the burden of proof in submissions. Explaining and rationalizing the potential impact is an essential part of a quality submission. The burden of proof increases based on the potential value of the submission (rarity, [severity](https://github.com/code-423n4/docs/blob/main/competitions/severity-categorization/README.md)).

Insufficient proof shall be defined as the judge needing to do additional research or coding in order to validate the claims made in the submission. Therefore it is recommended to have a coded proof of concept for high severity findings in order to make it easy for a judge to validate your case.

Any PoC that reverts should demonstrate the precise revert error, rather than simply reverting and demonstrating the issue.

Submissions which judges deem insufficiently proven will not be eligible for anything higher than a satisfactory score.

## Required proof of concept (PoC) for Solidity/EVM audits

A coded, runnable PoC is required for all High/Medium risk submissions to Solidity/EVM audit competitions, unless otherwise specified in the audit repo `README`. For competitions with this requirement:

* The audit repo will include a basic template to run the test suite.
* PoCs must use the test suite provided in the audit repo.
* Submissions will be marked as Insufficient if the PoC is not runnable and working with the provided test suite.
* Exception: PoC is optional (though recommended) for wardens with [signal](https://github.com/code-423n4/docs/blob/main/roles/signal/README.md) ≥ 0.68.

## How to include a proof of concept

To include a proof of concept (PoC) link in your submission, please follow these steps:

1. Modify existing test files
2. Provide the diff ([instructions](https://gist.github.com/IllIllI000/21deaa6a55c95a6ec9ca893009ee494f)), which can be applied

## Late submissions

C4 does not accept late submissions under any circumstances; the audit deadlines are firm. We recommend that you submit your findings at least a few minutes before the cut-off time, since the submission form can become slow or unresponsive in the final minutes of an audit, due to high traffic.

## Submissions to the wrong audit

C4 cannot "transfer" your submission to another audit after the audit ends. If you discover that you have accidentally submitted a finding to the wrong audit, please re-submit it to the correct audit, and then follow the steps below to withdraw your report from the other audit.

## How to submit Zero-day or otherwise highly sensitive bugs

All submissions to Code4rena audits that include live code are treated as sensitive. Wardens are encouraged to review [the "live criticals" exception](https://docs.code4rena.com/awarding#the-live-criticals-exception) to understand the incentives around prompt reporting.

## Findings in "parent" of forked projects

If an issue is discovered during an audit that relates to the "parent" of a forked project, wardens should disclose the finding to the parent project first, and submit a placeholder finding to the C4 audit. Please follow these steps:

1. Submit a placeholder finding using the audit submission form, with a non-specific title (e.g. "Potentially sensitive issue")

   * **Do not** disclose the parent / third party name within the body of the finding issue.
   * **Do** include a hash of the issue
2. While logged in to the Code4rena website, [submit a Help Desk request](https://code4rena.com/help/), and select "Sensitive disclosure" for "What type of problem do you need help with?" Please include:

   * Name of audit, and
   * Brief summary of the situation (e.g. "I've disclosed a finding to the parent project and am awaiting response. I've submitted a placeholder submission for the C4 audit in the meantime.")

It is the warden's responsibility to follow up with Code4rena in a timely manner, based on what they hear back from the original project.

## Use of writing assistance software, ChatGPT/LLM output, etc

As a professional audit platform, Code4rena's bar for a satisfactory submission is that it is **as good as one might find in a professional audit report.**

Using the output of ChatGPT, GPT-3, or automated tools for audit submissions is highly discouraged as it leads to a high ratio of nonsense submissions. Wardens are responsible for verifying the validity and clarity of their own submissions. Sending multiple poor quality submissions in a single audit will result in all of your audit submissions being ruled invalidated. Additional penalties may also be applied at the discretion of judges and C4 staff.

We are aware this privileges native English speakers as online translation services can result in unclear wordings; therefore, a submission should not be marked as unsatisfactory purely based on grammar and spelling which does not interfere with a judge's ability to understand the submission.

Judges must make the best decision they can regarding quality and understandability of findings.

## Automated findings considered known issues

Wardens may use automated tools as a first pass, and build on these findings to identify High and Medium severity issues ("HM issues"). However, submissions based on automated tools will have a higher burden of proof for demonstrating to sponsors a relevant HM exploit path in order to be considered satisfactory.

## Constant variables

For in-scope contracts, all constant variables should be assumed to be mainnet settings, unless otherwise indicated in the audit repo `README`. Errors regarding constant variables configured using testnet values will, however, be graded as QA (Low Risk) at most, and should be submitted as part of a [QA report](/competitions/submission-guidelines#qa-reports-low-governance).

## Editing a report

To edit a submission to an open audit:

1. Sign into your https://code4rena.com user account.
2. Select the audit from the C4 Audits page.
3. Click on the “Your submissions” tab. There you will see a list of all your submissions for that audit (both individual and team submissions).
4. Select a submission from the list, make your edits and re-submit.

Submissions may only be edited within 2 hours of being submitted.

## Withdrawing a report

It is possible that a warden might want to withdraw a report after submitting it through the website. For example, if a new warden realizes they have not followed the report submission guidelines closely, or discover that a submission was outside the scope of the audit.

In this situation, wardens who wish to have a report withdrawn should:

1. Sign into your https://code4rena.com user account.
2. Select the audit from the C4 Audits page.
3. Click on the “Your submissions” tab. There you will see a list of all your submissions for that audit (both individual and team submissions).
4. Select a submission from the list, and choose the "withdraw" option.

Submissions may only be withdrawn within 2 hours of being submitted.


Last updated 4 days ago

Was this helpful?

---


# Submission limits

Source: https://docs.code4rena.com/competitions/submission-limits

To reduce spam while keeping C4 competitions open to all, submission numbers are capped on a per-competition basis, using a set of reputational criteria:

* `signal` determines the maximum number of submissions wardens/teams can make to each audit.

  + The higher your signal , the more submissions you get.
  + Low- or no-signal users' submissions are capped, to disincentivize spam and encourage newer wardens to focus on their highest-risk, highest-quality findings.
  + Unrestricted submissions unlock at 0.4 (40%) signal + verified identity/tax info.
* Earn bonus (or uncapped) submissions by verifying your identity and submitting your tax information.

For further details on how submission limits work, see [Privileges based on signal](/roles/signal#privileges-based-on-signal).


Last updated 24 days ago

Was this helpful?

---


# Getting started

Source: https://docs.code4rena.com/getting-started

To register, follow these steps:

1. Go to [code4rena.com/register](https://code4rena.com/register/account).
2. Fill in your information. You can choose to connect a web3 wallet for authorization or use only a username and password.

   * Note: You can update any of your details at any time after signup, **except for your username.**
3. **Wardens:** Provide your Ethereum wallet payment address for audit rewards.

## Account setup process

After registration, complete the following steps to set up your account:

1. You'll be directed to [code4rena.com/account](https://code4rena.com/account) for verification.
2. Discord verification is required:

   * Click 'Join Discord Server' to join the Code4rena Discord server.
   * Click 'Verify Discord' to confirm your membership and verify your account.
3. Email verification is also necessary:

   * Check your inbox for an email from [[email protected]](/cdn-cgi/l/email-protection).
   * Click the verification link to confirm your email address.
4. Once both verifications are complete, your account setup will finalize, granting you access to Code4rena's platform!

Code4rena reserves the right to require KYC on any C4 user account, if suspicious activity is detected or for any other reason.

## Warden teams

## Registering a team

To register a team, you must first login to your Warden account, and then register your team [here](https://code4rena.com/register/team/).

Once a team is created, you have the ability to add/remove members and update your payment address while logged in to the Code4rena website.

All team registrations and updates will create pull requests that are flagged for the C4 team to review and approve. Please allow 24-48 business hours for processing.

**Important note: Team awards are sent as a single payment to** ***one*** **wallet.** We strongly recommend using a multisig wallet, or a tool like [PaymentSplitter](https://docs.openzeppelin.com/contracts/4.x/api/finance#PaymentSplitter), to distribute awards among your team members. Note that C4 does not track which team member submitted each finding; your team is responsible for keeping track of that information, and distributing awards. The team structure at C4 is designed so that you submit as a team and get paid as a team.

## Participating as a team

Once individual team members are authenticated, and your team has been registered, each team member will be able to submit findings as either an individual participant, or on behalf of the team. These options are shown on the submission form.

---

## FAQ / troubleshooting

## Can't find email verification message

If you can't find the email verification message:

1. Log in and go to [code4rena.com/account](https://code4rena.com/account).
2. Navigate to the 'Account Verification' section and select 'Re-send Email'.
3. Check your inbox for the new email and click the link to confirm your email address.

## Discord verification

If you can't see most channels in the Code4rena Discord server, your Discord verification may be incomplete. The `warden` role in Discord is automatically assigned when the verification process is successfully completed. To troubleshoot:

1. **Check** [**discord.com**](https://discord.com) **in your web browser.** You may be logged into a different Discord account in your web browser, and if so, that Discord account has received the warden role instead.
2. Log out of Discord.
3. **Perform the Discord verification again:** Log into the account you want associated with your C4 user account, in the same browser you are using to interact with code4rena.com. Next, head to your account settings and perform the Discord verification again.

## Github verification (optional)

Wardens who wish to compete in mitigation reviews should verify their Github accounts in order to access private repos.

**Note:** Ensure that you are logged in to the correct GitHub account before starting the verification process.

To verify your GitHub account on Code4rena, follow these steps:

1. Log in to your Code4rena account.
2. Go to the [**Account Settings**](https://code4rena.com/account) page.
3. Look for the section labeled **User Information**.
4. Check if your GitHub account is verified.
5. If not verified, click the **Verify** link next to "Please verify your GitHub account."
6. You will be redirected to GitHub for authorization.
7. On the GitHub authorization page, click **Authorize**.
8. You will be redirected back to Code4rena, and you should see "✓ GitHub verified" if successful.

Your GitHub account is now successfully linked and verified on Code4rena!

## Wallet connect issues

If you encounter issues with wallet connection:

* Ensure your wallet is unlocked and connected to your device/browser.
* Make sure Ethereum is selected in your wallet settings.

## Forgot password

To reset your password:

1. Go to [code4rena.com/login](https://code4rena.com/login) and select "Forgot Password".
2. Enter your email address and click 'Reset Password'.
3. Check your email for a link to reset your password.

## Change email address

To update your email address:

1. Go to [code4rena.com/account](https://code4rena.com/account) and locate the 'Email Address' field.
2. Click 'Edit' and enter your new email address.
3. Follow the email instructions to confirm your new email address.

## Add/remove web3 wallets for authorization

To manage your web3 wallets for login authorization:

1. Visit [code4rena.com/account](https://code4rena.com/account) and find 'Login Addresses'.
2. To remove a login address:

   * Locate it in the table and select "Remove".
   * Follow the onscreen instructions to confirm removal.
3. To add a new wallet for authorization:

   * Select "Link New Address".
   * Follow the prompts to connect your wallet and sign a message.

## Edit payment address

To edit your payment address:

1. Audits typically pay rewards on the Ethereum network.
2. Your payment address can differ from your login authentication address.
3. Go to [code4rena.com/account](https://code4rena.com/account) and find 'Payment Information'.
4. Click 'Edit' and enter your desired payment address.

**Note:** for each audit, C4 distributes awards to the payment address on file *at the time of award calculation*.

## Tax reporting information

As a US-based entity, Code4rena is legally bound to comply with US tax law and OFAC sanctions. Therefore we collect tax information prior to distributing all award payments.

You can [complete your tax information here](https://code4rena.com/tax-info) or update it anytime from your [account settings](https://code4rena.com/account).

We take being entrusted with your privacy and personal information very seriously. As such:

* This info is submitted via an end-to-end encrypted transmission to isolated data storage separate from other Code4rena APIs.
* There is extremely restricted access to the key. During the course of its day-to-day operations, Code4rena does not have access to names, addresses or tax identification numbers.
* The sole use of provided information is to ensure OFAC compliance and enable accountants (under NDA) to submit annual tax reports.
* The only other cases data could be accessed would be under legal subpoena or due to justified inquiries based on compelling evidence of grave ethical violations.

## Can I change my username?

At this time we do not support username changes.

---

## My problem is not solved!

Feel free to reach out and [submit a Help Desk request](https://code4rena.com/help/).


Last updated 26 days ago

Was this helpful?

---


# Joining competitions and bounties

Source: https://docs.code4rena.com/getting-started/how-to-participate

Code4rena audits let people of a wide range of skill levels get rewarded while showcasing their talent in order to make the DeFi ecosystem more secure.

Stay up to date with new audits by [following C4 on Twitter](https://twitter.com/code4rena) and joining our [community Discord](https://discord.gg/EY5dvm3evD).

## Joining an audit

Anyone can register to participate in an audit. [Register here](https://code4rena.com/register/account), confirm your email address, then join [our Discord](https://discord.gg/code4rena) so you can keep up with important announcements about C4 audits and bounties.

Once you've completed those verification steps, [visit the C4 website](https://code4rena.com/audits/), where you'll find a list of open and upcoming audits, along with their pool size, start and end date.

To view specific audit details and other relevant information, **click the "Join audit" button.** Once the audit launches, the audit page will typically include links to the code repo, and to the finding submission form.

Please familiarize yourself with the [submission policy](/legal/submission-policy), [submission guidelines](/competitions/submission-guidelines), and [judging criteria](/competitions/judging-criteria) prior to participating.

## If you have questions during an audit

General questions about auditing or Code4rena processes should be asked in the `#questions` or `#wardens` channels in the C4 Discord, not directed to the sponsor.

If you have questions about a specific audit:

* Check the audit repo for links to documentation
* Most audits also have Code4rena's Documentation Bot enabled; the bot allows wardens to query the bot for information embedded within the project docs. Instructions for using the bot are shared in the audit channel in the C4 Discord server.
* When a sponsor designates a team member who is available for questions, [you will see a "Q&A" tab on the audit page](/platform-guide/platform-guide-for-wardens/sponsor-q-and-a). If this option is active, you may open a private thread with them to ask questions. However, please be sure to first review all documentation for the audit to ensure the answer hasn't already been provided.

We always encourage sponsors to share any available documentation, and make themselves available for questions, so they get the most out of their audit.

## How can I confirm that Code4rena has received my submission?

Simply head over to the specific audit page and navigate to the `Your Submissions` tab (located to the right of `Details`). If you see your issue listed, then it has been received by the C4 team.

## Can I edit my findings post-submission?

Yes! Go to the `Your Submissions` tab (located to the right of `Details`) on the specific audit page and open the submission, make edits and submit changes. This option is only available for 2 hours after you submit, however; after that, it will be locked in for judging.

## I submitted a finding but then realized it was invalid. Do I need to contact Code4rena?

While the audit is still active, you can go to the `Your Findings` tab (located to the right of `Details`) on the specific audit page and open the finding. There you will see an option to `Withdraw` the finding.

## ⏩ TL;DR

* Turn in your reports before the submission deadline.
* For each audit, submit your Medium and High risk findings individually.
* Bundle all of your low-risk and governance / centralization risk findings into a single QA report.
* Be sure to [register your username and wallet address](https://code4rena.com/register/account) to participate.
* Watch the C4 Discord for award announcements -- if your username is among the winners, [submit your tax information](https://code4rena.com/tax-info) within 30 days to receive your awards.

Publicly disclosing (e.g. publishing or discussing) any discovered bugs or vulnerabilities before the audit report has been published is grounds for disqualification from all C4 events.


Last updated 4 months ago

Was this helpful?

---


# Legal

Source: https://docs.code4rena.com/legal

[Code4rena Terms of Service](/legal/terms-of-service)

Last updated 9 months ago

Was this helpful?

---


# Code4rena Code of Conduct

Source: https://docs.code4rena.com/legal/code4rena-code-of-conduct

Code4rena wardens are held to a high standard in creating a professional and objective environment designed to empower each individual to do their best work. While C4 audits are highly competitive, we are a collaborative community working together to help each other improve our skills and to help sponsor projects become more secure.

It is our individual and collective responsibility to contribute to an environment that helps each contributor feel welcomed and respected. Working as a warden means agreeing to abide by these standards in order to create a productive, professional, and high quality environment for everyone.

This Code of Conduct applies to all Code4rena spaces and in any communication with employees of C4 and its affiliates, contractors, sponsors, or Code4rena community members—both online and off.

At the discretion of C4 staff, any person who violates this code of conduct may be subject to suspension, removal from the community, or forfeiture of awards.

## Professional standards

All wardens are expected to:

* Conduct yourself in an ethical manner when performing security research or disclosing vulnerabilities—both within C4 competitions and outside C4.
* Refrain from using alternate accounts and from any other attempt to circumvent the intent of C4 competition incentives and guidelines.
* Take an objective, collegial, and intellectually open tone in considering and discussing all findings.
* Treat other wardens, sponsors, judges, staff, and their work with respect and an assumption of positive intent.
* **Maintain confidentiality:** Treat the contents of ALL submissions as private and confidential until the audit report is made public.
* Disclose any conflicts of interest or unfair advantages in competitions and either recuse yourself or declare your ineligibility and forfeiture of awards.
* **Contribute constructively:** Accept that the process is human and therefore imperfect, and take an attitude of blameless postmortems. Rather than attempting to retroactively reverse a result you disagree with, find ways the process can be improved for better results in the future.
* Avoid engaging in any discussion and evaluation of submissions you have made, except to answer questions or provide additional context or clarification when requested by a judge or sponsor.
* Respect judges’ decisions as final.

## Community standards

The following behavior is not permitted:

* Discriminatory language
* Aggressive, belittling, or harassing behavior
* Physical threats or abusive, creepy, or inappropriate comments
* Association or identification with hate groups
* Posting people's private information without consent (”doxing”)
* Off-topic spam or shilling, including unsolicited links, referral/affiliate links, and self-promotional DMs
* Job postings or recruitment offers
* NSFW content, including in user avatars, handles, and emoji

## Scope and enforcement

All rules apply to text and voice channels within the Code4rena community and platform. At the discretion of staff, this Code of Conduct may also apply to behavior outside community channels which violates our standards and interferes with sponsors', wardens', judges', or staff’s ability to accomplish their objectives.

Violations of this Code of Conduct will result in consequences at the sole discretion of staff, which may include warnings, suspension, permanent removal from the community, and forfeiture of awards.

## Reporting a violation

If you notice another warden violating this Code of Conduct, you are encouraged wherever possible to first go directly to the relevant person and kindly provide them with feedback. If additional support is necessary, you may report violations of this Code of Conduct to a C4 staff memeber so that C4 can take any necessary steps.

Reports will be handled by C4 staff or their designees. If the person implicated is a member of staff, that person will excuse themselves from handling your incident. Staff will respond as promptly as they reasonable can. Reporting should be private (e.g. private thread in the C4 Discord or [C4 Help Desk](https://code4rena.com/help) request) and not done via public discussion in any online or physical space.

All good-faith reports will be taken seriously. This may include behavior outside Code4rena spaces in instances where behavior crosses an ethical line. Staff reserve the right to reject any report they believe to have been made in bad faith. Reports intended to silence legitimate criticism may be ignored without response.

[PreviousPrivacy Policy](/legal/privacy-policy)

Last updated 4 days ago

Was this helpful?

---


# Privacy Policy

Source: https://docs.code4rena.com/legal/privacy-policy

## Code4rena Privacy Policy and Data Practices for Personally Identifiable Information

## Collection and Use of Personal Information

We collect certain personal information (PII) for specific, limited purposes:

1. To ensure compliance with OFAC (Office of Foreign Assets Control) regulations
2. To enable the submission of annual tax reports by authorized accountants

## Data Protection and Security

We implement strict measures to protect your personal information:

* All data is transmitted using end-to-end encryption
* Information is stored in isolated data storage, separate from our main API
* Access to the encryption key is extremely restricted

## Data Access and Disclosure

Your personal information will only be accessed or disclosed under the following circumstances:

1. By authorized accountants (bound by Non-Disclosure Agreements) for tax reporting purposes
2. In response to a legal subpoena
3. In case of justified inquiries based on compelling evidence of grave ethical violations

We do not use, share, or disclose your personal information for any other purposes.

## Your Rights and Choices

If you have any questions about this privacy policy or wish to exercise your data protection rights, please contact us through the [help desk form](https://code4rena.com/help).


Last updated 9 months ago

Was this helpful?

---


# Code4rena Terms of Service

Source: https://docs.code4rena.com/legal/terms-of-service

Effective Date: January 7, 2025

## 1. What is this?

These Terms of Service (these “Terms”) are a binding agreement between ZC Security Holdings, LLC, dba Code4rena, a Delaware Limited Liability Company (“C4”, “we”, “us”, or “our”), and the individual, group, or entity that creates an Account or otherwise accesses or uses the Services (“you”).

By selecting “I agree,” creating an Account, or accessing or using the Services, you: (a) accept these Terms; (b) acknowledge the Privacy Policy; and (c) agree that the Program Rules (defined below) and, where applicable, the Private Audit Terms (defined below) are incorporated herein by reference.

If you accept these Terms on behalf of an entity, you represent and warrant that you have authority to bind that entity and that “you” refers to such entity. You consent to receive notices and records electronically.

## 2. Incorporated Documents; Program Rules; Private Audit Terms

The following documents are incorporated into and made part of these Terms (collectively, the “Program Rules”): [Code of Conduct](https://docs.code4rena.com/legal/code4rena-code-of-conduct), each as updated from time to time in accordance with this Agreement.

If you participate in any private, closed, or invite‑only audit (each, a “Private Audit”), you must accept any terms we set forth specific to each audit (“Private Audit Terms”).

## 3. Definitions

"Account” means the user account you create to access the Services.

“Audit” means a public security review event coordinated through the Services, governed by the Program Rules.

“Private Audit” means a security review coordinated through the Services with restricted access governed by the Private Audit Terms.

“Sponsor” means the person or entity whose code or system is the subject of an Audit or Private Audit.

“Warden” means a participant who submits findings or related materials in an Audit or Private Audit.

“Judge” means an individual appointed by C4 to evaluate Submissions and allocate Awards pursuant to the judging criteria in relevant Program Materials.

“Scout” means an individual who performs community functions designated by C4.

“Submission” means any report, finding, reproduction steps, proof‑of‑concept, analysis, code snippet, diagram, or other material submitted through the Services.

“Background IP” means your pre‑existing ideas, know‑how, methods, workflows, tools, templates, and materials, whether or not included in a Submission.

“Award” means any compensation, bounty, prize, token allocation, or other consideration provided in connection with an Audit or Private Audit. Award criteria will be provided on a per contest basis and determined in C4 sole discretion .

“Tokens” means blockchain‑based digital assets.

“Program Materials” means per‑audit documentation (including READMEs, out‑of‑scope lists, and timelines), judging criteria, award criteria, and other documents published by C4 or a Sponsor. In the event of a conflict between materials Program Materials published by C4 and those published by a Sponsor, Program Materials published by C4 shall govern.

“Prohibited Person” means any person or entity subject to sanctions or trade restrictions under applicable laws or located in an embargoed jurisdiction.

“Unauthorized Methods” has the meaning set out in Section 8.

“Safe Harbor Scope” means the permitted testing boundaries and conditions for an Audit or Private Audit, as defined in the applicable Program Materials.

“Services” means the Code4rena platform, websites, applications, and related services we provide.

“Work Product” means any reports, summaries, write-ups, findings, narratives, analyses, and other materials created by or on behalf of C4 (including by wardens, judges, scouts, or other contributors engaged through C4) in connection with the Services, which may incorporate or be based on Submissions and other inputs. Work Product does not include Sponsor IP, your Background IP, or C4 IP that exists independently of a particular Audit or Private Audit.

## 4. Interpretation; Order of Precedence

a) Confidentiality. If there is any inconsistency about confidentiality, these Terms will control.

b) Everything else. For all other inconsistencies, the documents that make up this agreement apply in the following order. The earlier item in the list governs over any later item on the same point:

1. These Terms
2. Program Rules (Code of Conduct)
3. Program Materials for the relevant Audit or Private Audit
4. FAQs and other announcements published on the Services

## 5. Eligibility; Account Security; Compliance; Multiple Accounts

a) General. You may use the Services only if you are not a Prohibited Person and are not barred from using the Services under applicable law.

b) Age and Minors.

(i) If you are 18 or older (or the age of majority where you reside), you represent and warrant that you have the legal capacity to enter into these Terms.

(ii) If you are under the age of majority but at least 13 years old (a “Minor”), you may use the Services and participate in Audits and Private Audits only if your parent or legal guardian (A) has reviewed and accepted these Terms on your behalf, (B) agrees to be responsible for your use of the Services, and (C) is the contracting party for purposes of any Awards and related tax/KYC obligations.

(iii) C4 does not knowingly permit children under 13 to create Accounts or use the Services. If you are under 13, you may not use the Services. If we learn that a child under 13 has created an Account, we may delete the Account and associated data subject to applicable law. No awards will be made to persons under 13.

c) Sanctions / Compliance. You represent and warrant that neither you nor, where you are a Minor, your parent or legal guardian, is a Prohibited Person and that you (and, where applicable, your parent or legal guardian) will comply with applicable sanctions, export, anti-corruption, and anti-money-laundering laws.

d) KYC / Tax. We may require identity verification, KYB/KYC, and tax information (for example, IRS Forms W-8/W-9) from you or, where you are a Minor, from your parent or legal guardian before permitting participation or payment of any Award.

e) Account Security. You are responsible for maintaining the confidentiality of your Account credentials and for all activities under your Account. Where you are a Minor, your parent or legal guardian is responsible for supervising your use of the Services and ensuring compliance with these Terms.

f) You will only register and maintain one account. The creation or use of multiple accounts by the same user is prohibited and subject to termination and forfeiture of any Awards.

g) You will ensure that you will treat data accessed and received with reasonable safeguards designed to protect private and confidential information, and that you will promptly inform C4 of any breaches of their administrative, technical or operational security. You shall be liable for disclosure of Confidential Information resulting from a breach of your administrative, technical or operational security.

## 6. Services; Roles; Access; Suspension

We operate the Services, schedule Audits and Private Audits, coordinate judging and Awards, and publish reports. We may modify or discontinue features, or suspend or terminate your access for actual or suspected violation of these Terms or the Program Rules, risk to the Services or users, or legal compliance reasons. Participation does not create employment, agency, joint venture, or partnership.

## 7. Conduct; Acceptable Use; Unauthorized Methods

You must comply with the Code of Conduct and the Program Rules. Without limiting the foregoing, you will not: (a) test outside the Safe Harbor Scope; (b) harm production systems or third‑party users; (c) access data beyond minimal proof of concept; (d) violate law or third‑party rights; (e) attempt to circumvent rate limits or access controls; (f) engage in collusion, doxxing, harassment, or similar misconduct; (g) create multiple accounts.

“Unauthorized Methods” include, without limitation: mainnet exploitation; attacks causing denial of service; spam; social engineering of non‑consenting parties; exfiltration of personal data; and privacy‑impacting tests without explicit written scope. Team participation is permitted if all contributors are registered and bound by these Terms; subcontracting to unbound parties is prohibited.

## 8. Intellectual Property; Licensing; Feedback

(a) Ownership of Submissions and Background IP. As between you and C4, you retain all right, title, and interest in and to your Submissions and your Background IP.

(b) License to C4. You grant C4 a perpetual, worldwide, non-exclusive, royalty-free, transferable, sublicensable license to host, reproduce, display, distribute, adapt, and create derivative works from your Submissions to: (i) operate and provide the Services; (ii) conduct Audits and Private Audits; (iii) produce, use, and publish Work Product; and (iv) market the Services and community.

(c) License to Sponsors. For each Audit or Private Audit you join, you grant the applicable Sponsor a perpetual, worldwide, non-exclusive, royalty-free license to use your Submissions internally to evaluate, triage, and remediate issues and to include limited excerpts in their internal documentation. This license does not include any right to commercialize your general methodologies or Background IP, or to use your Submissions to train or improve machine-learning or similar models, unless agreed in a separate written instrument.

(d) Work Product and C4 IP. C4 may combine, edit, and incorporate Submissions into Work Product. As between you and C4, C4 owns all right, title, and interest in and to the Work Product and C4 IP, subject to your continued ownership of your Submissions and Background IP and the licenses you grant in this Section.

(e) C4 Content and Limited License to You. The Services, C4 IP, Work Product, audit reports, leaderboards, documentation, site text, designs, compilations, selection and arrangement of Submissions, and all other content C4 makes available (together, “C4 Content”) are owned by C4 or its licensors. Subject to these Terms, C4 grants you a limited, revocable, non-exclusive, non-transferable, non-sublicensable license to access and use C4 Content solely for your own personal or internal business purposes in connection with participating in Audits and Private Audits or evaluating the Services.

(f) Reservation of Rights. Except for the licenses expressly granted in this Section, no rights are granted to you in or to the Services, C4 Content, any Submissions, or C4 IP, whether by implication, estoppel, or otherwise. C4 and its licensors reserve all rights not expressly granted.

(g) No Scraping or Bulk Access. You may not, and may not permit any third party, bot, or agent, to, access, search, scrape, crawl, or index the Services or any C4 Content by automated means (including bots, AI agents, or similar tools), or copy, harvest, or bulk-download C4 Content, except as expressly permitted in a separate written agreement with C4 or via public APIs that C4 designates for that purpose.

(h) No AI or Machine-Learning Training. Except as expressly authorized in a separate written agreement with C4 (or via C4 affiliate entities), you may not, and may not permit any third party to: (i) use the Services, any C4 Content, or any Submissions to train, fine-tune, evaluate, or otherwise improve any machine-learning, large-language, foundation, or similar artificial-intelligence model; (ii) create embeddings, feature vectors, or other derived datasets from the Services, any C4 Content, or any Submissions for use with such models; or (iii) use any automated means to collect or analyze the Services, C4 Content, or Submissions for text-and-data-mining purposes related to model training or evaluation.

(i) Aggregated / De-identified Use by C4. C4 may use Work Product, Submissions, and data derived from the provision and use of the Services in aggregated or de-identified form to operate, maintain, and improve the Services and related contests, to develop analytics and machine-learning or artificial-intelligence models, and to publish industry research and benchmarking, provided that such use does not identify you by name without your consent, except where you have already publicly associated yourself with the relevant Audit or Private Audit.

(j) Code Snippets. You grant a non-exclusive copyright license to any code snippets included in your Submissions that are strictly necessary to demonstrate or reproduce a finding.

(k) Moral Rights. To the extent permitted by law, you waive moral rights in your Submissions against C4 and Sponsors to enable the licenses and uses described in this Section.

(l) Attribution. C4 may attribute Submissions and Work Product to your platform handle (and, where you have chosen to use it, your name) unless you opt out where permitted by the Program Rules.

(m) Feedback. If you provide ideas, suggestions, or other feedback, you grant C4 a perpetual, worldwide, irrevocable, royalty-free license to use such feedback for any purpose without attribution or compensation.

(n) Enforcement. Any access or use of the Services, C4 Content, or Submissions in violation of the restrictions in this Section 8 is unauthorized, automatically terminates the license granted to you under this Section, and may subject you to civil and/or criminal liability under applicable law.

## 9. Safe Harbor (Authorization to Test)

Authorization. Within the Safe Harbor Scope defined in the applicable Program Materials your testing is authorized by the relevant Sponsor.

Conditions. You will not target production users, exfiltrate personal data, or disrupt availability. You will cease testing upon request. You will promptly report vulnerabilities through the Services.

Third‑Party Inquiries. If a third party contacts you regarding your audit activity, notify C4 and we will confirm your authorization to that party to the extent appropriate.

Out‑of‑Scope. Activity outside the Safe Harbor Scope is not authorized and may result in disqualification, suspension, referral to applicable authorities, and other remedies.

## 10. Confidentiality; Prohibition on Public Disclosure before Publication

1. Public Audits. Submissions and Program Materials may be published consistent with the Program Rules.
2. Private Audits. Information designated confidential is governed by these Terms as well as any applicable documents you may be required to sign. Exceptions include information already public without breach, independently developed without use of confidential information, or rightfully received from a third party without confidentiality obligations.
3. Public Disclosure Before Publication. You must not publicly disclose, publish, or discuss any bugs, vulnerabilities, or related technical details discovered in connection with an Audit or Private Audit (including in Submissions or Work Product) until C4 has published the corresponding audit report or otherwise notified participants that disclosure is permitted. “Publicly disclose” includes, without limitation, posting on social media, blogs, forums, conferences, meetups, code repositories, messaging channels not expressly designated by C4 for that Audit, or sharing with third parties outside your registered team.

A violation of this Section is a material breach of these Terms and may result in immediate disqualification from the applicable Audit, forfeiture of any unpaid Awards related to that Audit, suspension or termination of your Account, and loss of eligibility to participate in future C4 events.

## 11. Awards; Payments; Taxes; Unclaimed Property; Token Risks

1. Awards. Awards are discretionary and based on the Program Materials A Judge’s determinations are final absent fraud or manifest error.
2. KYC & Tax. Payment requires timely completion of KYC/KYB and tax forms. We may engage service providers to perform identity and sanctions screening.
3. Timing and Method. Payments may be in fiat or crypto. Network selection and timing may vary due to network conditions. Unless prohibited by law, you bear network fees and are responsible for accurate payout details.
4. Deadlines and Unclaimed Property. If you fail to complete required steps within 30 days of notice, we may delay payment. If you do not complete any required KYC/KYB and tax requests within 30 days of the award announcement, you will forfeit your award.
5. Withholding. We may withhold amounts required by law or by Sponsor instructions that comply with law.
6. Token Risks. Tokens are volatile and may be subject to technological, regulatory, or market risks. You assume all risks of Token receipt, custody, taxation, and disposition.

## 12. Privacy; Data Protection

The Privacy Policy describes our processing of personal data, including categories of data, purposes, lawful bases, processors, cross‑border transfers, retention, security measures, and user rights. We will not make security representations beyond those in the Privacy Policy.

## 13. Third‑Party Services; Open Source

You may connect third‑party services (e.g., wallets, identity providers, analytics). Those services are governed by their own terms and privacy policies, which we do not control. You are responsible for compliance with open‑source licenses in audited repositories.

## 14. Copyright Policy (DMCA)

If you believe content on the Services infringes your copyright, you may submit a notice pursuant to 17 U.S.C. §512 to C4’s designated agent at: [[email protected]](/cdn-cgi/l/email-protection) Your notice must include the information required by §512(c)(3). We may remove or disable access to allegedly infringing material and terminate repeat infringers in appropriate circumstances.

## 15. Representations and Warranties; Disclaimer

Your Representations. You represent and warrant that: (a) you have all rights necessary to grant the licenses in Section 8; (b) your Submissions do not infringe, misappropriate, or violate any third‑party rights or law; (c) you will comply with these Terms, the Program Rules, and applicable law; and (d) you are not a Prohibited Person.

Service Disclaimer. THE SERVICES, AUDITS, PRIVATE AUDITS, REPORTS, AND ALL RELATED MATERIALS ARE PROVIDED “AS IS” AND “AS AVAILABLE.” TO THE MAXIMUM EXTENT PERMITTED BY LAW, C4 DISCLAIMS ALL WARRANTIES, EXPRESS OR IMPLIED, INCLUDING WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE, TITLE, NON‑INFRINGEMENT, AND QUIET ENJOYMENT. AUDITS ARE NOT CERTIFICATIONS OR GUARANTEES OF SECURITY.

## 16. Indemnification

By You. You will indemnify, defend, and hold harmless C4, its Affiliates, and their respective officers, directors, employees, and agents from and against claims, losses, liabilities, damages, costs, and expenses (including reasonable attorneys’ fees) arising out of or relating to: (a) your Submissions; (b) your out‑of‑scope or unlawful testing; (c) your breach of these Terms or the Program Rules; or (d) your violation of law or third‑party rights.

By Sponsors. Sponsors will indemnify, defend, and hold harmless C4 from and against claims arising from Sponsor materials and the audited codebase.

Mutual. Each party will indemnify the other for breaches of confidentiality obligations under these Terms or an applicable NDA.

## 17. Limitation of Liability

TO THE MAXIMUM EXTENT PERMITTED BY LAW, NEITHER PARTY IS LIABLE FOR INDIRECT, INCIDENTAL, SPECIAL, CONSEQUENTIAL, EXEMPLARY, OR PUNITIVE DAMAGES, OR FOR LOST PROFITS, REVENUE, GOODWILL, OR DATA, EVEN IF ADVISED OF THE POSSIBILITY. C4’S TOTAL LIABILITY FOR ALL CLAIMS RELATING TO THE SERVICES WILL NOT EXCEED THE GREATER OF: (i) USD $1,000; OR (ii) THE AMOUNTS PAID BY C4 TO YOU IN THE 12‑MONTH PERIOD PRECEDING THE EVENT GIVING RISE TO LIABILITY. THE FOREGOING LIMITATIONS DO NOT APPLY TO WILLFUL MISCONDUCT, FRAUD, OR BODILY INJURY. SOME JURISDICTIONS DO NOT ALLOW CERTAIN LIMITATIONS, IN WHICH CASE THEY APPLY TO THE MAXIMUM EXTENT PERMITTED.

## 18. Dispute Resolution; Arbitration; Governing Law

1. Governing Law. These Terms are governed by the laws of the State of New York, excluding its conflict‑of‑laws rules.
2. Mandatory Arbitration. Any dispute, claim, or controversy arising out of or relating to these Terms or the Services (collectively, “Disputes”) will be resolved by binding arbitration administered by JAMS under its Comprehensive Arbitration Rules and Procedures then in effect (the “Rules”). The seat and venue of arbitration will be New York, New York. The language will be English. One arbitrator will preside.
3. Delegation. The arbitrator has exclusive authority to resolve any Dispute relating to the interpretation, applicability, enforceability, or formation of this arbitration agreement, including any claim that all or any part of it is void or voidable.
4. Class and Jury Waiver. You and C4 agree that each may bring claims only in your or its individual capacity, not as a plaintiff or class member in any purported class, collective, consolidated, mass, or representative proceeding. To the extent a Dispute is litigated in court rather than arbitrated, the parties waive any right to a jury trial.
5. Injunctive Relief; Small Claims. Either party may seek temporary or preliminary injunctive relief in a court of competent jurisdiction to protect its intellectual property or confidential information. Either party may bring an individual action in small claims court.
6. Confidentiality. The parties will keep the existence of the arbitration, the proceedings, and the award confidential, except as required to enforce or vacate an award or as required by law.
7. Opt‑Out. You may opt out of this arbitration agreement within 30 days of first accepting these Terms by sending written notice to [[email protected]](/cdn-cgi/l/email-protection). Your opt‑out will not affect other provisions of these Terms.

## 19. Export; Sanctions; Anti‑Corruption

You will comply with all applicable export control, sanctions, and anti‑corruption laws, including those administered by OFAC, BIS, and other authorities. You represent that neither you nor your beneficial owners, directors, or officers are Prohibited Persons. You will not access or use the Services from, or for the benefit of, any Prohibited Person or embargoed jurisdiction.

## 20. Changes

We may update these Terms and the Program Rules by posting an updated version and effective date on the Services and maintaining a public change log. Material changes require re‑acceptance before you join or continue in Audits or Private Audits occurring after the effective date of such changes.

## 21. Term; Termination; Suspension; Survival

These Terms commence on your acceptance and continue until terminated. You may terminate by closing your Account. We may suspend or terminate for cause, including for violations of these Terms or legal requirements, or upon platform shutdown. Any provisions that by their nature should survive, will survive termination.

## 22. Notices

C4 may provide notices to the email address associated with your Account or through the Services. Legal notices to C4 must be sent to: [[email protected]](/cdn-cgi/l/email-protection#94f8f1f3f5f8d4f7fbf0f1a0e6f1faf5baf7fbf9). Notices are deemed given when received (email) or three business days after mailing by certified mail (physical).

## 23. Assignment; Change of Control

You may not assign or transfer these Terms without our prior written consent, and any attempted assignment in violation of the foregoing is void. We may assign these Terms to an Affiliate or in connection with a merger, acquisition, reorganization, or sale of assets. These Terms are binding upon and inure to the benefit of the parties and their permitted successors and assigns.

## 24. Force Majeure

Neither party is liable for delay or failure to perform due to events beyond its reasonable control, including acts of God, labor disputes, zombie attacks, AGI, attacks by space aliens, governmental actions, war, terrorism, civil disturbances, failures of telecommunications or networks, or utility failures, provided that the affected party uses commercially reasonable efforts to resume performance.

## 25. Severability; Waiver; Equitable Relief

If any provision of these Terms is held invalid, illegal, or unenforceable, the remaining provisions will remain in full force and effect, and the invalid provision will be deemed modified to the minimum extent necessary to make it valid and enforceable. No waiver is effective unless in writing and signed by the waiving party. You acknowledge that unauthorized use of the Services or Submissions may cause irreparable harm for which monetary damages are inadequate, and C4 may seek equitable relief without posting a bond.

## 26. Third‑Party Beneficiaries; Entire Agreement

Sponsors are intended third‑party beneficiaries of Section 9 (Safe Harbor) and the Sponsor license in Section 8, solely to that extent. There are no other third‑party beneficiaries. These Terms (including the documents incorporated by reference) constitute the entire agreement between the parties regarding the Services and supersede all prior or contemporaneous understandings on that subject.


Last updated 4 days ago

Was this helpful?

---


# Platform Guide

Source: https://docs.code4rena.com/platform-guide

## [For sponsors](/platform-guide/platform-guide-for-sponsors)

Code4rena's sponsor platform provides our clients' teams with a secure, efficient interface to prepare for audits, review submissions, and track progress through fixes and report publication.

## [For wardens](/platform-guide/platform-guide-for-wardens)

The Code4rena warden platform is designed to help wardens focus on what matters most: reporting vulnerabilities, earning rewards, and securing projects.


Last updated 26 days ago

Was this helpful?

---


# Platform guide for sponsors

Source: https://docs.code4rena.com/platform-guide/platform-guide-for-sponsors

Code4rena's sponsor platform provides your team with a secure, efficient interface to prepare for your audit, review submissions, and track your progress through fixes and report publication.

This guide will help you navigate the platform, view and respond to submissions, and understand how to use the tooling for maximum efficiency.

* [Account setup](/platform-guide/platform-guide-for-sponsors#account-setup)
* [Your audit dashboard](/platform-guide/platform-guide-for-sponsors/your-audit-dashboard)
* [Viewing submissions](/platform-guide/platform-guide-for-sponsors/viewing-submissions)
* [Comments](/platform-guide/platform-guide-for-sponsors/comments)

---

## Account setup

Access to the client dashboard requires a Code4rena user account.

* If you already have Code4rena accounts, share your usernames with your Engagement Manager.
* If not:

  + [Create C4 accounts here](https://code4rena.com/register/account)
  + Verify email for your account

    - Check your inbox for an email from [[email protected]](/cdn-cgi/l/email-protection).
    - Click the verification link to confirm your email address.
  + Provide your Engagement Manager with a list of the team's usernames

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FptpMqMvrkydBvWcbmc9x%252Fimage.png%3Falt%3Dmedia%26token%3Debcd3d8a-4972-443b-8867-db96644bbf80&width=768&dpr=4&quality=100&sign=1ac5ea94&sv=2)

Visit [https://code4rena.com/register](https://code4rena.com/register/account) to register an account.

Once we link your accounts to the audit, you'll be able to track your audit’s progress by logging in at [code4rena.com](https://code4rena.com).

You can safely ignore the "Connect wallet" option - that's for wardens, so they can get paid for finding bugs.


Last updated 26 days ago

Was this helpful?

---


# Comments

Source: https://docs.code4rena.com/platform-guide/platform-guide-for-sponsors/comments

Sponsors are welcome to leave comments on any submission. Comments are primarily reviewed by the judge, who will take them into account during their deliberations on validity and severity.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FuzDMaOFW6HRD7KnCvQdz%252Fimage.png%3Falt%3Dmedia%26token%3D08c8d2e7-0e72-450a-b808-06fc42a3b09a&width=768&dpr=4&quality=100&sign=8b4cdd3e&sv=2)

The comment section sits at the bottom of the individual submission screen.

Comments can be threaded for clarity.

## Viewing recent comments

The quickest way to access all comments on your audit submissions is from your audit dashboard: there is a handy "Comments" link in the right sidebar that points to a comment list, sorted by recency.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FpcradWRmOYAUhTCqoXli%252Fimage.png%3Falt%3Dmedia%26token%3D10826f4a-576b-458b-9fb5-78b6e5d21da3&width=768&dpr=4&quality=100&sign=fe7681bc&sv=2)

Clicking the "Comments" link will take you to a date-sorted list of every comment on your audit submissions.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FoF0LnuwiKHamNQ5Z7JBS%252Fimage.png%3Falt%3Dmedia%26token%3D8aa64119-41fe-42db-a831-02cd9000c001&width=768&dpr=4&quality=100&sign=a6c08610&sv=2)

The comment list displays the number of replies to each comment, along with the username of the commenter and the date of the most recent comment.

## Who can comment?

Don't be surprised if you see some new and unfamiliar usernames during the post-judging QA phase of your audit; those are [SR wardens](/roles/sr-wardens), a subset of KYC'd, high-signal wardens who have earned the privilege of participating in a 48-hour QA discussion period with the judge.

Comment privileges vary depending on the phase of the audit:

* From launch through completion:

  + Authenticated members of your team
  + Judge(s) assigned to your audit
* Post-judging QA phase:

  + Certified (KYC'd) wardens may comment during this 48-hour period only.


Last updated 26 days ago

Was this helpful?

---


# Viewing submissions

Source: https://docs.code4rena.com/platform-guide/platform-guide-for-sponsors/viewing-submissions

To view all submissions to your audit, click `View all` from your audit dashboard; this will take you to the full submission list.

During the submission phase, and until the judge finishes triaging all submissions, the default view has three sections:

1. Triaged submissions (judge confirmed) at the top
2. Ungrouped submissions (new and under review) next
3. QA reports (compilations of Low-risk findings) last

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FE5bXJzfvA539EdLBPmwW%252Fimage.png%3Falt%3Dmedia%26token%3D886dafed-2fb6-4c85-a964-0cb1868a0e1b&width=768&dpr=4&quality=100&sign=7f2ddd50&sv=2)

Triaged submissions show first, then new submissions (not yet triaged).

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FWbQ5rbFUWjsZmpguOne7%252Fimage.png%3Falt%3Dmedia%26token%3Daad7da23-f304-46c6-a7d9-147c88882720&width=768&dpr=4&quality=100&sign=a49bb62b&sv=2)

QA reports are listed at the bottom of the submission list; submissions default to displaying in order of severity.

Once triage has been completed, you will no longer see ungrouped ("New & under review") submissions, just submissions that have been confirmed by the judge:

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FTqucYNSObfNHJEvK5BV4%252Fimage.png%3Falt%3Dmedia%26token%3Dbcc9e720-d98b-4a42-939b-e710accb736a&width=768&dpr=4&quality=100&sign=8a0438a7&sv=2)

This view shows triage is complete; there's no longer a section for "New and under review" submissions.

## Submission filters

Audit submissions may be filtered by:

* Risk
* Status
* Group/ungroup duplicates
* (For mitigation reviews only) Mitigated/Unmitigated status

## Filtering by severity

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FCE54h4gB8dZnVKNlsrfG%252Fimage.png%3Falt%3Dmedia%26token%3Dd2ef9656-23d3-406f-b005-93bc0688b9a1&width=768&dpr=4&quality=100&sign=cc223d7c&sv=2)

The `Severity` filter defaults to displaying all severities, but you can adjust it to focus on a single severity level.

## Filtering by status

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FXn1tuNzXGnWkNl4gjaBp%252Fimage.png%3Falt%3Dmedia%26token%3D002cee7d-45c0-406e-8108-c47156a483be&width=768&dpr=4&quality=100&sign=935ad443&sv=2)

The default setting for `Status` is to include everything except rejected submissions. The filter can be adjusted as needed.

## Group/ungroup duplicates

Since Code4rena audits typically include many duplicate submissions, the default view groups duplicates together to save you time. However, there can be valuable detail and nuance within individual submissions in a duplicate set.

If you prefer to review each submission separately, simply uncheck the `Group by duplicates` option.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FR5vi2XCbf2zwPkS85NZk%252Fimage.png%3Falt%3Dmedia%26token%3D0d9a9cf6-27bb-42c5-a48c-7c0646f2af74&width=768&dpr=4&quality=100&sign=b6db1182&sv=2)

Unchecking `Group by duplicates` displays every submission individually.

## Sorting

By default, the submission list is sorted by severity, from High to Low. The sorting menu allows you to sort by severity, last updated, or duplicate count (number of duplicates) — and to select ascending or descending order.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252Fh1eXB1packXsWmcD2tCs%252Fimage.png%3Falt%3Dmedia%26token%3D273f7b37-f9d0-4ea4-8a00-0188ec3e02ba&width=768&dpr=4&quality=100&sign=b0a9d0d6&sv=2)

The "Last updated" sorting function is especially helpful during the judging phase, if you wish to follow the judge's deliberations closely. It will highlight any submissions that have recent updates to the judge's evaluation, or new comments.

## Search

The search function offers a convenient way to locate submissions related to a particular file or function. The search field supports searching for:

* keywords in submission title or body
* warden/team name
* submission number (e.g. `S-42`)

For best results, ensure that you deselect any filters, and uncheck `Group by duplicates` — otherwise, some results may be hidden.

![Screencap of a submission list displaying search results for `S-42`](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FqyyYkqH1aXOu0dg86Nlu%252Fimage.png%3Falt%3Dmedia%26token%3Daf5b096c-5196-4b46-8329-4df9f02a4f2f&width=768&dpr=4&quality=100&sign=387c45fe&sv=2)

Searching by submission number works best with all filters removed.

## Submission status

The `Status` function allows your team to keep track of findings confirmed by the judge.

This is optional, but you may wish to update a submission's `Status` in order to:

* Keep track of your team's stance and mitigation progress
* Provide input to the judge

The options for `Status` are:

* `Judge confirmed` : the default status for triaged submissions that the judge believes are valid and merit review.
* `Fixed` : sponsor has fixed the issue
* `Mark as informative`: issue does not currently need a fix
* `Disputed`: sponsor disputes the validity of this issue

## Bookmark submissions

The bookmark icon can be selected to help you track submissions you want to come back to.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FaUnNEHjZ7Z7iMce8fN7R%252Fimage.png%3Falt%3Dmedia%26token%3Deb81cfd8-75ff-4709-91c6-a9dbc5622acc&width=768&dpr=4&quality=100&sign=3a1ee828&sv=2)

The yellow bookmark icon appears on the far right of each submission row.

To view your bookmarked submissions, click the filter icon and select `Bookmarked only`:

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FD3IBOoL5feetXu4tLJMN%252Fimage.png%3Falt%3Dmedia%26token%3Dba5cfd1d-d8f3-4d50-83f5-597fb0082985&width=768&dpr=4&quality=100&sign=de047745&sv=2)

The filter icon is in the top left section of the screen, directly below the "Submissions" heading.

This applies another filter to your existing filters, so you may need to adjust your other filters if you wish to display \*all\* bookmarked submissions.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FYfKYvE4kX1czFoKAYpmD%252Fimage.png%3Falt%3Dmedia%26token%3Db746cc22-4757-4a41-a8f0-88d73e5efb1b&width=768&dpr=4&quality=100&sign=dfcd5c8&sv=2)

To reset the `Bookmarked only` filter, just click the x next to it.

## Which submissions will appear in our report?

Once the judge finalizes all decisions, your Engagement Manager will share a link to view all submissions that will appear in your audit report.

All judge confirmed High- and Medium-risk findings will appear in your audit report, along with the top-ranked QA report. While your input to the judge (via the `Status` labels and comments) is taken into account, ultimately the judge has the final say in determining validity and severity.

To preview the findings that will appear in your report, set the filters to:

* Severity: `High` and `Medium`
* Status: `Judge confirmed`, `Fixed`, `Informative`, `Disputed`
* `Group by duplicates` = checked

## Individual submissions

Clicking a submission title will open the full submission in a side panel.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FYgt8g0R2ROfMHRoD7n7G%252Fimage.png%3Falt%3Dmedia%26token%3D920a394c-c443-4c38-9c68-f13c99617890&width=768&dpr=4&quality=100&sign=4bfe1e87&sv=2)

Side panel view of an individual submission

Individual submissions may also be displayed in a full-screen view:

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FNUQHULLSkg50XXUoVJEf%252Fimage.png%3Falt%3Dmedia%26token%3D7c1209ad-a233-4e79-acc4-b9617b818541&width=768&dpr=4&quality=100&sign=4500b2f7&sv=2)

Click the "full screen" / "compact view" icon in the upper right (next to the x) to switch from side panel to full screen, and back again.

The submission number in the upper left corner can be clicked to show all duplicates of the selected submission.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252Fv92Tjjd8rkXF0DsxHToF%252Fimage.png%3Falt%3Dmedia%26token%3Db11d8355-4463-49b1-9c49-5ba7fca2d2af&width=768&dpr=4&quality=100&sign=c4173e28&sv=2)

The duplicate navigation menu distinguishes between the Primary submission (selected as best by the judge) and the other duplicates in the set.

## Export submissions as Markdown

Any submission to your audit can be exported in Markdown format. The export feature will save the submission to your device as an .md file.

## To export a submission from the submission table

1. Right-click on a submission to open the context menu
2. Select `Export markdown`

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252Fow0uA4vo18GfwFp23Owh%252Fimage.png%3Falt%3Dmedia%26token%3Dcb95ad6a-e3a9-4061-9850-86dcfd36e97f&width=768&dpr=4&quality=100&sign=8c42598f&sv=2)

## From the side panel (or fullscreen) view

1. Click the `…` menu
2. Select `Export markdown`

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252F9HIc7qexCJ3Uvm8jbMiG%252Fimage.png%3Falt%3Dmedia%26token%3D4ce89106-17c4-44d2-aad1-3baa10883b02&width=768&dpr=4&quality=100&sign=984b32f3&sv=2)

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FN0hDMXBWWJgRRiIDVyXA%252Fimage.png%3Falt%3Dmedia%26token%3Ddd4fb3aa-f077-4ddd-af3e-4fc74914fcc5&width=768&dpr=4&quality=100&sign=94a51b36&sv=2)


Last updated 1 month ago

Was this helpful?

---


# Warden Q&A

Source: https://docs.code4rena.com/platform-guide/platform-guide-for-sponsors/warden-q-and-a

During the submission phase of your Code4rena audit, C4 wardens (auditors) may reach out to ask technical questions about your code, implementation details, intended behavior, and potential vulnerabilities.

Here are some tips for managing these conversations:

* [Monitor discussions from your client dashboard](/platform-guide/platform-guide-for-sponsors/warden-q-and-a#warden-questions-will-appear-in-your-client-dashboard)
* [Sort your Q&A inbox](/platform-guide/platform-guide-for-sponsors/warden-q-and-a#sorting-your-q-and-a-inbox)
* [Use thread settings to manage your inbox](/platform-guide/platform-guide-for-sponsors/warden-q-and-a#thread-settings)

## Locating the Q&A inbox

To access your Q&A inbox:

* Navigate to your client dashboard
* Select the **Warden Q&A** link.

If you have new questions from wardens, you will see a red notification alert.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252F3gLTchG9uDynMqVs7RxY%252Fimage.png%3Falt%3Dmedia%26token%3D05dd472c-26bd-45d1-bc00-548e6015a9ea&width=768&dpr=4&quality=100&sign=4445ed1b&sv=2)

A link to your Q&A inbox appears in the sidebar of your client dashboard

You can also see the Q&A inbox from the evaluation dashboard, below the list of submissions and submission comments.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FBRyQmlBWOSrYBKUdciIe%252Fimage.png%3Falt%3Dmedia%26token%3D1ee80e8a-4de4-4888-8996-8002f4809f6f&width=768&dpr=4&quality=100&sign=4ea90489&sv=2)

## The Q&A inbox

Clicking through to the inbox will open a list of Q&A threads:

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FGKUmabWJ1W3FSCAKPVOI%252Fimage.png%3Falt%3Dmedia%26token%3Dfd13e76d-bbf1-4f87-99c6-21bf41fecddc&width=768&dpr=4&quality=100&sign=2239e9ca&sv=2)

* The inbox has two tabs: `Open` and `Closed,` based on the status of your Q&A threads.
* Wardens who don't have the [SR role](/roles/sr-wardens) are limited to one active thread, and cannot add to that thread until you reply, or mark it as resolved/closed.
* You can sort your inbox based on:

  + `Created on` - date the thread was opened/created
  + `Last updated` - time of last update
  + `Total replies` - number of comments in the thread
  + `Signal score` - accuracy score of the warden who opened the thread (see [Signal](/roles/signal))

## Sorting your Q&A inbox

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252F5r70vBuTazyqmaXF77hb%252Fimage.png%3Falt%3Dmedia%26token%3D51c674a3-b23f-4722-94ff-03bb503153e9&width=768&dpr=4&quality=100&sign=decfa86c&sv=2)

## Reply to questions

To reply to a question, select the thread and type in the `Reply` field:

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FY6ushRAJPJNeHETGepaU%252Fimage.png%3Falt%3Dmedia%26token%3D40b58e15-c077-4f78-9513-1c698dbc50f0&width=768&dpr=4&quality=100&sign=31f8cabe&sv=2)

## Thread settings

You can also choose to:

* **Leave the thread open** - which allows the warden to reply so you can continue the discussion; or
* **Close the thread** (with or without replying to it) - this marks the thread as complete/resolved.
* **Lock thread:** prevents further comments on the thread
* **Set to public:**

  + If you receive the same question multiple times, you may wish to make a thread public. This allows all participating wardens to view the discussion, which should save your team from having to answer the question repeatedly.
  + Making a thread public will make it visible to ALL participants.

The nature of competitive audits is that wardens guard their work closely. Before making a thread public, be careful it doesn't contain any details that could leak hints to other competitors.

* **Mute/Unmute author:** hide all threads or comments from this warden.

  + You can manage muted wardens anytime from your Q&A inbox.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FT0Zz9EVlE28A0dyJhdni%252Fimage.png%3Falt%3Dmedia%26token%3D9c2c6f0d-ebdb-49c8-8374-e1ae5ea18eff&width=768&dpr=4&quality=100&sign=b3494870&sv=2)

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FglU9mxt26I2Kv3IOAu9A%252Fimage.png%3Falt%3Dmedia%26token%3D14199240-3fdb-4485-b7be-295a11f32aa3&width=768&dpr=4&quality=100&sign=b2430d11&sv=2)


Last updated 26 days ago

Was this helpful?

---


# Your audit dashboard

Source: https://docs.code4rena.com/platform-guide/platform-guide-for-sponsors/your-audit-dashboard

The audit dashboard is where you’ll view your audit findings and check on progress.

As each audit phase unfolds, from onboarding to report delivery, your dashboard will guide you through each step.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FLj5iqVbxMNTkRUX8jOAw%252Fimage.png%3Falt%3Dmedia%26token%3D3442d18e-b5f5-4811-8c04-dc29d6611f7e&width=768&dpr=4&quality=100&sign=d1f43958&sv=2)

This view of the audit dashboard shows the onboarding phase.

## Triaged submissions

Once the audit is live, your dashboard will display a summary of triaged submissions.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252F3MSlFVtW5kJXTiLkkrqM%252Fimage.png%3Falt%3Dmedia%26token%3D6c82e03a-7af0-47e8-b647-492526a412c9&width=768&dpr=4&quality=100&sign=9b756b3d&sv=2)

During the submission phase, the judge will triage submissions they believe to be valid to your dashboard for review.


Last updated 6 months ago

Was this helpful?

---


# Platform guide for wardens

Source: https://docs.code4rena.com/platform-guide/platform-guide-for-wardens

The Code4rena platform interface is designed to help wardens focus on what matters most: finding vulnerabilities and earning rewards. This guide will help you navigate the platform, participate in competitions, and track the status of your submissions.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FHXIm0ysa35f03UM3uAc6%252Fimage.png%3Falt%3Dmedia%26token%3D1c49992f-82ac-4608-9005-b91e31d0e640&width=768&dpr=4&quality=100&sign=463ae348&sv=2)

The welcome screen (News) for wardens logged in to Code4rena.com

Here's what you'll find in the main navigation:

* **News** - Latest updates, announcements, and important information from the Code4rena team
* [**Leaderboard**](/platform-guide/platform-guide-for-wardens/leaderboard-and-profiles) - Track your performance and see how you rank against other wardens
* [**Submissions**](/platform-guide/platform-guide-for-wardens/participate-in-audits#viewing-your-submissions) - Manage your audit submissions and track their status
* [**Audits**](/platform-guide/platform-guide-for-wardens/participate-in-audits) - Browse active, upcoming, and past audit competitions
* **Bounties** - Ongoing bug bounty programs you can participate in
* **Reports** - Access published audit reports and findings
* **Platform Tools:**

  + **Docs** - Access to Code4rena's comprehensive documentation
  + **Support** - Get help when you need it
  + **Settings** - Manage your account preferences and notifications

## Account Management

Your account details appear in the lower left corner of the screen, showing:

* **Your warden name** - Your C4 username (e.g. `CaptainBigMoney`)
* **Account dropdown** - Access to additional account settings and options
* **Account settings** - Manage your profile and platform preferences

For more info on account setup and management, see the [Account management page](/other-details/account-management).


Last updated 26 days ago

Was this helpful?

---


# Comments on submissions

Source: https://docs.code4rena.com/platform-guide/platform-guide-for-wardens/comments-on-submissions

This page is about comments on audit submissions. Documentation about sponsor Q&A can be viewed here.

To view comments for an audit, start from the audit dashboard, and click the `View all comments` button.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FsOE28NhpB9Ny0wPWn0jQ%252Fimage.png%3Falt%3Dmedia%26token%3D2712fd7c-8612-4fc0-bcec-23ed742174d2&width=768&dpr=4&quality=100&sign=4f17716d&sv=2)

The comment list is grouped by the date of the most recent comment in the thread, and shows the number of comments.

Clicking on a thread will take you to the comment section on the relevant submission:

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252Fg58iogBLTaTRfpKReQkC%252Fimage.png%3Falt%3Dmedia%26token%3D1254085f-302c-49ad-b943-29f4150e06e2&width=768&dpr=4&quality=100&sign=f77f949c&sv=2)

SR wardens will see a comment form that can be used to add comments during the Post-judging QA phase of the audit. SR wardens may also leave a private comment for the judge and sponsor.

## Who can comment?

Comment privileges vary depending on the phase of the audit:

* From launch through completion:

  + Sponsor team members
  + Judge(s) assigned to the audit
* [Post-judging QA](/competitions/judging-criteria#post-judging-qa-pjqa) phase:

  + Wardens may comment during this 48-hour period only.


Last updated 26 days ago

Was this helpful?

---


# Leaderboard and profiles

Source: https://docs.code4rena.com/platform-guide/platform-guide-for-wardens/leaderboard-and-profiles

Code4rena's leaderboard displays up-to-date rankings for every warden and team who has earned awards from a competition.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252F8BhSEhAKlWXENiAlua2W%252Fimage.png%3Falt%3Dmedia%26token%3Dee9257f3-6afd-4b57-b87f-4bed36ebdc20&width=768&dpr=4&quality=100&sign=3629d60a&sv=2)

## Filtering leaderboard results

The leaderboard's date range can be adjusted through the `Date range` menu:

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FyntHTpYkVYS6Of1KRju4%252Fimage.png%3Falt%3Dmedia%26token%3Dfbbbf8c1-4040-41b2-bd0a-e695ffea8ffd&width=768&dpr=4&quality=100&sign=17e41997&sv=2)

The search bar provides quick access to any warden or team ranked on the leaderboard.

## User profiles

To view a user's profile, click on their name in the leaderboard:

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FXpzyt6ezF9wKPiJFSF1p%252Fimage.png%3Falt%3Dmedia%26token%3D6e16b6e7-3b35-49fb-bab5-3cd0ebaa2571&width=768&dpr=4&quality=100&sign=a07f5b67&sv=2)

## Editing your public Code4rena profile

[SR wardens](/roles/sr-wardens) have the option to customize their Code4rena profile by adding:

* Links
* Highlights

They can also choose to display or hide the "Helped Secure" logo cluster, which highlights audits in which you've participated.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252F1pT8VQPJ1YaJYl35oHVQ%252Fimage.png%3Falt%3Dmedia%26token%3D8b020ce8-78aa-4daa-8acd-39f046a35038&width=768&dpr=4&quality=100&sign=d4f62bdc&sv=2)

A customized C4 profile, showing highlights, links, and the "Helped Secure" section.

If you have the [SR role](/roles/sr-wardens), you can find these options in [your Account settings page](https://code4rena.com/account).


Last updated 6 months ago

Was this helpful?

---


# Participate in audits

Source: https://docs.code4rena.com/platform-guide/platform-guide-for-wardens/participate-in-audits

## Join an audit

To join an audit, navigate to the audit page and click the `Join audit` button.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FMbOqIV1fvaUcaXvPApwG%252Fimage.png%3Falt%3Dmedia%26token%3Dc55b9d66-cd41-46d8-a660-f0e1d43c13ba&width=768&dpr=4&quality=100&sign=dd37eae1&sv=2)

Joining the audit will reveal the available details.

Once you've joined the audit, you'll be able to see the available details, including links to view the audit repo and submit findings.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FroenSSpXxoxE0LYVFcNC%252Fimage.png%3Falt%3Dmedia%26token%3D7b98df15-ddf6-43cc-a7f5-8ebb17441e07&width=768&dpr=4&quality=100&sign=3fe02fd2&sv=2)

## Audit access

* Most Code4rena audits are open and public.
* Some audits have special requirements and limited access. Details are normally posted in the #rsvp channel in the Code4rena Discord server.
* Private audits show "Audit details are not available," with an explanation.

## Making submissions

* To submit a finding, join the relevant audit, and a `Submit finding` button will appear.
* Click this button to access the submission form.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FQqyra1zf46n9JfFba0lQ%252Fimage.png%3Falt%3Dmedia%26token%3Db2f23fe8-614c-43e3-9442-86582b89d235&width=768&dpr=4&quality=100&sign=c713ad27&sv=2)

The submission form fields will vary depending on the Severity rating you select.

Each warden has a limited number of submissions per audit; see "[Submission limits based on signal](/roles/signal#submission-limits-based-on-signal)" for more details.

## **Required information**

**Severity rating** *(required)*

* Select from dropdown: High severity, Medium severity, QA (Quality Assurance)
* **High and Medium severity** require additional fields (root cause links and Proof of Concept)
* Severity selection determines the structure of your submission
* Review [Code4rena's severity categorization guide](https://docs.code4rena.com/competitions/severity-categorization) for detailed criteria

**Title** *(required)*

* Maximum 255 characters
* Summarize your findings for the bug or vulnerability
* This becomes the issue title, and should be clear and descriptive

**Links to root cause** *(required for High/Medium risk submissions)*

* Provide GitHub links with specific line numbers to the vulnerability location
* Format: `https://github.com/code4rena/project/file.sol#L44-L55`
* Use "Add another link" to reference multiple code sections

**Vulnerability details** *(required)*

* Add your submission details in Markdown. The editor includes a `Preview` tab
* The template provided is merely intended as guidance about what types of details to include in your submission:

  + `## Finding description and impact` - include a detailed explanation of the root cause and impact(s)
  + `## Recommended mitigation steps` - describe the best method(s) to mitigate the finding
* The editor supports full markdown formatting and math notation.

**Proof of Concept (PoC)** *(Optional but* [*often required for Solidity/EVM audits*](/competitions/submission-guidelines#required-proof-of-concept-poc-for-solidity-evm-audits)*)*

* Separate Markdown field for PoC code
* Check the audit repository and [Code4rena's submission guidelines](https://docs.code4rena.com/competitions/submission-guidelines) for specific PoC requirements
* Supports full Markdown formatting for code examples and explanations

## **Before submitting:**

1. **Review requirements** - Check the audit repository and [submission guidelines](https://docs.code4rena.com/competitions/submission-guidelines) for specific requirements
2. **Verify links** - ensure all GitHub links point to correct line numbers
3. **Use Preview** - Review formatting and clarity with the `Preview` tab
4. **Check policies** - Review the code of conduct and submission policy (both linked from the submission form)

## Viewing your submissions

There are two ways to access your submissions to audits:

1. Navigate to "Submissions" in the main menu to view all your submissions across every audit you've participated in. This centralized dashboard helps you track your entire Code4rena submission history. From the "Submissions" page, scroll to the relevant audit, click the `Expand` toggle, then select the submission you want to view.
2. From the `Audits` page, select the relevant audit, then select the appropriate option depending on the audit's current phase:

   1. During the submission (Active) phase, click the `Your Findings` tab to view your submissions
   2. After submissions close, click `View dashboard` to view *all* submissions to the audit, including your own. ([The dashboard is documented here.](/platform-guide/platform-guide-for-wardens/the-audit-dashboard))

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FAZ4Ls7ethm2sCyU5ewoO%252Fimage.png%3Falt%3Dmedia%26token%3D9fed8768-a4e9-44eb-97b1-40c6e916b6c6&width=768&dpr=4&quality=100&sign=e5dda6d2&sv=2)

The "Submissions" tab displays a list of audits you've submitted to.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FpVyCCnOQnDovDzxlz5ik%252Fimage.png%3Falt%3Dmedia%26token%3D4a68e09c-a6bf-4e55-92bf-e94ce68a87a4&width=768&dpr=4&quality=100&sign=df238f1c&sv=2)

Expanding an audit in the list shows all of your submissions to that audit.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FPaPkU6q6HLHr72ApHf42%252Fimage.png%3Falt%3Dmedia%26token%3Dc833ced8-9042-4735-bcfa-de4660711794&width=768&dpr=4&quality=100&sign=437090f6&sv=2)

To view all of your submissions to an audit, you can also navigate to that audit's page and select the "Your Findings" tab.

Note: Judges are permitted to edit submission titles for clarity, so your submission may appear with an altered title.

## Editing submissions

Submissions can only be edited or withdrawn within 2 hours of creation.

Click on any submission card showing a clock icon to enter the editing mode.

During the two-hour submission window, clicking into a submission will allow you to edit or withdraw your submission.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FpN1bMWeiDVZBZQqUNmfD%252Fimage.png%3Falt%3Dmedia%26token%3Dc5ae4b23-53eb-489b-a7f7-8244beecf4f9&width=768&dpr=4&quality=100&sign=bee30504&sv=2)

Your submissions will display a countdown to show how long you have left to make changes.

You will also see a countdown on the editing screen. The edit interface uses the same form layout as creating new submissions, with all your original content pre-populated.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FPe2BcjyBotRBAzUEwxpY%252Fimage.png%3Falt%3Dmedia%26token%3Db2a3eb7e-caf8-4a32-badc-cd04ca6756c0&width=768&dpr=4&quality=100&sign=96d3cbbd&sv=2)

## **Edit interface features**

* **Same form fields** - All original submission fields (title, severity, root cause links, vulnerability details, PoC) are editable
* **Edit/Preview tabs** - Full markdown editing capabilities with preview functionality
* **Precise deadline warning** - Blue notification shows exact deadline (e.g., "you must submit your changes or withdraw your submission by 3:33 PM (in 119 minutes 11 seconds)")

## **Available actions**

At the bottom of the edit screen, you'll see three action buttons:

* **Undo changes** - Revert all modifications back to the original submission content
* **Withdraw submission** - Permanently remove the submission from the audit (cannot be undone)
* **Save submission** - Update the submission with your changes


Last updated 5 months ago

Was this helpful?

---


# Sponsor Q&A

Source: https://docs.code4rena.com/platform-guide/platform-guide-for-wardens/sponsor-q-and-a

Code4rena's in-app Q&A feature enables direct communication between wardens and sponsors during active audits, allowing wardens to ask technical questions about the codebase and receive clarifications from the project team.

## Key things to know

* **New threads are private by default.** You can double-check the privacy settings in the right side column.
* **Sponsors are not required to reply to all threads.** To maximize your odds of receiving a helpful response:

  + Ensure that your thread is clear, concise, and not already answered in the audit's provided `README` and documentation.
  + Focus on your most important clarification needs.
  + Allow sufficient time for a response. Bear in mind that sponsors often receive questions from dozens of wardens — and that they have limited time to allocate to warden Q&A.
* **Read the docs first:**

  + Before using the Q&A feature, be sure to check the audit repo for project documentation, and search for answers there.
  + Most Code4rena audits are supported by our **Docs Wolf documentation bot** 🤖**.**

    - The bot will make its best attempt to answer based on the codebase and documentation, and link you to relevant resources.

      * Instructions for using Docs Wolf are posted in each audit's dedicated Discord channel.
* **Question limits by role:**

  + Wardens who don't have [the SR role](/roles/sr-wardens) are limited to **one open thread at a time.**

    - You must wait for your current question to be resolved and/or closed before posting another.
  + [SR wardens](/roles/sr-wardens) may open multiple threads.

    - However, it is still recommended to keep your questions focused and concise.

## Accessing the Q&A Feature

Navigate to any active audit and click the **Q&A** tab (alongside `Details` and `Your Findings`)

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FgPHjwRTbZZT304wqHgrC%252Fimage.png%3Falt%3Dmedia%26token%3Dd4565075-611e-4cee-a568-c90859d9620c&width=768&dpr=4&quality=100&sign=95118390&sv=2)

If you have no active threads, you will only see a button to open a new thread.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FnzvQm5XL1CAtNPbjul3h%252Fimage.png%3Falt%3Dmedia%26token%3D7374f0bb-6f51-4a57-8d13-74b0fd02a02e&width=768&dpr=4&quality=100&sign=468f43ee&sv=2)

If you have asked a question already, you will see a list of threads (both open and closed).

If you have new replies from the sponsor, you will see a red notification alert.

## Creating New Questions

**Starting a question thread:**

1. Click **"New thread"** button in the Q&A tab
2. Provide a clear, specific title for your question
3. Include technical details, code references, or specific scenarios
4. Submit your question to the sponsor team

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FaqmK1Wwm4yWTbKSwHHxv%252Fimage.png%3Falt%3Dmedia%26token%3Dbd9bc4c7-5726-44a4-b983-d7b628218954&width=768&dpr=4&quality=100&sign=cc8db79&sv=2)

New threads are private by default and only visible to the sponsor team, judge, and Code4rena staff.

**Best practices for questions:**

* **Be specific** - Reference exact functions, contracts, or line numbers when possible
* **Provide context** - Explain what you're trying to understand or verify
* **Stay technical** - Focus on implementation details rather than general questions
* **Check existing threads** - Review current and closed questions to avoid duplicates


Last updated 5 months ago

Was this helpful?

---


# The audit dashboard

Source: https://docs.code4rena.com/platform-guide/platform-guide-for-wardens/the-audit-dashboard

Once the submission deadline passes, wardens may view all submissions to most Code4rena audits. (Exceptions — such as audits that include live/deployed code — are normally noted in the audit repo `README` file.) After submissions close, you can use [the audit dashboard](/platform-guide/platform-guide-for-wardens/the-audit-dashboard) for an overview of judging progress.

## Dashboard overview

After clicking "View dashboard," you'll see an abbreviated list of the highest-severity findings, and any recent comments:

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252Fd7aqP2qc9GKJTTyTbPGd%252Fimage.png%3Falt%3Dmedia%26token%3D0daf0c84-1b53-4a40-9759-7891b13d8291&width=768&dpr=4&quality=100&sign=a883aa6&sv=2)

This is an overview screen only; to view all submissions or comments, select the relevant "View all" button.

## Submission list

From the dashboard, clicking "View all submissions" will take you to the full submission list:

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FH6dJ8L9DU8T2hdkAbbqD%252Fimage.png%3Falt%3Dmedia%26token%3D2176f3ff-639d-49dd-9369-59d6154614c3&width=768&dpr=4&quality=100&sign=866d69a4&sv=2)

This is a table view that shows:

* ID: the finding (`F-`) and/or submission (`S-`) ID

  + Finding = set of duplicate submissions sharing a root cause
  + Submission = an individual warden or team's submission to the audit
* Severity: the judge's assessed severity (see [Severity criteria](/competitions/severity-categorization))
* Title: the title of the submission
* Triage: initial evaluation by the Validator [deprecated]
* Review: input from the sponsor, if available
* Judge: validity and quality evaluations from the judge
* Author: username of the warden or team who created this submission

Findings are expanded by default, to display the individual submissions within each duplicate set. For a more compact view, use the `Filter` menu to select `Primary submissions` only.

## Filters

There are three ways to filter submissions in the dashboard:

1. Search
2. `Filter` menu
3. `Severity` menu

## Search

The search function offers a convenient way to locate submissions related to a particular file or function. You can also search by warden/team name.

From the `Filter` menu, you can select:

* All submissions (default)
* Primary submissions - submissions selected as `Primary` (best) within a finding (duplicate set)
* Satisfactory submissions - submissions confirmed as valid and sufficient quality by the judge
* Partial credit - submissions evaluated as only eligible for [partial credit](/awarding#duplicates-getting-partial-credit) by the judge
* Eligible for awards - all submissions currently eligible for awards

Note that all judge rulings are subject to change until awards are announced.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FDzOSGWzvSEEwCVCVc6hQ%252Fimage.png%3Falt%3Dmedia%26token%3Da7fa237e-9c0c-442a-a3de-6d61ca20588e&width=768&dpr=4&quality=100&sign=a1e64991&sv=2)

Selecting "Primary submissions" will produce a more compact view in the dashboard, by hiding all duplicates.

## Filtering by severity

From the `Severity` menu, you may select:

* All
* Highs and Mediums
* Highs
* Mediums
* Lows
* QA/Gas

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FFIKav7UXsDTEY7L5eSM9%252Fimage.png%3Falt%3Dmedia%26token%3Df3f8f427-da3c-442c-be1f-57c4bf2c32e7&width=768&dpr=4&quality=100&sign=54634982&sv=2)

## Individual findings and submissions

To view an individual finding or submission, click on the title from the submission list. It will open the relevant finding or submission.

## Finding pages

When viewing a finding (group of duplicate submissions), you'll see:

* an excerpt from the primary submission in the finding set,
* a timeline of inputs and evaluations, and
* a list of all duplicates within the finding set.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FTgAvSFRluUaNE4PcPlTK%252Fimage.png%3Falt%3Dmedia%26token%3Db468e182-636f-4638-8a6d-813b55182fc0&width=768&dpr=4&quality=100&sign=9c4e3bc1&sv=2)

The upper section of a finding page shows the finding number (F-67 in the example), an excerpt from the primary submission, and a timeline of inputs and evaluations.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FNb90eLlK8sZ48jtYjoSG%252Fimage.png%3Falt%3Dmedia%26token%3D8a13a2a8-faed-4adc-add8-2b9b7bafc6a4&width=768&dpr=4&quality=100&sign=e728d7c8&sv=2)

Farther down, there's an option to view the full-length primary submission, and a list of all duplicates.

## Submission pages

The Submission page is slightly different from the Finding page:

* Instead of an excerpt, the full-length submission is displayed.
* Duplicates are listed in a block on the right side of the screen.
* You may also view a list of submissions by the same author.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252F7j4h9VKPDRmF7dQ5hkSj%252Fimage.png%3Falt%3Dmedia%26token%3D203d4c6b-ac03-43b0-a3d2-4268e6f485fe&width=768&dpr=4&quality=100&sign=d7ed8b81&sv=2)

The Submission page is fairly similar to the Finding page, with slight differences.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FO6i1Ba2KIdl6D5u42xiN%252Fimage.png%3Falt%3Dmedia%26token%3D7981cbd0-b655-43b8-af1b-980fa38deac4&width=768&dpr=4&quality=100&sign=2cd82ef9&sv=2)

Selecting the "By this author" tab displays a list of submissions by the same author as the submission you're viewing.

## Interpreting the Timeline

The Timeline block displays a series of inputs and evaluations; it expands as the audit phases unfold.

* **Submitted:** severity level selected by the warden (or team) at the time of submission
* **Triage:** initial evaluation by the Validator [deprecated]
* **Review:** input from the sponsor, if available.
* **Judging:** validity, severity, and quality evaluations from the judge (see [Judging criteria](/competitions/judging-criteria))

Code4rena makes the final determination regarding validity, quality, and severity. All other inputs are advisory.

![](https://docs.code4rena.com/~gitbook/image?url=https%3A%2F%2F2463680924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MYGYvqTD29_fAaod9NJ%252Fuploads%252FUXhO8xUE6QtsYnpKklQ7%252Fimage.png%3Falt%3Dmedia%26token%3D0e64226b-f83f-4818-8ce9-cf3eb80f3bb1&width=768&dpr=4&quality=100&sign=85708bfb&sv=2)

The Timeline expands as each audit moves through triage and judging. Shown: the Timeline as it appears during Post-judging QA.


Last updated 1 month ago

Was this helpful?

---


# Advancement and roles

Source: https://docs.code4rena.com/roles

Code4rena audits let people of a wide range of skill levels get rewarded while showcasing their talent in order to make the DeFi ecosystem more secure.

## Signal metrics

[The `signal` metric](/roles/signal) is Code4rena's accuracy metric for wardens and teams: a means of identifying wardens and teams who have consistently submitted valid findings. `signal` is a factor (along with [certification](/roles/certification-id-verification)) in determining submission limits and commenting privileges during Post-Judging QA (PJQA).

## Wardens

Wardens protect the DeFi ecosystem from threats by auditing code.

Anyone can register to participate in an audit. Learn more about registering as a warden and joining an audit [here](/getting-started).

## SR Wardens and certified contributors

Wardens who meet certain performance criteria within C4 earn the Certified Security Researcher (SR) role, which provides access to:

* Unrestricted number of comments during Post-judging QA
* Unrestricted number of submissions per audit

[More details here](/roles/sr-wardens).

## Judges and Scouts

These prestigious roles are assigned to the most skilled and fair-minded members of Code4rena's community:

* [Judges](/roles/judges)
* [Scouts](/roles/scouts)


Last updated 26 days ago

Was this helpful?

---


# Certification (ID verification)

Source: https://docs.code4rena.com/roles/certification-id-verification

Certifying your identity (also known as ID verification, or KYC) is an optional step for warden accounts that — along with your [signal](/roles/signal) score — unlocks [privileges](/roles/signal#privileges-based-on-signal) including higher submission limits and post-judging QA comments.

C4's certification process is as follows:

1. From the account screen, a warden submits the Certified Contributor Application form.
2. C4's KYC provider, [Persona](https://www.withpersona.com/), verifies their identity.
3. Persona certifies a contributor as having successfully completed their identity verification process.

Your certification status can be viewed on [your C4 account screen](https://code4rena.com/account).

In the event that a certified contributor violates or is believed to have violated the Terms and Conditions, Code4rena reserves the right to pursue all remedies available, including removal from the platform or legal action.

## How long does it take to get certified?

* Persona verification can usually be completed within a few minutes, assuming you have the necessary documents available.
* For tips on verifying your identity with Persona, visit [Persona's Help Center](https://help.withpersona.com/for-end-users/).


Last updated 26 days ago

Was this helpful?

---


# Judges

Source: https://docs.code4rena.com/roles/judges

Judging is a central part of the C4 competitive audit model.

In order to align wardens with sponsors, and remove the burden from each player in the arena from determining the ultimate severity of findings, C4 selects an impartial judge for each audit.

Judges review warden findings and sponsor input, and come to their own independent conclusion. Doing this enables C4 to determine awards for wardens and categorize findings for audit reports.

In order to ensure the impartiality of the process, C4 judges determine the severity of findings, considering input from both wardens and sponsors. Judges receive compensation for their work as a portion of the audit pool.

## Becoming a judge

Judging is an important and elite Code4rena role. It also provides a guaranteed payout from each audit.

In order to ensure the impartiality and depth of knowledge we aim for among judges, judge applications are reviewed carefully by senior Code4rena staff.

## Minimum criteria (subject to change)

* Compete in at least 3 Code4rena audits
* Find at least 3 high severity vulnerabilities
* Must be a [Certified C4 contributor](/roles/certification-id-verification) in good standing

## Non-technical criteria

* **Sense of fairness**—i.e. evidence suggests you don't show favoritism, but instead aim for a fair competition where quality is rewarded.
* **Discernment**—ability to impartially a) assess the logic behind a vulnerability, b) verify its validity, and c) determine its severity, given the specifics outlined by the warden as well as the broader context of the code.
* **Clear written communication**—your English does not need to be perfect, but you should be able to engage in nuanced discussions with wardens and sponsors via written text.

## How to apply

Complete [this form](https://code4rena.com/judge-application/) and share:

* Short bio/intro and summary of relevant experience
* Links that help demonstrate your expertise
* 3 example submissions to Code4rena audits that were judged high severity
* Description of how each submission demonstrates your depth of knowledge

**Note:** judge applications are reviewed on an as-needed basis. Notices of application and review windows will be posted in the C4 Discord server.

## Judge selection process

Being a judge is a critical role and we only have so many spots.

Judge applications are reviewed regularly by senior C4 staff. We will review your application and give you a "yes" or "not yet".

## Is it possible for a warden who competed in an audit to judge that same audit?

Yes—but in the interest of impartiality, they must forgo any awards they would have received for their findings in said audit.

Teams that include the judge are also ineligible for awards in such cases, to avoid conflict of interest.

## Staff oversight of judges

Judges' decisions and performance are reviewed and overseen by senior members of Code4rena staff.

## Validators

The Validator role was deprecated in April 2025; the information here is to assist in understanding the evaluation process for validator-supported audits.

Validators triaged submissions from wardens with accuracy rates below the qualifying threshold; they also de-duped all submissions.

* Each competition had a [**qualifying threshold**](/roles/signal#validation-process) that allowed wardens to bypass validators. This threshold wass based on [signal score](/roles/signal), as well as being established as a quality contributor and as non-sybil.
* Qualified wardens’ submissions were shared with the sponsor team immediately after submissions closed.
* All other wardens’ submissions were routed through a Validator.

**Validators** reviewed submissions immediately after submissions close. The judge reviewed all submissions except those the Validator marked as `spam`.


Last updated 26 days ago

Was this helpful?

---


# Scouts

Source: https://docs.code4rena.com/roles/scouts

Code4rena Scouts specialize in scoping and pre-audit intel. They assess the following factors to help determine the optimal parameters for an audit:

* Library dependencies
* External calls
* Timelocks
* SLoC

In evaluating the scope of an audit, Code4rena Scouts provide valuable, high-level information to all parties:

* **For Wardens:** Ensuring that the audit repo is set up correctly ensures that those considering participating have a well-organized, consistently-formatted workspace.
* **For Projects:** Providing feedback to the project team on the scoping accuracy and repo preparedness helps accelerate the audit launch while maintaining consistency.

## How the Scouting process works

1. Scouts review the proposed code and repo before an audit has begun.
2. Feedback is provided to the Project and Code4rena teams on scoping accuracy and preparedness. Any concerns are flagged, and the Scout can supply technical guidance regarding any necessary adjustments.
3. Once the audit begins, the Scout monitors the public code repo to ensure all guidelines are adhered to.
4. As compensation, Scouts receive a flat fee of $500 USDC per competition.

## Scout selection process

Due to the highly sensitive nature of the role, Scouts are hand-picked by the Code4rena team.


Last updated 26 days ago

Was this helpful?

---


# Signal metrics

Source: https://docs.code4rena.com/roles/signal

The `signal` metric is Code4rena's accuracy metric for wardens and teams: a means of identifying wardens and teams who have consistently submitted valid findings. For a warden or team, it is the ratio between the number of valid High- or Medium-risk findings versus all of the high and mediums they've submitted.

`signal = findings / submissions`

A signal of `1` means that the user has consistently submitted valid H and M, whereas a signal of 0 means the user has either submitted only invalid findings or has not yet met the participation threshold to calculate signal.

Contributors' `signal` will display as `null` until they have:

* participated in at least 3 audits, and
* submitted at least 5 findings

## How is `signal` used?

`signal` is a factor in determining eligibility for a variety of privileges within Code4rena competitions, including:

* [Eligibility for the `SR` role](/roles/sr-wardens) and its associated privileges
* [Unrestricted submissions](/roles/signal#submission-limits-based-on-signal)
* [PoC requirements waived for high-signal wardens](/competitions/submission-guidelines#required-proof-of-concept-poc-for-solidity-evm-audits)

This is not an exhaustive list, and is subject to change.

## Privileges based on signal

## Submission limits based on `signal`

To reduce spam while keeping C4 competitions open to all, `signal` determines the maximum number of submissions wardens/teams can make to each audit.

* The higher your signal , the more submissions you get.
* Low- or no-signal users' submissions are capped, to disincentivize "spray and pray" behavior and encourage newer wardens to focus on their highest-risk, highest-quality findings.
* Earn bonus (or uncapped) submissions by verifying your identity and submitting your tax information.
* Unrestricted submissions unlock at 0.4 (40%) signal + verified ID/tax info.

These limits are subject to change, based on the volume of low-quality/spam submissions we receive over time.

User's signal

Max # of submissions per audit

Certified + submitted tax info

`null` or < 0.2

2

4

0.2-0.3999

2

10

0.4+

10

unrestricted

* If (( signal = `null` (e.g. you are a newly-registered user, or have participated in < 3 audits) OR signal < 0.2 )) and you have not verified your identity and submitted tax info, then you will be limited to 2 submissions per audit.

  + If you meet these criteria, and have both [verified ID](/roles/certification-id-verification) and [submitted tax info](/awarding/awarding-process#tax-information-for-code4rena-contributors-wardens-judges-etc), then you will unlock 2 bonus submissions per audit (i.e. max 4 submissions in total)
* If your `signal` = 0.2-0.3999 and you have not verified your identity and submitted tax info, then you will be limited to 2 submissions per audit.

  + If you meet these criteria, and have both [verified ID](/roles/certification-id-verification) and [submitted tax info](/awarding/awarding-process#tax-information-for-code4rena-contributors-wardens-judges-etc), then you will unlock 8 bonus submissions per audit (i.e. max 10 submissions in total)
* If your `signal` ≥ 0.4, you will be limited to 10 submissions per audit.

  + If you meet these criteria, and have both [verified ID](/roles/certification-id-verification) and [submitted tax info](/awarding/awarding-process#tax-information-for-code4rena-contributors-wardens-judges-etc), then you will have unrestricted submissions.

**How do submission limits apply to teams?**

* Warden teams are subject to the same criteria as individual wardens.
* Submission limits apply to both teams and individual wardens.
* To unlock bonus submissions, *all* members of a team must verify their identity and submit tax info.

## PJQA comments based on `signal`

For competitions starting on or after January 8, 2026, all wardens may comment during post-judging QA (PJQA) if they:

* Have [verified their identity (KYC)](/roles/certification-id-verification)
* Have [submitted tax info](/awarding/awarding-process#tax-information-for-code4rena-contributors-wardens-judges-etc)
* Submitted at least one finding to the relevant competition

User's signal

Max # of PJQA comments per audit

Certified + submitted tax info

`null` or < 0.2

0

1

0.2-0.3999

0

3

0.4+

0

unrestricted

## High/Medium submissions downgraded to QA

Currently, all High- and Medium-risk submissions that are downgraded to Low-risk (QA) are excluded from `signal` calculations.

We expect this to change in future, and will update this documentation as the formula evolves.

## FAQ

**Q. How can I view my signal?**

A. You can view your `signal` score on [your account settings screen](https://code4rena.com/account).

**Q. How can I view my team's signal?**

A. You can view the score on the [teams page](https://code4rena.com/account/teams) in your account settings

**Q. Who else can see my signal?**

A. Authenticated users can see the `signal` score of wardens and teams alongside their submissions, while logged in to [Code4rena.com](https://code4rena.com).

**Q. How often is signal updated?**

A. It is updated daily.

**Q. What happens if my signal changes — and crosses the submission limit threshold — during an audit?**

A. If a warden’s signal increases to cross a submission limit threshold during an audit, their submission limit will increase accordingly. Conversely, if their signal decreases to drop below a submission limit threshold during an audit, their submission limit will decrease accordingly. Any submissions made prior to the change will be preserved.

**Q. How much historical data does the** `signal` **calculation use?**

A. The score is calculated with data from March 2023 to present.


Last updated 3 days ago

Was this helpful?

---


# SR Wardens

Source: https://docs.code4rena.com/roles/sr-wardens

Wardens who meet certain performance criteria within C4 gain the Certified Security Researcher (SR) role, which provides access to:

* Unrestricted number of comments during post-judging QA
* Unrestricted number of submissions per audit

The criteria (visible on [your Code4rena account screen](https://code4rena.com/account)) to become a Certified SR are as follows:

1. **Your** `signal` **is 40% (0.4) or higher.** This is shown at the top of the page, below the “User information” heading.
2. **Your tax information has been accepted.** Scroll down to the “Tax information” section to confirm that your tax information has been accepted. If not, you must submit it to qualify for the SR role.
3. **Be approved as a Certified C4 contributor.**
4. Abide by the Certified Contributor Terms and Conditions.
5. **Final manual review by C4 staff.** After the above criteria are met, Code4rena staff will perform a manual review of your account before assigning the Certified SR role. This review typically completes within 1 business day but may take up to 7 business days during busy periods or holidays. If you haven't heard back after 7 business days, feel free to create a [helpdesk ticket](https://code4rena.com/help).


Last updated 10 minutes ago

Was this helpful?

---


# Sponsors

Source: https://docs.code4rena.com/sponsors

Any project can submit a request to sponsor a C4 audit. Just [complete this form](https://go.code4rena.com/start) and we will reach out to set up a meeting or send over a scoping questionnaire to get the ball rolling.

One of our team members will review your repo, assess your responses and contracts, and recommend which audit package would be appropriate for your scope.

**If you decide to move ahead with an audit, all relevant code will be made public at the time of your audit in most cases.** We also offer KYC and private competitions if privacy is a need; just let our team know.

## Determining pot size

To attract warden participation in the highly competitive engineering market, we will advise you on standard award pool sizes based on the scope of the audit. We regularly evaluate and adjust audit pricing to ensure incentive alignment with wardens. Sponsors always have the option of boosting their award pool, which tends to attract more warden talent and attention.

## No platform fee

Code4rena's audit competitions have zero platform fees. [This blog post](https://www.zellic.io/blog/code4rena-free-contests) explains how we cover our operating costs, and what we mean by "running audit competitions for free."

## Audit scheduling

Our standard, one-week audits start and end on weekdays at 20:00:00 UTC. Due to high demand, we only lock audits into the schedule after receiving a deposit for the audit.

To provide your team with the most efficient and effective code review, we require your team to add ALL code, documentation, and notes to your audit repo at least 2 business days prior to your audit start time.


Last updated 5 months ago

Was this helpful?

---
