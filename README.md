# AI Product Manager Portfolio
## Overview:
<table width="100%">
  <thead>
  <tr>
    <th width="24%">Project</th>
    <th width="48%">Problem and Solution</th>
    <th width="28%">Built With</th>
  </tr>
  </thead>
  <tbody>
  <tr>
    <td width="24%" valign="top"><b><a href="central/README.md">Central</a></b><br>Personal AI assistant harness for knowledge work<br><br><a href="https://michaelma-central.vercel.app/"><img src="https://img.shields.io/badge/Try%20the%20demo-1a7f5a?style=flat-square" alt="Central demo"></a> <a href="central/README.md"><img src="https://img.shields.io/badge/README-0b3d91?style=flat-square" alt="Central README"></a></td>
    <td width="48%" valign="top"><b>Problem:</b> Paid AI plans cap usage, and knowledge work is scattered across email, calendar, documents and the web.<br><br><b>Solution:</b> One agentic assistant at &#36;0 inference cost that:<ul><li>works across 37 tools</li><li>shows what it will do first</li><li>remembers the user in a readable profile</li><li>blocks harmful requests without over-refusing</li><li>keeps working when a provider fails</li></ul></td>
    <td width="28%" valign="top"><img src="https://img.shields.io/badge/LangGraph-supervisor%20%2B%20workers-1a7f5a?style=flat-square" alt="LangGraph"> <img src="https://img.shields.io/badge/LlamaIndex-Qdrant%20Cloud%20vector%20store-1a7f5a?style=flat-square" alt="LlamaIndex"> <img src="https://img.shields.io/badge/Google%20Workspace-6%20apps-1a7f5a?style=flat-square&logo=google&logoColor=white" alt="Google Workspace"> <img src="https://img.shields.io/badge/Web-search%20·%20Fetch%20MCP%20·%20YouTube-1a7f5a?style=flat-square" alt="Web"> <img src="https://img.shields.io/badge/Hybrid%20RAG-Gemini%20embeddings%20%2B%20BM25-1a7f5a?style=flat-square" alt="Hybrid RAG"> <img src="https://img.shields.io/badge/Safety%20gate-Nemotron%20content%20safety-1a7f5a?style=flat-square" alt="Safety gate"> <img src="https://img.shields.io/badge/Inference-Gemini%20API%20·%20NVIDIA%20NIM-1a7f5a?style=flat-square" alt="Inference"></td>
  </tr>
  </tbody>
  <tbody>
  <tr>
    <td width="24%" valign="top"><b><a href="eval_harness/README.md">Eval Harness</a></b><br>Offline evals and release gate for Central<br><br><a href="https://michaelma-eval-harness.vercel.app/"><img src="https://img.shields.io/badge/Try%20the%20demo-1a7f5a?style=flat-square" alt="Eval Harness demo"></a> <a href="eval_harness/README.md"><img src="https://img.shields.io/badge/README-0b3d91?style=flat-square" alt="Eval Harness README"></a></td>
    <td width="48%" valign="top"><b>Problem:</b> Any change can break an agent, and small teams often ship after spot-checks because agent evals are hard to build.<br><br><b>Solution:</b> A working example of agent evals on a real product that:<ul><li>scores Central's outputs against 211 golden cases</li><li>trusts the judge only once it matches human labels</li><li>gives one release verdict</li></ul></td>
    <td width="28%" valign="top"><img src="https://img.shields.io/badge/Golden%20dataset-211%20cases-b45309?style=flat-square" alt="Golden dataset"> <img src="https://img.shields.io/badge/LLM--as--judge-validated%20vs%20human%20labels-b45309?style=flat-square" alt="LLM-as-judge"> <img src="https://img.shields.io/badge/Cohen's%20κ-hand--rolled%2C%20degenerate--aware-b45309?style=flat-square" alt="Cohen's κ"> <img src="https://img.shields.io/badge/Release%20gating-P0%20%2B%20coverage%20%2B%20κ-b45309?style=flat-square" alt="Release gating"> <img src="https://img.shields.io/badge/Judges-2%20models%20on%20NVIDIA%20NIM-b45309?style=flat-square&logo=nvidia&logoColor=white" alt="Judges"> <img src="https://img.shields.io/badge/Arize%20Phoenix-tracing-b45309?style=flat-square" alt="Arize Phoenix"></td>
  </tr>
  </tbody>
</table>


## Demo Preview:
**[Central](https://michaelma-central.vercel.app/)**

<a href="https://michaelma-central.vercel.app/"><img src="assets/central-first-screen.png" alt="Central first screen" width="100%"></a>

**[Eval Harness](https://michaelma-eval-harness.vercel.app/)**

<a href="https://michaelma-eval-harness.vercel.app/"><img src="assets/eval-harness-home.png" alt="Eval Harness home page" width="100%"></a>

## Connect
[![LinkedIn](https://img.shields.io/badge/LinkedIn-michaelcma-0A66C2?style=flat-square&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI%2BPHBhdGggZmlsbD0id2hpdGUiIGQ9Ik0yMC40NDcgMjAuNDUyaC0zLjU1NHYtNS41NjljMC0xLjMyOC0uMDI3LTMuMDM3LTEuODUyLTMuMDM3LTEuODUzIDAtMi4xMzYgMS40NDUtMi4xMzYgMi45Mzl2NS42NjdIOS4zNTFWOWgzLjQxNHYxLjU2MWguMDQ2Yy40NzctLjkgMS42MzctMS44NSAzLjM3LTEuODUgMy42MDEgMCA0LjI2NyAyLjM3IDQuMjY3IDUuNDU1djYuMjg2ek01LjMzNyA3LjQzM2MtMS4xNDQgMC0yLjA2My0uOTI2LTIuMDYzLTIuMDY1IDAtMS4xMzguOTItMi4wNjMgMi4wNjMtMi4wNjMgMS4xNCAwIDIuMDY0LjkyNSAyLjA2NCAyLjA2MyAwIDEuMTM5LS45MjUgMi4wNjUtMi4wNjQgMi4wNjV6bTEuNzgyIDEzLjAxOUgzLjU1NVY5aDMuNTY0djExLjQ1MnpNMjIuMjI1IDBIMS43NzFDLjc5MiAwIDAgLjc3NCAwIDEuNzI5djIwLjU0MkMwIDIzLjIyNy43OTIgMjQgMS43NzEgMjRoMjAuNDUxQzIzLjIgMjQgMjQgMjMuMjI3IDI0IDIyLjI3MVYxLjcyOUMyNCAuNzc0IDIzLjIgMCAyMi4yMjIgMGguMDAzeiIvPjwvc3ZnPg%3D%3D)](https://linkedin.com/in/michaelcma) [![GitHub](https://img.shields.io/badge/GitHub-michaelma--ai-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/michaelma-ai) [![Email](https://img.shields.io/badge/Email-ma.michael.sf%40gmail.com-0b3d91?style=flat-square&logo=gmail&logoColor=white&ext=.svg)](mailto:ma.michael.sf@gmail.com)
