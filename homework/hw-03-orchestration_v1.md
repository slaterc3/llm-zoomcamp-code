# Christopher Guy Slater
## 2026-06-29
## Homework: Vector Search

## Question 1: Context Engineering

After trying the same prompt in ChatGPT vs Kestra's AI Copilot, what is the primary reason AI Copilot generates better Kestra flows?

- AI Copilot uses a more powerful model
- ✅ AI Copilot has access to current Kestra plugin documentation ✅
- AI Copilot uses more tokens
- AI Copilot has internet access

### **Q1 Explanation**

![chatgpt-vs-kestra](.\screenshots\3-01-comparison.png)

The Kestra Copilot's output is noticeably slimmer than the ChatGPT counterpart. The reason for this is that the Kestra AI Copilot has the massive advantage of access to the updated documentation, which is essentially RAG in action. ChatGPT, on the other hand, is only aware of outdated information.

## Question 2: RAG vs No RAG

The non-RAG response about Kestra 1.1 features is best described as:

- Accurate and specific, matching the actual release notes
- ✅ Vague, generic, or fabricated — the model guesses from training data ✅
- Empty — the model refuses to answer without context
- Identical to the RAG version

### **Q2 Explanation**
The non-RAG response is a classic example of an LLM hallucination. Because the model doesn't have Kestra 1.1's specific release notes in its pre-trained weights, it attempts to fulfill the prompt by statistically guessing and generating a list of plausible, generic data orchestration features (like "Folders" or "Retries"). It presents these fabricated guesses with absolute confidence, which is why the homework's execution log explicitly calls out the output as incorrect and vague when compared to the fact-based, context-grounded RAG version.
## Question 3: Token usage — short summary

What is the approximate **output** token count for `multilingual_agent`?

- 5-15 tokens
- ✅ 60-100 tokens ✅
- 200-400 tokens 
- 500+ tokens

### **Q3 Explanation**
![log_token_usage](screenshots\3-03-tokens.png)

For the Multilingual Agent, we can see a total of 123 output tokens. Therefore a range of 60-100 tokens is a more appropriate than the next largest option - 200-400 tokens.

## Question 4: Token usage — long summary

Run `4_simple_agent.yaml` again with `summary_length = long`.

Compare the `multilingual_agent` output token count to your result from Question 3. Roughly how many times more output tokens does the long summary use?

- About the same (within 20%)
- 2-5x more
- 10-20x more
- 50x more

## Question 5: Modifying a flow

Open `4_simple_agent.yaml` in the Kestra flow editor. Find the `english_brevity` task and change its prompt from asking for exactly **1 sentence** to asking for exactly **3 sentences**.

Save the flow, then run it with `summary_length = long`.

Compare the `english_brevity` output token count to the original 1-sentence version (also with `summary_length = long`). How do they compare?

- About the same (within 20%)
- 2-4x more
- 5-10x more
- 10x+ more

## Question 6: Best Practices

Based on what you learned in this module, for production workflows requiring deterministic, repeatable results with strict compliance requirements (e.g., financial reporting, workflows in highly regulated industries), which approach is most appropriate?

- Always use AI agents for maximum flexibility and adaptation
- Use traditional task-based workflows for predictability and auditability
- Use only RAG without agents for better performance
- Use web search tools exclusively to ensure current data