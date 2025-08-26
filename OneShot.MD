🔹 One-Shot Prompting

In One-Shot Prompting, the model is given exactly one example before the real user query.

This helps guide the AI toward the desired structure or style, but with less token usage compared to multi-shot.

It’s a balance between zero-shot (no example) and multi-shot (many examples).

✅ Example

System Prompt:
"You are an empathetic AI assistant. Detect the user’s emotion and suggest coping strategies. Provide output in JSON."

One Example (training the model inside the prompt):
Input: "I feel lonely today."
Output:

{
  "emotion_detected": "loneliness",
  "strategies": ["Call a close friend", "Go for a walk in a public space"]
}


Now the real user query:
Input: "I feel nervous about my interview tomorrow."

AI Output:

{
  "emotion_detected": "nervousness",
  "strategies": ["Prepare common interview answers", "Practice deep breathing before the interview"]
}

🌟 Why One-Shot Prompting is useful in MindEase AI

Saves tokens compared to multi-shot.

Ensures structured output (JSON, markdown, etc.) by showing a single example.

Works well when you want just enough guidance for the AI without overloading it.