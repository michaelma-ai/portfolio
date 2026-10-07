# Eval Harness
[![Eval Harness demo](https://img.shields.io/badge/Eval%20Harness-Try%20the%20demo-1a7f5a?style=flat-square)](https://michaelma-eval-harness.vercel.app/) [![Central README](https://img.shields.io/badge/Central-README-0b3d91?style=flat-square)](https://github.com/michaelma-ai/portfolio/blob/main/central/README.md)

<div align="justify">

An offline evaluation harness for Central to answer "is the AI working?" and serves as a worked example for AI PMs and engineers learning to build evals.

</div>

## The Problem and the Solution
<div align="justify">

Built for small teams shipping AI agents without a dedicated eval team, who often ship once the product "feels ready", and for anyone learning how to evaluate AI products. **Scope:** offline only; 53 of the 137 metric specs need production traffic and are defined but not yet measured. Each row pairs a problem these teams face with what Eval Harness does about it.

</div>

<table>
  <tr>
    <th width="18%" align="left">Problem</th>
    <th width="38%" align="left">Why it matters</th>
    <th width="44%" align="left">What Eval Harness does</th>
  </tr>
  <tr>
    <td valign="top"><b>Knowing When It's Safe to Ship</b></td>
    <td valign="top">A prompt edit, a model swap or a provider change can break an agent, and small teams often ship after spot-checks.</td>
    <td valign="top">Captures real outputs from the served product, scores them against 211 golden cases in six golden sets, and gives one SHIP or HOLD verdict.</td>
  </tr>
  <tr>
    <td valign="top"><b>Trusting the LLM Judge</b></td>
    <td valign="top">An LLM judge is itself a model: if it disagrees with people, its scores mislead.</td>
    <td valign="top">Judge scores count only when Cohen's κ against 54 human-labeled cases is at least 0.60.</td>
  </tr>
  <tr>
    <td valign="top"><b>Checking Every Step</b></td>
    <td valign="top">An agent can skip an approval or call the wrong tool and still write a reply that reads well.</td>
    <td valign="top">Code checks the steps (tier, tools called, approval gates, retrieval rank, PII), and the judge scores only what code cannot decide.</td>
  </tr>
  <tr>
    <td valign="top"><b>Eval Errors vs Product Failures</b></td>
    <td valign="top">Rate limits, judge outages and stale captures fail cases the product would pass, and look like product regressions.</td>
    <td valign="top">Every case is pass, fail or undecided, and undecided never counts as a pass. Each run records the judge, rubric and capture version it used.</td>
  </tr>
  <tr>
    <td valign="top"><b>Learning to Build Evals</b></td>
    <td valign="top">Most eval guidance explains concepts; few examples show a complete eval system working on a real product.</td>
    <td valign="top">The demo shows every part on recorded runs of Central: golden set, metric specs, judge labels, run history and model comparisons.</td>
  </tr>
</table>

## Demo
<table width="100%">
  <tbody>
  <tr>
    <td colspan="6" align="center" valign="top">
      <img src="assets/home.png" alt="Eval Harness home page" width="100%"><br>
      <sub><b>Release verdict and trend</b></sub>
    </td>
  </tr>
  </tbody>
  <tbody>
  <tr>
    <td colspan="6" align="center" valign="top">
      <img src="assets/assistant-evals.png" alt="Assistant Evals page" width="100%"><br>
      <sub><b>Assistant Evals</b></sub>
    </td>
  </tr>
  </tbody>
  <tbody>
  <tr>
    <td colspan="2" align="center" valign="top" width="33%">
      <img src="assets/judge-validation.png" alt="Judge Validation page" width="100%"><br>
      <sub><b>Judge Validation</b></sub>
    </td>
    <td colspan="2" align="center" valign="top" width="33%">
      <img src="assets/golden-dataset.png" alt="Golden Dataset page" width="100%"><br>
      <sub><b>Golden Dataset</b></sub>
    </td>
    <td colspan="2" align="center" valign="top" width="34%">
      <img src="assets/metrics.png" alt="Metrics page" width="100%"><br>
      <sub><b>Metrics</b></sub>
    </td>
  </tr>
  </tbody>
</table>

## System Design
<div align="justify">

*This section covers choices that affect a single component. Decisions that span several components are in [Tradeoffs and Decisions](#tradeoffs-and-decisions).*

</div>

<a href="https://raw.githubusercontent.com/michaelma-ai/portfolio/main/eval_harness/assets/system-design.svg"><img src="assets/system-design.svg" alt="Eval Harness system design: the golden set and Central feed capture; deterministic scorers and an independent LLM judge score each output into 33 P0 metrics; the judge's audit verdicts and human labels give Cohen's kappa; the release gate ships only when the P0 metrics meet their thresholds and kappa is at least 0.60" width="100%"></a>

<table>
<tr><th width="17%">Component</th><th width="25%">Purpose</th><th width="33%">What it is built from</th><th width="25%">Key decision and trade-off</th></tr>
<tr><td valign="top"><b>Metrics &amp; Golden Dataset</b></td><td valign="top">Define what success means for each component, and how it is tested</td><td valign="top">Each component has a north-star metric, broken into P0–P2 metrics with thresholds; golden test scenarios (211 in total) are then written to measure the offline P0 metrics</td><td valign="top">Measures offline P0 metrics only; P1–P2 metrics are defined but out of scope, and online metrics wait for real traffic</td></tr>
<tr><td valign="top"><b>Capture</b></td><td valign="top">Record the product's live output</td><td valign="top">Runs each case through the served app</td><td valign="top">Not Applicable</td></tr>
<tr><td valign="top"><b>Deterministic scorers</b></td><td valign="top">Score verifiable answers with code</td><td valign="top">Checks for tier, tools, approval gates, retrieval rank and PII</td><td valign="top">Code first; the judge only where code cannot decide</td></tr>
<tr><td valign="top"><b>LLM judge</b></td><td valign="top">Score open-ended quality</td><td valign="top">Independent judges mistral-nemotron (backup deepseek-v4-flash) with a binary pass/fail rubric and reason for verdict </td><td valign="top"> No model grades its own output to remove judge bias. Binary labels are quick and unambiguous. </td></tr>
<tr><td valign="top"><b>Judge validation</b></td><td valign="top">Decide whether judge scores can be trusted</td><td valign="top">54 audit cases with human labels to measure human-judge observed alignment and Cohen's κ</td><td valign="top">Judge scores count only at κ ≥ 0.60 on current labels</td></tr>
<tr><td valign="top"><b>Release gate</b></td><td valign="top">Decide whether the whole product ships: one SHIP or HOLD across all components</td><td valign="top">SHIP only if all 34 offline P0 metrics meet their thresholds, every P0 has test cases, and the judge's κ is at least 0.60</td><td valign="top">One P0 miss holds the release; so do stale labels.</td></tr>
<tr><td valign="top"><b>Telemetry</b></td><td valign="top">Instrument every case, so latency, cost and failures trace back to individual calls</td><td valign="top">Phoenix traces of every model and tool call for each case to measure latency, cost and quality with deep dive capability</td><td valign="top">Not Applicable</td></tr>
<tr><td valign="top"><b>Eval Dashboard</b></td><td valign="top">Easy to access UI dashboard to manage end-to-end AI product evals </td><td valign="top">Shows release verdict enabling drill down into each component's case and its trace, and covers golden dataset, metrics, model comparisons and judge labeling.</td><td valign="top">Built in-house; see Tradeoffs and Decisions.</td></tr>
<tr><td valign="top"><b>Model eval suite</b></td><td valign="top">Decide which models from free inference providers to bring into Central</td><td valign="top">Served model evals score the current lineup on 34 cases, Fast evals sweeps provider catalogs, Deep evals re-tests the fast passers, and Benchmarks adds published scores.</td><td valign="top">Tested by integrating each model into Central, as leaderboards do not showcase performance within the Central app use case. </td></tr>
</table>

## Tradeoffs and Decisions
| Decision                                   | What it changed, and what it cost |
| ------------------------------------------ | --------------------------------- |
| Offline golden set before live evals       | Every release is gated on the same 211 cases, so runs are comparable and cost \$0. The cost is no signal from real traffic: 53 of the 137 metric specs wait for it. |
| Built in-house over a hosted eval platform | Custom gate rules, \$0 and full run history, versus ~\$19–\$249 a month and 14–60 days of free retention on hosted platforms. The cost is building and maintaining the dashboard. |

## Evaluation Strategy & Results
<div align="justify">

Eval Harness evaluates Central offline on 211 golden cases: 157 across five product components, plus a 54-case judge audit set. Results below are from the 2026-09-02 release run, which passed the verdict to ship.

</div>

### Part 1: Evaluation Strategy
<table>
  <tr>
    <th width="10%" align="left">Axis</th>
    <th width="60%" align="left">How it is measured</th>
    <th width="30%" align="left">Role in the release</th>
  </tr>
  <tr>
    <td valign="top">Quality</td>
    <td valign="top">Each golden case passes, fails or is undecided (for example, the judge was down); undecided never counts as a pass. Cases roll up into P0 metrics with thresholds</td>
    <td valign="top">Gates the release: all 34 offline P0 rows must meet threshold</td>
  </tr>
  <tr>
    <td valign="top">Latency</td>
    <td valign="top">End-to-end time per case and time to first token, from Phoenix traces, at p50 and p95</td>
    <td valign="top">Reported, not gated.</td>
  </tr>
  <tr>
    <td valign="top">Cost</td>
    <td valign="top">Measured LLM calls × assumed tokens × a mid-range open-weight price (&#36;0.60 / &#36;2.00 per 1M), next to actual spend (&#36;0)</td>
    <td valign="top">Reported next to the verdict, not gated</td>
  </tr>
</table>

<div align="justify">

**Components eval approach:** the table shows a few P0 gates per component. All 34 offline P0 metrics and their thresholds are on the Metrics page of the [Eval Harness demo](https://michaelma-eval-harness.vercel.app/).

</div>

<table>
  <tr>
    <th width="21%">Component</th>
    <th width="18%">North star</th>
    <th width="6%" align="center">Cases</th>
    <th width="24%">Key P0 gates</th>
    <th width="31%">What the cases test</th>
  </tr>
  <tr>
    <td valign="top"><b>Model Selection</b><br>Which models should Central serve?</td>
    <td valign="top">End-to-end multi-turn task success (correct and safe) ≥ 85%</td>
    <td valign="top" align="center">34</td>
    <td valign="top">Tool call correctness ≥ 98% exact match, destructive-action gating 100%, prompt-injection resistance ≥ 95%</td>
    <td valign="top">7 prompt-injection resistance, 5 tool call correctness, 5 answer correctness, 5 single-turn task success, 5 multi-turn task success, 4 answer faithfulness, 3 destructive-action gating</td>
  </tr>
  <tr>
    <td valign="top"><b>Central Assistant</b><br>Does it complete the request without overstepping?</td>
    <td valign="top">Request success rate</td>
    <td valign="top" align="center">24</td>
    <td valign="top">Approval gate integrity 100%, task completion rate ≥ 90%, action-claim faithfulness ≥ 99%</td>
    <td valign="top">5 task completion rate, 4 approval gate integrity, 3 each for action-claim faithfulness, answer correctness, answer groundedness, post-approval resume correctness, and over-action and scope adherence</td>
  </tr>
  <tr>
    <td valign="top"><b>Knowledge Base</b><br>Does it retrieve the right source and answer only from it?</td>
    <td valign="top">% of queries correct, relevant and faithful</td>
    <td valign="top" align="center">30</td>
    <td valign="top">Recall@K ≥ 0.90, faithfulness (groundedness) ≥ 0.95</td>
    <td valign="top">10 Recall@K, Precision@K and F1@K, 7 faithfulness (groundedness), 7 answer correctness, 6 answer relevance</td>
  </tr>
  <tr>
    <td valign="top"><b>Personalization</b><br>Remembers the right things, and nothing it shouldn't?</td>
    <td valign="top">Personalized answer preferred ≥ 75% vs baseline</td>
    <td valign="top" align="center">49</td>
    <td valign="top">Sensitive PII write rate ≈ 0%, profile update correctness ≥ 95%, personalization helpfulness ≥ 75% preferred vs baseline</td>
    <td valign="top">13 profile update correctness, 10 sensitive PII write rate, 9 constitution adherence, 6 profile grounded recall, 4 profile fact-retention rate, 4 personalization helpfulness, 3 preference adherence</td>
  </tr>
  <tr>
    <td valign="top"><b>Safety</b><br>Catches harm without over-refusing?</td>
    <td valign="top">Safe-handling rate: harmful inputs caught, legitimate requests passed</td>
    <td valign="top" align="center">20</td>
    <td valign="top">Recall (safety coverage) ≥ 99%, adversarial attack success rate ≤ 2%, over-refusal rate ≤ 2%</td>
    <td valign="top">15 gate classification, scored for recall, precision, F2, over-refusal rate and pass-through rate; 5 adversarial attack success rate</td>
  </tr>
  <tr>
    <td valign="top"><b>Judge audit</b><br>Can the judge be trusted?</td>
    <td valign="top">Judge–human agreement (Cohen's κ)</td>
    <td valign="top" align="center">54</td>
    <td valign="top">κ ≥ 0.60</td>
    <td valign="top">Judge / human agreement (Cohen's κ) on 54 cases drawn from product captures: 17 Personalization, 12 Central Assistant, 10 Knowledge Base, 8 Safety, 7 Model Selection</td>
  </tr>
  <tr>
    <td valign="top"><b>Total</b></td>
    <td valign="top"></td>
    <td valign="top" align="center"><b>211</b></td>
    <td valign="top"><b>34 P0 gates</b></td>
    <td valign="top"></td>
  </tr>
</table>

<div align="justify">

**How the golden set was built:** the golden dataset was built to define what good looks like for Central. These are the principles it follows:

</div>

<table>
  <tr>
    <th width="28%" align="left">Principle</th>
    <th width="72%" align="left">Description</th>
  </tr>
  <tr>
    <td valign="top">Each P0 metric has goldens written for evaluation</td>
    <td valign="top">Each case is tagged with the one P0 metric it tests, and a P0 with no cases holds the release. Scoring each P0 separately keeps the safety-critical ones (attack, injection, PII, destructive-action and approval-gate) from being averaged away by easy passes.</td>
  </tr>
  <tr>
    <td valign="top">Each metric is tested across different case types</td>
    <td valign="top">Cases span four test types (happy path, negative, edge, adversarial) at three difficulties (easy, medium, complex). 24 of 27 metric groups have happy-path, negative and edge cases, and 7 also include adversarial ones.</td>
  </tr>
  <tr>
    <td valign="top">Most cases are written to make the product fail</td>
    <td valign="top">63% of the 157 product cases are negative, edge or adversarial (45, 36 and 18), against 58 happy path; 60 are rated complex and only 1 easy.</td>
  </tr>
  <tr>
    <td valign="top">Human-reviewed, whether written by hand or by an LLM initially</td>
    <td valign="top">Cases can be drafted by a person or generated by an LLM, but a human reviews every case and signs off on the ideal answer that defines success.</td>
  </tr>
  <tr>
    <td valign="top">Start with a small set of cases, and grow it from product use</td>
    <td valign="top">This first golden dataset shows the framework rather than full coverage as right now each P0 metric rests on 3–15 cases, so one failure moves it 7–33 points. Additional cases are added as using the product surfaces new failures and as more ambitious use cases are targeted.</td>
  </tr>
</table>

### Part 2: Results
<div align="justify">

The pass rate rose from 57% on 2026-07-29 to 99% (208 of 211) across 5 weeks on the release-cleared run of 2026-09-02: all 157 product cases passed, and the judge agreed with my labels on 51 of 54 audit cases (κ 0.74). 42 failing cases were logged with a fix: 31 in the product ([Central README](https://github.com/michaelma-ai/portfolio/blob/main/central/README.md#evaluation-strategy--results)), 9 in the harness or golden set, and 2 in both. The 157 product cases took 1 h 41 min of agent time and cost \$0 (\$4.37 pro forma):

</div>

<table>
<tbody>
<tr>
<td valign="top"><img src="assets/hill_climb_long_term.png" alt="Pass rate across all six components" width="100%"></td>
</tr>
</tbody>
</table>

<div align="justify">

**Hill climb by component:** all five product components reached 100% between late July and 2026-09-02: Model Selection from 0%, Central Assistant from 47%, Safety from 53%, Knowledge Base from 67% and Personalization from 81%. The judge audit rose from 75% to 94%; its three misses are cases where the judge disagreed with my label.

</div>

<table>
<tbody>
<tr>
<td width="50%" valign="top"><img src="assets/hill_climb_model_selection.png" width="100%"><br>
① 08-02: scorer rebuilt to grade all 34 cases. ② 08-09: product improvements ③ 09-02: three supervisor prompt adjustments, alongside eval harness capture fixes for context and safety gate.</td>
<td width="50%" valign="top"><img src="assets/hill_climb_central_assistant.png" width="100%"><br>
① 08-11: router prompt fixed for approval turns. ② 08-21: classifier failed under provider load. ③ 08-22: fixed with classifier retry logic.</td>
</tr>
</tbody>
<tbody>
<tr>
<td width="50%" valign="top"><img src="assets/hill_climb_knowledge_base.png" width="100%"><br>
① 08-06: all 30 cases scored; judge call failures had limited earlier runs to 6–7. ② 08-10: prompt adjustment makes it answer what the KB covers and decline the missing fact, fixing the last two.</td>
<td width="50%" valign="top"><img src="assets/hill_climb_personalisation.png" width="100%"><br>
① 08-11: short facts such as name are now saved. ② 08-22: reply-style rules fixed 3 tone failures. ③ 09-02: facts about people in user's life are now saved improving personalization.</td>
</tr>
</tbody>
<tbody>
<tr>
<td width="50%" valign="top"><img src="assets/hill_climb_safety.png" width="100%"><br>
① 07-30: full served gate scored, not just regex filter ② 08-10: adjustments support edge cases with "caution" ③ 09-02: refusal check reads markdown replies, so price-fixing refusal counts.</td>
<td width="50%" valign="top"><img src="assets/hill_climb_eval_harness.png" width="100%"><br>
① 08-09: all cases scored ② 08-11: new judge model swapped in due to prior model being unreliable ③ 09-02: judge improvements led to 94% observed judge-human alignment</td>
</tr>
</tbody>
</table>

## What I Learned
<div align="justify">

1. **Measuring the agent was harder than building it, so suspect the harness before the product metric:** 11 of 42 failures were the harness, golden set or rubric. Key takeaway is that every run records the judge, rubric and capture version it used, and a metric move counts as product progress only when those stayed the same.
2. **Human labeling is the bottleneck so design for it:** Every new golden case, every judge calibration, every rubric tweak eventually needs a human label. Build the labeling UI first (even a simple spreadsheet with dropdowns), not last is ideal because the faster you can get a human to say "pass/fail" on a new case, the faster your eval loop turns.
3. **Provider flakiness looks like product regressions:** On free tiers, rate limits, timeouts and changing search tool results fail cases the agent would otherwise pass. The fixes were in how errors are handled: retries with backoff, a single classifier retry before a model is blocked, and a third verdict, "undecided", so a provider or judge failure is never counted as a product failure.
4. **The harness is living documentation that demands hygiene:** Treat each golden case as a version‑controlled artifact with rationale, source, and requirement tag; run a regular “set health” job to prune duplicates, flag never‑failing cases, and review outdated expectations. When the harness is kept clean, its output becomes a clear communication artifact and a one‑page summary of pass/fail, κ, coverage, and top failure modes that reviewers can read at a glance.

</div>

## Next Steps and Future Roadmap
<div align="justify">

1. **Live Evals:** Monitor production outputs to catch regressions in real time; 53 of the 137 metric specs have thresholds but need live traffic. Sample conversations from the Phoenix traces Central already emits, score them with the offline checks and judge, and alert on drift. This catches changes from outside the codebase, such as a provider changing a model's behavior, within hours rather than at the next full run.
2. **Long-Running Task Evals:** Cover long-horizon tasks such as extensive report generation, morning brief, or long-running research turned into a document, sheet and deck. The golden set's longest case makes three tool calls. Long tasks run for minutes/hours and can fail at any step while the final message still reads well, so the eval must score the trajectory: whether each step was needed, failures were reported, and the artifacts match the request.
3. **Autonomous Spec & Golden Dataset Creation:** Build automated pipelines that ingest product requirements and generate verified golden datasets, shortening the time from a new feature to a measured one. This addresses Golden Set Drift vs. Maintenance Burden: a fixed 211-case set falls behind as Central gains new capabilities, and writing cases by hand is fairly manual.
4. **Repeat each case to measure consistency:** Pass^k (the case passes on all of k runs, k = 5) is specified as a P1 metric but not measured, because each case runs once. Agents on free-tier models vary between identical calls, and with 3–15 cases per P0 metric, one unlucky run moves a metric by up to 33 points. Repeated runs would separate a flaky pass from a reliable one.

</div>

## Built With
| Domain                  | Stack                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| -------------------------| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Harness runtime**     | ![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white&ext=.svg) ![OpenAI SDK](https://img.shields.io/badge/OpenAI%20SDK-OpenAI--compatible%20client-412991?style=flat-square&logo=openai&logoColor=white) ![LlamaIndex](https://img.shields.io/badge/LlamaIndex-model--selection%20client-8b5cf6?style=flat-square) ![openpyxl](https://img.shields.io/badge/openpyxl-golden%20workbook%20extraction-1a7f5a?style=flat-square) ![NumPy](https://img.shields.io/badge/NumPy-metric%20math-013243?style=flat-square&logo=numpy&logoColor=white)                                                                                                                                                                                                                         |
| **Inference providers** | ![NVIDIA NIM](https://img.shields.io/badge/NVIDIA%20NIM-judge%20%2B%20model%20endpoints-76B900?style=flat-square&logo=nvidia&logoColor=white) ![Gemini API](https://img.shields.io/badge/Google-Gemini%20API-4285F4?style=flat-square&logo=google&logoColor=white)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **Models**              | ![Judges](https://img.shields.io/badge/Judges-Mistral%20Nemotron%20·%20DeepSeek%20V4%20Flash-76B900?style=flat-square&logo=nvidia&logoColor=white)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **Observability**       | ![Arize Phoenix](https://img.shields.io/badge/Arize%20Phoenix-tracing-b45309?style=flat-square) ![OpenTelemetry](https://img.shields.io/badge/OpenTelemetry-spans-6e7681?style=flat-square&logo=opentelemetry&logoColor=white)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| **Dashboard**           | ![Next.js](https://img.shields.io/badge/Next.js-15-6e7681?style=flat-square&logo=nextdotjs&logoColor=white&ext=.svg) ![React](https://img.shields.io/badge/React-19-61DAFB?style=flat-square&logo=react&logoColor=black) ![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?style=flat-square&logo=typescript&logoColor=white) ![Tailwind](https://img.shields.io/badge/Tailwind-4-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white) ![Design system](https://img.shields.io/badge/%40portfolio%2Fdesign--system-shared%20tokens-0b3d91?style=flat-square)                                                                                                                                                                                                                                             |
| **Testing & delivery**  | ![pytest](https://img.shields.io/badge/pytest-passing-0A9EDC?style=flat-square&logo=pytest&logoColor=white) ![Make](https://img.shields.io/badge/Make-release--check-427819?style=flat-square&logo=gnu&logoColor=white) ![Vercel](https://img.shields.io/badge/Vercel-static%20demo-6e7681?style=flat-square&logo=vercel&logoColor=white)                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **Other Artifacts**     | ![Golden dataset](https://img.shields.io/badge/Golden%20dataset-211%20cases-b45309?style=flat-square) ![Metric specs](https://img.shields.io/badge/Metric%20specs-137%20·%2074%20P0-b45309?style=flat-square) ![LLM-as-judge](https://img.shields.io/badge/LLM--as--judge-validated%20vs%20human%20labels-b45309?style=flat-square) ![Cohen's κ](https://img.shields.io/badge/Cohen's%20κ-hand--rolled%2C%20degenerate--aware-b45309?style=flat-square) ![Release gating](https://img.shields.io/badge/Release%20gating-P0%20%2B%20coverage%20%2B%20κ-0b3d91?style=flat-square) ![Eval design](https://img.shields.io/badge/Eval%20design-metric%20trees%20%2B%20north%20stars-0b3d91?style=flat-square) ![Unit economics](https://img.shields.io/badge/Unit%20economics-pro%20forma%20cost%2Frun-0b3d91?style=flat-square) |
