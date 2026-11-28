# Flexylen Consultation Scribe

A single web page that turns a consultation transcript into structured clinical notes you can paste into EMIS Web or SystmOne.

## Using it

1. Open `index.html` in a browser (or host it, e.g. on GitHub Pages).
2. Under **Settings**, paste an Anthropic API key. It is kept only in your browser, and only saved if you tick "Remember".
3. Paste or upload the transcript (`.txt`, `.vtt` or `.srt`), choose the note format, consultation type and clinician role, and press **Generate notes**.
4. Review and edit each section, then **Copy all for EMIS**.

Note formats: EMIS/SystmOne headings, SOAP, Structured Medication Review, or Brief.

No API key? **Copy prompt instead** copies the instructions and transcript for use in another AI tool your practice has approved; paste the result back to split it into sections.

## Learning from a consultation (optional)

After the notes are generated, **Generate learning** creates, separately from the clinical record:

- a **CPD reflection** in Flexylen PracticeLab's four fields (What did I learn? / What knowledge did I update? / What will I do differently? / How will I evidence impact?), editable before export;
- **knowledge-gap prompts** linked to what happened, each naming the guidance to read;
- an **anonymised teaching case** with three single-best-answer questions.

The learning is written without patient identifiers (age as a decade band, no names, dates or places). The clinician should still check it before sharing.

**Send to PracticeLab** opens `<PracticeLab address>/import#flexylen-learning=<data>`. The address defaults to https://askmerx79.github.io/casecraft-practice-pathways and can be changed under Settings. The data sits in the URL fragment, which browsers never send to a server, and PracticeLab saves it in the learner's own browser. **Copy reflection** and **Download (.json)** are alternatives.

### Learning package format

`<data>` is the package as JSON, UTF-8 encoded, then base64url-encoded without padding:

```json
{
  "source": "flexylen-scribe",
  "version": 1,
  "createdAt": "2026-11-27T21:30:00.000Z",
  "consultationType": "face to face",
  "clinicianRole": "Clinical pharmacist (independent prescriber)",
  "title": "Persistent cough in an ex-smoker",
  "tags": ["Respiratory"],
  "cpd": { "learned": "…", "knowledge": "…", "differently": "…", "evidence": "…" },
  "knowledgeGaps": [{ "topic": "…", "why": "…", "guidance": "NICE NG12" }],
  "teachingCase": {
    "title": "…", "setting": "…", "vignette": "…",
    "questions": [{ "question": "…", "options": ["A", "B", "C", "D"], "answer": 1, "explanation": "…" }]
  }
}
```

## Safety and information governance

- The notes are an AI draft. The responsible clinician must check every line before it enters the record. A separate **For clinician to check** box lists gaps and likely transcription errors and is never copied into the notes.
- The transcript is sent from the browser to the Anthropic API. Get practice approval (DPIA / IG sign-off) before using it with real patient data.
- **Remove identifiers** (on by default) strips NHS numbers, dates of birth, phone numbers, emails and postcodes before sending. It only catches these patterns: names and addresses spoken in the consultation are not removed.

## Technical notes

- Plain HTML and JavaScript, no build step. The Anthropic TypeScript SDK (`@anthropic-ai/sdk` 1.8.0) loads from the jsDelivr CDN; fonts load from Google Fonts.
- Default model: Claude Opus 5.5, with Claude Sonnet 5.5 as a faster option. Requests use adaptive thinking, streaming and server-side refusal fallbacks.
