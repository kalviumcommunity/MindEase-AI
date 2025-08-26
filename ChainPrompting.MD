🧩 Chain-of-Thought (CoT) Prompting
What it is

Chain-of-Thought prompting asks the model to reason step-by-step before giving an answer. It improves performance on tasks that benefit from decomposition (reasoning, classification with subtle cues, planning).

⚠️ Best practice: Don’t expose long internal reasoning to users. Ask the model to think step-by-step privately and output only a concise result or a brief justification. This keeps responses safe, readable, and cost-efficient.

When to use it in MindEase AI

Emotion triage with subtle signals (e.g., differentiating “nervousness” vs. “anxiety”).

Prioritizing coping strategies (weighing options given constraints like time, sleep, exams).

Action planning (building a short daily routine from user context).

Safe CoT Patterns (Policy-friendly)
1) Hidden Reasoning, Structured Output (recommended)

Ask the model to reason silently and return only JSON.

Prompt template

System: You are a careful mental wellness assistant. 
Think step by step privately. Do NOT reveal your reasoning.
Return only valid JSON that matches this schema:
{
  "emotion_detected": string,
  "intensity_level": "low" | "moderate" | "high",
  "suggested_strategies": string[3],
  "brief_explanation": string  // ≤ 20 words, no step-by-step details
}
User: "<user_message>"


Example output

{
  "emotion_detected": "nervousness",
  "intensity_level": "moderate",
  "suggested_strategies": [
    "Practice 4-7-8 breathing for 3 minutes",
    "Do a 20-minute focused study block",
    "Prepare tomorrow’s checklist now"
  ],
  "brief_explanation": "Language indicates anticipatory worry about a near-term event."
}

2) Plan-then-Answer (concise, visible rationale)

Keep the “plan” to short bullets; avoid detailed CoT.

Prompt template

System: Create a 1–3 bullet plan, then give the answer.
Keep the plan high-level (no internal steps). 
Output:
- Plan: (≤3 bullets)
- Answer: JSON
User: "<user_message>"


Example output

Plan:
- Identify mood and intensity
- Pick 3 short, evidence-informed strategies
- Add a brief explanation

Answer:
{
  "emotion_detected": "stress",
  "intensity_level": "high",
  "suggested_strategies": [
    "Box breathing for 2 minutes",
    "Schedule a 10-minute walk break",
    "Write top 3 priorities for today"
  ],
  "brief_explanation": "Overload cues and urgency terms suggest high stress."
}

3) Self-Consistency (internal)

Ask the model to sample a few internal reasonings and choose the majority/most consistent answer—but only return the final result.

Prompt cue (add to system prompt)

Privately sample multiple solution paths and select the most consistent result.
Do not reveal intermediate reasoning. Output final JSON only.

Guardrails & Tips

Cap rationale length: e.g., “brief_explanation ≤ 20 words.”

Schema-first: Validate JSON to prevent leaks of internal steps.

Token budget: CoT increases tokens; keep prompts lean and outputs concise.

Privacy: Never echo user PII inside hidden reasoning or logs.

Determinism: Lower temperature (e.g., 0.2–0.4) for stable triage; raise slightly when brainstorming strategies.

Suggested Tuning (MindEase defaults)
Parameter	Value	Rationale
temperature	0.3	Stable emotion classification
top_p	0.9	Some diversity for strategies
max_tokens	350	Tight, cost-aware responses
frequency_penalty	0.2	Less repetition
presence_penalty	0.1	Mild novelty in tips
Function Calling + CoT (example)

Use hidden reasoning to decide which tool to call; return only the tool result.

Prompt cue

Decide privately if a tool is needed. 
If high anxiety, call start_meditation_session(duration_minutes:int, type:string). 
Return merged JSON only.


Possible final JSON

{
  "emotion_detected": "anxiety",
  "intensity_level": "high",
  "action_triggered": {
    "tool": "start_meditation_session",
    "args": { "duration_minutes": 5, "type": "guided_breathing" }
  },
  "suggested_strategies": [
    "Guided breathing just started",
    "Reduce evening caffeine",
    "Prepare tomorrow’s checklist"
  ],
  "brief_explanation": "Physical arousal cues suggest acute anxiety."
}

RAG + CoT (decision, not exposition)

Use CoT privately to decide which snippet is most relevant; cite briefly.

Final JSON example

{
  "emotion_detected": "panic",
  "intensity_level": "high",
  "suggested_strategies": [
    "5-4-3-2-1 grounding exercise",
    "Slow breathing: inhale 4, hold 7, exhale 8",
    "Move to a quieter space if possible"
  ],
  "source_note": "Derived from MindEase KB: CBT quick techniques"
}