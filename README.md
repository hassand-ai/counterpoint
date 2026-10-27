# Counterpoint

Counterpoint is a debate coach for practising clear, respectful arguments about everyday, low-stakes ethical questions. Choose a prompt, take a side, debate an AI opponent for three turns, and receive rubric-based feedback from a separate judge.

## What it does

- Lets you choose a hand-reviewed question or generate a fresh, balanced everyday dilemma with Claude in live mode.
- Gives you three turns with an AI opponent.
- Scores both speakers on claim clarity, relevant support, rebuttal, consistency, and respectful communication. In hosted multi-model mode, three independent models score the debate and the app combines their rubric scores using the median.
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

## Live modes

The Claude Artifact mode uses Claude to generate a question, debate as the opponent, and give final remarks. It does not call other model providers.

The hosted multi-model mode uses an OpenRouter-backed Vercel Function. It generates questions, screens user questions, rotates models for opponent turns, and sends the final transcript to three independent model judges. Counterpoint uses the median rubric scores and shows which models took part. Model IDs can be changed in `OPENROUTER_MODELS`.

To enable multi-model mode:

1. Deploy this repository to Vercel.
2. Create an OpenRouter API key and add it as the `OPENROUTER_API_KEY` environment variable in Vercel. Never put the key in the HTML or commit it to GitHub.
3. Set `OPENROUTER_MODELS` to three or more OpenRouter model IDs. The defaults are example model IDs and may need to be changed to models enabled for your account.
4. Redeploy after setting the variables. Model calls use your OpenRouter account and may incur provider charges; set an account spending limit before sharing the site.

The standalone `index.html` uses demo mode unless it is served from a deployment with the API configured. The `artifact-source.html` file retains Claude Artifact support. The artifact database stores reports under `reports/<viewer id>/items`; outside that environment, reports are saved to the browser's local storage.

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
- `api/ensemble.js` - Vercel endpoint for OpenRouter model calls and combined judging.
- `.env.example` - environment variable names for hosted multi-model mode.
- `README.md` - setup and project notes.

## Feedback and limitations

The judge sees speakers as Debater A and Debater B and uses a fixed five-part rubric. The page calculates the result from rubric totals; it is not selected by the model. Quoted excerpts are checked against the transcript, while factual claims remain unverified.

AI-generated questions are not hand-reviewed and can occasionally miss the intended scope; a keyword check blocks some sensitive areas. AI judging can be inconsistent, and anonymisation and bias instructions do not remove all bias. Custom-question screening uses a keyword filter and one model check, so it can make mistakes. Live provider calls require valid OpenRouter access and have not been exercised end to end during development. The API endpoint should be protected by provider spending limits before the site is shared.

For a different AI provider, route requests through your own backend. **Do not put API keys in the HTML page.**

## Research and design notes

- [WCAG 2.2 changes](https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/) informed focus visibility and target sizing.
- [Zheng et al. (2023)](https://arxiv.org/abs/2306.05685) document position, verbosity, and self-enhancement biases in language-model judges.
- The [Google PAIR Guidebook](https://pair.withgoogle.com/chapter/People%20+%20AI%20Guidebook%20-%20Explainability%20+%20Trust.pdf) informed how the interface explains what feedback is based on.
- [Amershi et al. (CHI 2019)](https://www.microsoft.com/en-us/research/publication/guidelines-for-human-ai-interaction/) provide validated human-AI interaction guidelines.
- [Casado-Ledesma et al. (2021)](https://pure.uva.nl/ws/files/67938146/Casado_Ledesma2021_Article_TeachingArgumentativeSynthesis.pdf) studied how deliberative dialogue can help students integrate arguments and counterarguments.

These findings informed anonymised judging, a fixed rubric, visible demo-mode labels, 44 px controls, and using both labels and shapes to distinguish speakers.
