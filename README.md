# Counterpoint

Counterpoint is a debate coach for practising clear, respectful arguments about everyday, low-stakes ethical questions. Choose a prompt, take a side, debate an AI opponent for three turns, and receive rubric-based feedback from a separate judge.

## What it does

- Lets you choose a question and argue either side.
- Gives you three turns with an AI opponent.
- Scores both speakers on claim clarity, relevant support, rebuttal, consistency, and respectful communication.
- Separates unverified factual claims from rubric feedback.
- Lets you report an unfair or unhelpful assessment.

## Run locally

There is no build step or package installation.

1. Open `index.html` in a modern browser, or serve the folder:

   ```bash
   python3 -m http.server 8000
   ```

2. Visit [http://localhost:8000](http://localhost:8000).

The standalone page uses demo mode. It works without an API key; the sample opponent turns are prewritten and the feedback is illustrative rather than generated from your responses. Fonts load from Google Fonts when online, with system-font fallbacks.

## Live mode

Live mode is available when `artifact-source.html` is published as a Claude Artifact. The viewer can authorize separate Claude calls for the opponent, custom-question screening, and judge. The artifact database stores reports under `reports/<viewer id>/items`; outside that environment, reports are saved to the browser's local storage.

Configure the Artifact with these capabilities:

```json
{
  "sample": {},
  "user": {},
  "db": {
    "rules": [
      { "path": "reports", "read": "admin", "write": "admin" },
      { "path": "reports/{self}", "read": "interact", "write": "interact" }
    ]
  }
}
```

## Files

- `index.html` - standalone page for local use.
- `artifact-source.html` - Artifact source; the platform supplies the outer HTML document.
- `README.md` - setup and project notes.

## Feedback and limitations

The judge sees speakers as Debater A and Debater B and uses a fixed five-part rubric. The page calculates the result from rubric totals; it is not selected by the model. Quoted excerpts are checked against the transcript, while factual claims remain unverified.

AI judging can be inconsistent, and anonymisation and bias instructions do not remove all bias. Custom-question screening uses both a keyword filter and a Claude check, so it can make mistakes. Live mode has not been exercised end to end during development.

For a different AI provider, route requests through your own backend. **Do not put API keys in the HTML page.**

## Research and design notes

- [WCAG 2.2 changes](https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/) informed focus visibility and target sizing.
- [Zheng et al. (2023)](https://arxiv.org/abs/2306.05685) document position, verbosity, and self-enhancement biases in language-model judges.
- The [Google PAIR Guidebook](https://pair.withgoogle.com/chapter/People%20+%20AI%20Guidebook%20-%20Explainability%20+%20Trust.pdf) informed how the interface explains what feedback is based on.
- [Amershi et al. (CHI 2019)](https://www.microsoft.com/en-us/research/publication/guidelines-for-human-ai-interaction/) provide validated human-AI interaction guidelines.
- [Casado-Ledesma et al. (2021)](https://pure.uva.nl/ws/files/67938146/Casado_Ledesma2021_Article_TeachingArgumentativeSynthesis.pdf) studied how deliberative dialogue can help students integrate arguments and counterarguments.

These findings informed anonymised judging, a fixed rubric, visible demo-mode labels, 44 px controls, and using both labels and shapes to distinguish speakers.
