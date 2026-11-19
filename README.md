<div align="center">

# 🎓 AZ-104 Exam Simulator

### Microsoft Azure Administrator · 214 questions · 2 case studies · runs entirely in the browser

[![Live site](https://img.shields.io/badge/%E2%96%B6%20Take%20the%20exam-chrismunnps.github.io-0d9488?style=for-the-badge)](https://chrismunnps.github.io/exam-sim/az-104/)
[![Questions](https://img.shields.io/badge/questions-214-2563eb?style=for-the-badge)](#-question-bank)
[![Pass mark](https://img.shields.io/badge/pass%20mark-700%20%2F%201000-7c3aed?style=for-the-badge)](#-scoring-and-review)
[![No account](https://img.shields.io/badge/signup-not%20required-16a34a?style=for-the-badge)](#-privacy)

**▶ Live exam: https://chrismunnps.github.io/exam-sim/az-104/**

</div>

---

## 🚀 Executive summary

A free, full-length practice exam for **Exam AZ-104: Microsoft Azure Administrator**, built from the Microsoft Learn AZ-104 learning paths. It recreates the real sitting closely enough to be useful: a weighted 50-question draw, a 100-minute clock, scaled scoring against the 700 pass mark, two multi-question case studies, and twelve architecture diagrams.

Everything runs client-side. There is no account, no install, no server, no analytics, and no network call after the page loads — scores and review history stay in the visitor's own browser.

The same seven files power both the repository and the live site, so what you test locally is exactly what is published:

> **🌐 Live site: https://chrismunnps.github.io/exam-sim/az-104/**
> Served by GitHub Pages from the portfolio repo's `exam-sim/az-104/` folder — the same seven runtime files in this repository are the deployed build.

It is an independent study tool. It is not affiliated with, endorsed by, or connected to Microsoft.

---

## ✨ Why this one is different

🧭 **It behaves like the real exam.** Fifty questions drawn by domain weight, 120 seconds each, a running clock, flag-for-review, a question palette, and submit-on-timeout. No "practice mode" shortcuts unless you ask for them.

🧠 **Explanations teach, not just score.** Every item carries a rationale, one sentence on each wrong option, an exam tip, and the Microsoft Learn module it came from.

🧩 **Two case studies.** Fourteen questions hang off shared scenarios — Contoso Retail's cloud migration and Fabrikam's global expansion — so you practise holding context across a case, the way the real exam does.

🖼️ **Real topology, described twice.** Twelve inline SVG diagrams cover hub-and-spoke, NSGs, load balancing, Application Gateway, private endpoints, VPN, Front Door, scale sets, storage redundancy, UDRs, Bastion, and private DNS. Every diagram item also explains the topology in words, so it survives a broken image or a screen reader.

✅ **Multi-answer items are all-or-nothing.** Thirty-two "choose two/three" questions are graded exactly as strictly as the exam grades them.

🔁 **A spaced-review queue.** Wrong answers come back on a schedule instead of disappearing, so weak domains get revisited without re-running whole exams.

🌓 **Light and dark themes.** A top-bar switch, remembered per browser, following the OS until you choose. Printing always forces black-on-white.

⌨️ **Keyboard-first.** `A`–`E` to answer, arrows to navigate, `F` to flag, plus a visible focus ring and a skip link.

📴 **Genuinely offline.** Double-click the page and it works with the network unplugged. No bundler, no local server, no runtime dependencies.

---

## 🎯 Exam at a glance

| | |
|---|---|
| **Certification** | Microsoft Certified: Azure Administrator Associate |
| **Exam** | AZ-104 — Microsoft Azure Administrator |
| **Questions in bank** | 214 (200 standalone + 14 case-study) |
| **Default attempt** | 50 questions, weighted by domain |
| **Time limit** | 120 seconds per question — 100 minutes for the default draw |
| **Passing score** | 700 / 1,000 (scaled from percentage correct) |
| **Review** | Every question after submission, with explanations and sources |
| **Prerequisites** | A browser. That is the whole list. |

---

## 📚 Question bank

| Domain | Questions | Weight | IDs |
|---|---:|---|---|
| Identities and governance | 42 | 20–25% | Q1–Q40, Q204, Q206 |
| Storage | 37 | 15–20% | Q41–Q75, Q203, Q211 |
| Compute | 52 | 20–25% | Q76–Q125, Q201, Q212 |
| Virtual networking | 49 | 15–20% | Q126–Q170, Q202, Q208–Q210 |
| Monitoring and backup | 34 | 10–15% | Q171–Q200, Q205, Q207, Q213, Q214 |
| **Total** | **214** | | |

**Question mix:** 60% scenario-based, 25% least-administrative-effort, 15% multi-answer.

### 🗂️ Case studies

| Case | Questions | Scenarios |
|---|---|---|
| Contoso Retail Cloud Migration | Q201–Q207 | Availability zones, VPN gateway, storage redundancy, Azure RBAC, VM backup, Azure Policy, Azure Monitor |
| Fabrikam Global Expansion | Q208–Q214 | Front Door, Application Gateway, Load Balancer, GRS storage, VM scale sets, Azure Backup, cross-region monitoring |

---

## 🖥️ Run it locally

**The short version:** download the repository and double-click `index.html`.

```powershell
git clone https://github.com/ChrisMunnPS/AZ104Exam.git
```

```text
index.html        – page shell, theme bootstrap
exam.config.js    – pass mark, domains, weights, timer
questions.js      – 214 compiled questions (the whole bank)
diagrams.js       – 12 inline SVG diagrams
app.js            – exam engine
styles.css        – shared theme, light + dark
md-loader.js      – data bootstrap and zero-setup fallback
```

No `npm install`. No build step. No web server. The page opens from `file://`, reads the compiled bank through a plain `<script>` tag, and starts.

> 💡 **Why not Markdown at runtime?** A double-clicked page is not allowed to `fetch` other local files — that rule applies to every `file://` URL. Compiling the Markdown into `questions.js` once is what makes the offline copy work with no setup prompt.

If you only want to check the bank without opening the UI, the authoring kit (not included in this repository) has a Node-free PowerShell validator: `tools\build-questions.ps1 -Check`.

---

## 🧮 Scoring and review

📊 **Scaled score.** `round(correct / total * 1000)` — the same shape Microsoft uses in the score report. Seven out of ten is a pass.

🎆 **Passing feels like passing.** Clearing 700 adds a five-second fireworks overlay to the score report. It is skipped automatically when the operating system asks for reduced motion, and never appears in print.

🔍 **Review everything.** After submitting, filter by all / incorrect / flagged. Each item shows your answer, the correct answer, the full explanation, and the source module.

📄 **Print or save.** "Print full review" and "Print score report" produce clean paper layouts with the light palette forced, so a dark-mode session still prints readable black text.

💾 **Resume safe.** An in-progress attempt is saved on every change. Close the tab, come back, and the clock and answers are where you left them.

---

## 🔐 Privacy

Everything is local. The simulator writes four keys to `localStorage` and nothing else:

| Key | Holds |
|---|---|
| `az104-sim:attempt` | The in-progress attempt, for resume |
| `az104-sim:settings` | Draw size, timer, instant-feedback preference |
| `az104-sim:srs` | Spaced-review scheduling data |
| `az104-sim:theme` | Light or dark choice |

No cookies, no requests to third parties, no telemetry. Clearing site data for the page resets the simulator completely.

---

## ♿ Accessibility

- Full keyboard operation, including `A`–`E` (or `1`–`5`) answer keys and arrow navigation.
- Semantic landmarks, a skip link, and a polite live region for toasts.
- Visible focus rings and readable contrast in both themes.
- `prefers-reduced-motion` disables the fireworks and shortens transitions.
- Diagrams are hidden from assistive technology; every diagram question restates the topology in prose.

---

## ⚠️ Disclaimer

Unofficial study material built from publicly available Microsoft Learn content. Not affiliated with or endorsed by Microsoft. Exam objectives and product behaviour change; verify the current skills measured on Microsoft Learn before sitting the exam. Facts snapshot: **November 2026**.

<div align="center">

**[▶ Start practising](https://chrismunnps.github.io/exam-sim/az-104/)** · [Microsoft Learn AZ-104](https://learn.microsoft.com/en-us/credentials/certifications/azure-administrator/)

</div>
