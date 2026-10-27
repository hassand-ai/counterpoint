# Counterpoint

An AI debate coach for practising clear, respectful arguments about everyday, low-stakes ethical questions.
Choose a question, pick a side, debate an AI opponent for three turns, then get rubric-based feedback from a separate judge.

## Files

| File | What it is |
|---|---|
| `index.html` | Standalone page. Open it directly in any modern browser. |
| `artifact-source.html` | The same app as published on claude.ai (no `<html>`/`<head>` wrapper; the platform adds it). |
| `README.md` | This file. |

## How to run

- **Standalone:** double-click `index.html`, or serve the folder (`python3 -m http.server`) and open `http://localhost:8000`.
  No build step or install. Fonts load from Google Fonts; system fonts are used if offline.
- **On claude.ai:** publish `artifact-source.html` as an Artifact with these capabilities:
  ```json
  { "sample": {}, "user": {},
    "db": { "rules": [
      { "path": "reports", "read": "admin", "write": "admin" },
      { "path": "reports/{self}", "read": "interact", "write": "interact" } ] } }
  ```

## Live vs. demo

- **Live mode** runs only inside a Claude Artifact viewer, where `window.claude.use("sample")` is available.
  The opponent, the custom-question check, and the judge are separate Claude calls on the viewer's own account
  (the viewer is asked to allow it on first use).
- **Demo mode** is used everywhere else, including when you open `index.html` locally. Opponent turns are pre-written
  and labelled; the assessment is labelled illustrative and is not computed from your text. Custom questions need live mode.
- To use a different AI provider outside Claude, replace the `S.sample(...)` / `S.sample.json(...)` calls in the script
  with requests to your own backend. Don't put API keys in the page.

## How judging works

- The judge sees the speakers only as "Debater A" and "Debater B", scored 1–5 on five criteria: clear claim; relevant
  reasons and examples; response to the other side; internal consistency; respectful, focused communication.
- Prompts tell the judge not to reward length, speaking order, or agreement with its own view.
- Win / loss / tie is computed in the page from rubric totals, not chosen by the model.
- Quoted excerpts are checked against the transcript; non-verbatim ones are marked.
- Factual claims are listed separately as unverified. Nothing in the app checks facts.

## Reports

"Report an unfair or unhelpful assessment" saves to the artifact database (`reports/<viewer id>/items`, readable by
the owner and editors). Where that isn't available (e.g. opened locally), it saves to the browser's localStorage only.

## Limitations

- Live mode was not exercised end to end during development; the demo flow was tested at desktop and phone widths.
- AI judging can be inconsistent; anonymisation and bias instructions reduce, but don't remove, bias.
- Custom-question screening (keyword filter plus a Claude check) can err in either direction.

## Research notes

Sourced findings:
- WCAG 2.2 adds Focus Not Obscured (2.4.11, AA) and Target Size Minimum 24×24 CSS px (2.5.8, AA). https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/
- LLM judges show position, verbosity and self-enhancement biases. Zheng et al., 2023. https://arxiv.org/abs/2306.05685
- AI products should help users calibrate trust and explain what an output rests on. Google PAIR Guidebook. https://pair.withgoogle.com/chapter/People%20+%20AI%20Guidebook%20-%20Explainability%20+%20Trust.pdf
- 18 validated human-AI interaction guidelines. Amershi et al., CHI 2019. https://www.microsoft.com/en-us/research/publication/guidelines-for-human-ai-interaction/
- Deliberative dialogue helped students integrate arguments and counterarguments. Casado-Ledesma et al., Instructional Science, 2021. https://pure.uva.nl/ws/files/67938146/Casado_Ledesma2021_Article_TeachingArgumentativeSynthesis.pdf

Design choices that follow from them: anonymised judging and a fixed rubric; a rubric criterion for rebuttal;
always-visible mode and labelled demo content; 44 px controls; docked composer with scroll padding; colour paired
with shapes (■ you, ● AI opponent).
