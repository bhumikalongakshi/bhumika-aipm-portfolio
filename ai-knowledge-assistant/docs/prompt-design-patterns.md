# 🧠 Prompt Design Patterns — AI Knowledge Assistant

**Document Type:** PM Artifact — Prompt Engineering Decisions  
**Project:** AI Knowledge Assistant (RAG-Based Enterprise Search)  
**Author:** Bhumika Longakshi, Technical Product Manager  
**Version:** 1.0

> This document captures the prompt design decisions made during the 
> development of the AI Knowledge Assistant. It is written from a 
> **product perspective** — focusing on why each decision was made, 
> what tradeoffs were involved, and what the user experience impact was.
> 
> This is not a code document. It is a record of product thinking 
> applied to prompt engineering.

---

## Why Prompt Design is a Product Decision

In a RAG-based system, the prompt is the interface between the 
retrieval layer and the user. It determines:

- **Tone:** Does the product sound like a trusted colleague or a 
  generic chatbot?
- **Trustworthiness:** Does it tell the user when it doesn't know 
  something, or does it confidently fabricate?
- **Utility:** Does it give the user what they actually need, or 
  what they literally asked for?
- **Safety:** Does it stay within its defined scope, or does it 
  wander into ungrounded territory?

Every prompt decision is a product decision with a user experience 
consequence. This document records those decisions.

---

## Pattern 1 — Grounding Constraint

### The Problem
Without explicit instruction, LLMs draw on parametric memory 
(what they learned during training) in addition to the retrieved 
context. For general consumer products, this is useful. For 
enterprise domain-specific products, this is dangerous — the 
model will confidently generate plausible-sounding but incorrect 
answers about our specific systems, configurations, and standards.

### The Decision
The system prompt includes an explicit grounding constraint:
Answer only using the information provided in the context below.
Do not use any knowledge outside of this context. If the context
does not contain enough information to answer the question
accurately, say so explicitly.
### Tradeoff Considered

| Option | Upside | Downside |
|--------|--------|----------|
| Allow model to use parametric knowledge | More complete-sounding answers | High hallucination risk for domain-specific content |
| Strict grounding only | Trustworthy, verifiable answers | Some queries return "insufficient information" |

**I chose strict grounding.** For an enterprise product where engineers 
make technical decisions based on answers, a wrong answer is worse 
than no answer. Trust is the product's core value — one confident 
hallucination destroys it.

### User Experience Impact
Users see source citations with every answer. They can verify. 
This turned out to be a feature, not a limitation — users 
appreciated transparency over completeness.

---

## Pattern 2 — Explicit Uncertainty Handling

### The Problem
Early testing revealed that without guidance, the model would 
attempt to answer even when retrieved context was weak — 
generating vague, hedged responses that looked like answers 
but weren't. Users couldn't tell the difference between a 
well-grounded answer and a poorly-grounded one.

### The Decision
The system prompt includes an explicit uncertainty instruction 
with a defined fallback response:
If the retrieved context does not contain sufficient information
to answer the question reliably, respond with:
"I don't have enough information in the knowledge base to answer
this accurately. You may want to consult [document category]
or reach out to [team]."
Do not attempt to answer beyond what the context supports.
The retrieval layer also applies a confidence threshold — queries 
returning top-match similarity scores below a defined threshold 
trigger the low-confidence path before the LLM even generates.

### Tradeoff Considered

| Option | Upside | Downside |
|--------|--------|----------|
| Always attempt an answer | Feels more helpful in the moment | Low-quality answers erode trust over time |
| Explicit "I don't know" with guidance | Builds long-term trust | Some users initially frustrated by non-answers |

**I chose explicit uncertainty handling.** The long-term trust 
cost of overconfident wrong answers far outweighs the short-term 
frustration of an honest "I don't know."

The fallback response design was important — it doesn't just say 
"I don't know." It redirects the user to a next step. That's the 
difference between a dead end and a useful interaction.

### User Experience Impact
Users reported higher trust in the system after seeing it say 
"I don't know" correctly a few times. Counterintuitively, the 
uncertainty response increased overall confidence in the answers 
it did give.

---

## Pattern 3 — Source Citation Instruction

### The Problem
Users needed to be able to verify answers — both for accuracy 
and for professional accountability (engineers citing sources 
in reports and decisions). Without citation, even correct 
answers felt unverifiable and therefore untrusted.

### The Decision
The system prompt instructs the model to reference the source 
document in its response:
At the end of your response, cite the source document(s)
you used from the provided context in this format:
Source: [Document Name], [Section if available]
If multiple documents were used, list each one.
### Tradeoff Considered

| Option | Upside | Downside |
|--------|--------|----------|
| No citation | Cleaner, shorter responses | Unverifiable; reduces trust |
| Inline citation (like academic papers) | Very precise | Disrupts readability for technical content |
| End-of-response citation | Clean response + verifiable | User must scroll to verify |

**I chose end-of-response citation.** Inline citations interrupted 
the technical content flow in a way that felt academic rather than 
useful. End-of-response citation kept the answer readable while 
still giving users a verification path.

### User Experience Impact
Engineers started including source citations from the assistant 
in their own internal reports — the citation format integrated 
naturally into existing workflows. Unintended positive outcome.

---

## Pattern 4 — Tone & Persona Definition

### The Problem
Without tone guidance, LLM responses defaulted to a generic 
"helpful assistant" register — conversational, slightly verbose, 
with phrases like "Great question!" and "Certainly, I'd be happy 
to help." This tone was inappropriate for a technical engineering 
context and felt unprofessional.

### The Decision
The system prompt defines a clear persona and tone:
You are a technical knowledge assistant for engineering
professionals. Your responses should be:

Precise and concise — no filler phrases, no unnecessary
preamble
Technical in register — use domain terminology accurately
Direct — answer the question first, then provide context
if needed
Professional — no conversational openers like "Great question"

Format responses clearly. Use bullet points or numbered lists
when listing steps or multiple items. Use plain paragraphs
for explanations.
### Tradeoff Considered

| Option | Upside | Downside |
|--------|--------|----------|
| Friendly/conversational tone | Approachable for all users | Feels unprofessional to technical users; verbose |
| Highly terse/minimal | Fast to read | Can feel cold; lacks necessary context |
| Technical but clear | Matches user context; professional | Requires careful calibration |

**I chose technical but clear.** The primary users are engineers 
who value precision over warmth. A response that starts with 
"Certainly! Here's what I found for you..." wastes their time. 
A response that starts with the answer respects it.

### User Experience Impact
Positive feedback specifically on response quality and clarity 
from technical users. No complaints about the tone being cold — 
engineers appreciated the directness.

---

## Pattern 5 — Scope Containment

### The Problem
Users occasionally asked questions outside the scope of the 
knowledge base — general industry questions, comparisons with 
competitor systems, or requests for opinions. Without guidance, 
the model would attempt to answer these using parametric 
knowledge, defeating the purpose of a grounded enterprise system.

### The Decision
The system prompt defines scope boundaries explicitly:
You are only able to answer questions based on the documents
in the enterprise knowledge base provided to you.
If a user asks a question that requires knowledge outside
this scope (general industry questions, competitor comparisons,
opinions, predictions), respond with:
"This question is outside the scope of the enterprise
knowledge base. For this type of query, I'd recommend
[appropriate resource or team]."
### Tradeoff Considered

| Option | Upside | Downside |
|--------|--------|----------|
| Answer anything (general knowledge allowed) | More "complete" experience | Defeats grounding purpose; trust risk |
| Hard scope with no guidance | Clean product boundary | Users feel abandoned with nowhere to go |
| Scope boundary with redirect | Honest + helpful | Requires knowing what to redirect to |

**I chose scope boundary with redirect.** A product that clearly 
knows what it is and what it isn't is more trustworthy than one 
that tries to do everything. The redirect design ensures the 
user experience doesn't end at "I can't help."

### User Experience Impact
Reduced hallucination incidents significantly. A small number of 
users initially pushed back ("why can't it answer general questions?") 
but this resolved once the grounding purpose was explained in 
onboarding documentation.

---

## Pattern 6 — Response Format Guidance

### The Problem
Without format guidance, response structure was inconsistent — 
sometimes prose, sometimes bullets, sometimes a mix, with 
varying levels of detail for similar questions. For engineers 
who needed to quickly scan answers, inconsistency was friction.

### The Decision
The system prompt includes format instructions tied to 
question type:
Format your responses as follows:

For procedural questions (how to do X): use numbered steps
For explanatory questions (what is X, why does X):
use concise paragraphs, 3–5 sentences maximum
For comparison questions (X vs Y): use a table if 3+
attributes are being compared; prose if simpler
For troubleshooting questions: state the likely cause
first, then resolution steps
Keep all responses under 300 words unless the question
genuinely requires more detail
### Tradeoff Considered

| Option | Upside | Downside |
|--------|--------|----------|
| No format guidance | Model chooses best format per query | Inconsistent; harder to scan |
| Fixed format (always bullets) | Consistent | Inappropriate for explanatory content |
| Context-aware format rules | Matches format to content type | More complex prompt; requires testing |

**I chose context-aware format rules.** The test for format is 
always: does this make the answer easier or harder to use? 
A numbered list for a procedural answer is faster to follow 
than prose. A table for a two-item comparison is overkill. 
Format should serve the content.

### User Experience Impact
User readability feedback improved after format instructions 
were added. Engineers specifically noted that procedural 
answers were easier to follow when formatted as steps.

---

## Prompt Iteration Log

| Version | Change Made | Reason | Outcome |
|---------|-------------|--------|---------|
| v0.1 | Basic grounding instruction only | Initial implementation | High hallucination rate on edge cases |
| v0.2 | Added explicit uncertainty handling | Users couldn't distinguish confident wrong answers | Hallucination rate dropped significantly |
| v0.3 | Added tone and persona definition | Generic assistant tone felt unprofessional | Positive user feedback on response quality |
| v0.4 | Added source citation instruction | Users wanted to verify answers | Adoption increased; citations used in reports |
| v0.5 | Added scope containment | Out-of-scope queries causing trust issues | Hallucination on OOS queries eliminated |
| v1.0 | Added format guidance | Inconsistent structure making answers hard to scan | Readability feedback improved |

---

## Key Learnings

**1. Prompts are product specs.**  
Every line in the system prompt is a product decision — about 
tone, trust, scope, and user experience. It deserves the same 
rigour as a PRD requirement.

**2. "I don't know" is a feature.**  
The most important prompt pattern for enterprise AI is 
uncertainty handling. Users need to know when to trust the 
system and when not to. A system that never says it doesn't 
know is a system that can't be trusted.

**3. Tone is a product signal.**  
The first three words of a response tell the user what kind 
of product this is. "Certainly! I'd be happy to..." signals 
a generic chatbot. Starting directly with the answer signals 
a professional tool. Both are prompt decisions.

**4. Iteration is the method.**  
No prompt is right on the first attempt. The v0.1 to v1.0 
journey above represents six rounds of testing, observation, 
and refinement. Prompt design is product design — it requires 
the same feedback loop.

**5. Format is a UX decision.**  
How information is presented matters as much as what information 
is presented. Formatting instructions in the prompt are UX 
decisions disguised as engineering parameters.

---

## V2 Prompt Design Considerations

| Area | Planned Change | Rationale |
|------|---------------|-----------|
| Multi-turn memory | Add conversation history to context window | Enable follow-up questions without re-stating context |
| Personalization | Adjust verbosity based on user role (engineer vs. exec) | Different users need different response depths |
| Feedback integration | Use thumbs up/down signals to adjust retrieval ranking | Ground truth from real usage improves system over time |
| Chain-of-thought | Explore CoT prompting for complex multi-step queries | May improve accuracy on diagnostic/troubleshooting queries |

---

*Document owner: Bhumika Longakshi*  
*Part of the AI Knowledge Assistant portfolio project*  
*github.com/bhumikalongakshi/bhumika-aipm-portfolio*
