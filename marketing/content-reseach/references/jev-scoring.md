# Jev scoring for SGA source posts

TypeSafe's Jev accepts a `state` plus typed questions and returns structured answers. Its Score result is a probability-weighted position along the supplied levels, with a probability distribution and confidence. **Confidence reflects how concentrated that distribution is; it is not the chance that an Instagram adaptation will go viral.** Jev does not provide a built-in virality predictor. This rubric is SGA's editorial ranking method.

Current documentation: [introduction](https://docs.typesafe.ai/introduction), [Score](https://docs.typesafe.ai/primitives/score), [confidence](https://docs.typesafe.ai/confidence), and [quick start](https://docs.typesafe.ai/introduction/quickstart). Check these when the API or model details matter, since availability and schema may change.

## Input

Use the TypeSafe Playground in a signed-in browser or the documented API with a configured `TYPESAFE_API_KEY`. Do not expose the key in the brief. For each candidate, provide the same state fields: post text or slide synopsis; platform; date and observation time; visible likes, replies, reposts, views and saves where available; creator size where visible; target SGA audience; and proposed SGA angle. Mark missing data as missing, not zero. The cutoff below is an editorial starting point, not a calibrated forecast; revise it after comparing scores with actual SGA outcomes.

Ask five independent Score questions. For each, use the following 0–2 levels in order. Keep a question focused on one dimension.

| Question | 0 | 1 | 2 |
| --- | --- | --- | --- |
| Hook | Generic opening with no clear reason to keep reading | Clear problem or curiosity, but familiar | Specific tension or surprising insight that invites the next slide |
| Resonance | Little connection to a real audience problem | Recognizable concern for part of the audience | Common, concrete concern the SGA audience is likely to discuss |
| Share/save value | Little reason to revisit or send onward | One useful takeaway | Several actionable or conversation-starting takeaways |
| Carousel transfer | Meaning depends on the original format or creator | Can become a carousel with some restructuring | Clear visual, stepwise, or reveal-driven progression for slides |
| SGA fit | No credible brand or audience connection | Plausible connection requiring a careful angle | Direct, credible connection to SGA's audience and expertise |

Example question shape for one dimension:

```json
{
  "state": {
    "post": "Full source text or slide synopsis",
    "platform": "Threads",
    "observed_engagement": "Counts, creator context, date, and missing fields",
    "sga_audience": "Known audience context",
    "proposed_angle": "Distinct SGA adaptation"
  },
  "model": "jev-latest",
  "questions": {
    "hook": {
      "type": "score",
      "instructions": "How strongly does the source post open a curiosity gap or audience tension?",
      "criteria": [
        "Generic opening with no clear reason to keep reading",
        "Clear problem or curiosity, but familiar",
        "Specific tension or surprising insight that invites the next slide"
      ]
    }
  }
}
```

Add the other four questions using the table's level descriptions; TypeSafe evaluates them independently in one request. The documented endpoint is `POST https://api.typesafe.ai/v1/systemone` with a bearer key. A signed-in Playground can be used without building an integration.

## Decision

Calculate `opportunity = 100 × (hook + resonance + share/save value + carousel transfer) / 8`. Treat SGA fit as a separate gate of at least `1.5/2`. Report each raw score on its 0–2 scale and its confidence. If a dimension's confidence is low, inspect the source and state for missing context or ambiguous rubric levels before deciding. Keep the observed engagement evidence alongside the score; Jev's judgment does not replace it. Verify factual claims separately before drafting.
