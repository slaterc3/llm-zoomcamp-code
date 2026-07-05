## LLM Zoomcamp 2026 Homework: Vector Search
## Christopher Guy Slater
## 2026-06-29


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
![log_token_usage](screenshots\3-03-short-tokens.png)

For the Multilingual Agent, we can see a total of 45 output tokens for the `short` summary option. Therefore a range of 60-100 tokens is a much more appropriate than the next largest option - 200-400 tokens.

## Question 4: Token usage — long summary

Compare the `multilingual_agent` output token count to your result from Question 3. Roughly how many times more output tokens does the long summary use?

- About the same (within 20%)
- ✅ 2-5x more ✅
- 10-20x more
- 50x more

### **Q4 Explanation**
![short-vs-long](screenshots\3-04-compare-short-long.png)

In Q3, the short summary produced `45` ouput tokens. For the long summary, we see `184` output tokens, which is about *4x as many*.

$ \frac{184\texttt{ tokens}}{45\texttt{ tokens}} \approx{4\times}\texttt{ as many} $

## Question 5: Modifying a flow

Compare the `english_brevity` output token count to the original 1-sentence version (also with `summary_length = long`). How do they compare?

- About the same (within 20%)
- 2-4x more
- 5-10x more
- 10x+ more

### **Q5 Explanation**
![increased-sentences](screenshots\3-05-sentences.png)

`1-sentence Output Tokens: 45`

`3-sentences Output Tokens: 93`

$ \frac{93}{45} \approx{2\times \texttt{more tokens}} $

## Question 6: Best Practices

Based on what you learned in this module, for production workflows requiring deterministic, repeatable results with strict compliance requirements (e.g., financial reporting, workflows in highly regulated industries), which approach is most appropriate?

- Always use AI agents for maximum flexibility and adaptation
- ✅ Use traditional task-based workflows for predictability and auditability ✅
- Use only RAG without agents for better performance
- Use web search tools exclusively to ensure current data

For any production workflows that require near-idempotent (deterministic )results, the best approach would be to use traditional `task`-based workflows. For highly regulated industry requirements, this will not only ensure that outputs are consistent, but segmentable tasks will provide clear data lineage.