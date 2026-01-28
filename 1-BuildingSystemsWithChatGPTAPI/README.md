# Building Systems with ChatGPT API

## Overview

This module covers **production-ready patterns** for building LLM-powered applications using OpenAI's Chat Completions API. Each notebook progressively builds towards a complete customer service system.

## Learning Path

### Fundamentals (A-C)
1. **A.ExploreCompletionsAPI** - API basics, token management, helper functions
2. **B.EvaluateAndClassifyInputs** - Intent classification and routing
3. **C.InputModerationTechniques** - Content safety and filtering

### Advanced Techniques (D-F)
4. **D.ChainOfThoughtReasoning** - Complex reasoning patterns
5. **E.PromptChaining** - Multi-step pipelines
6. **F.EvaluatingOutputs** - Quality assurance and validation

### Complete System (G-I)
7. **G.CustomerServiceBot** - End-to-end implementation
8. **H.EvaluatingLLMPerformance** - Performance metrics
9. **I.EvaluationUsingRubric** - Automated quality scoring

---

## Notebook Details

### A. Explore Completions API ✅ Documented

**Core Concepts**:
- Message roles (system, user, assistant)
- Temperature control (0=deterministic, 2=creative)
- Token counting for cost management
- Helper function patterns

**Key Takeaways**:
- System messages define behavior and constraints
- Token usage directly impacts cost (track with `response.usage`)
- Delimiters in prompts prevent injection attacks

**Production Patterns**:
```python
# Simple completion
response = get_completion(prompt)

# Full control with message history
response = get_completion_from_messages(messages, temperature=0, max_tokens=500)

# With token tracking
response, token_dict = get_completion_and_token_count(messages)
```

---

### B. Evaluate and Classify Inputs ✅ Documented

**Core Concepts**:
- Hierarchical classification (primary + secondary categories)
- JSON output for programmatic routing
- Delimiter-based input isolation

**Key Takeaways**:
- Classification enables intelligent routing and cost optimization
- Comprehensive taxonomies improve accuracy
- Always validate JSON responses

**Production Patterns**:
```python
# Classification system message
system_message = """
Classify queries into categories. Output JSON:
{"primary": "Billing", "secondary": "Cancel subscription"}
"""

# Route based on classification
classification = json.loads(response)
if classification["primary"] == "Billing":
    route_to_billing_team()
```

**Routing Logic**:
- Billing → Specialized billing handler
- Technical Support → Tech support team
- "Speak to a human" → Escalate immediately
- Low confidence → Human review

---

### C. Input Moderation Techniques

**Core Concepts**:
- OpenAI Moderation API for content safety
- Prompt injection detection
- PII (Personally Identifiable Information) filtering

**Key Takeaways**:
- Always moderate user input before processing
- Check for policy violations (violence, hate speech, self-harm)
- Sanitize inputs to prevent system prompt overrides

**Production Patterns**:
```python
# Use OpenAI Moderation API
moderation_response = client.moderations.create(input=user_input)
if moderation_response.results[0].flagged:
    return "Content policy violation"

# Check for prompt injection attempts
if detect_injection(user_input):
    return "Invalid input format"
```

---

### D. Chain of Thought Reasoning

**Core Concepts**:
- Step-by-step reasoning for complex tasks
- Inner monologue technique (hidden reasoning steps)
- Delimiter separation between reasoning and answer

**Key Takeaways**:
- CoT improves accuracy on multi-step problems
- Hide reasoning from users (show only final answer)
- Use structured prompts to elicit step-by-step thinking

**Production Patterns**:
```python
system_message = """
Follow these steps to answer:
1. Understand the query
2. Identify relevant information
3. Reason through the problem
4. Provide final answer

Format: ####REASONING####...####ANSWER####...
"""
```

**When to Use**:
- Mathematical calculations
- Multi-step troubleshooting
- Complex policy interpretation
- Legal/compliance questions

---

### E. Prompt Chaining

**Core Concepts**:
- Breaking complex tasks into sequential subtasks
- Passing output of one LLM call as input to next
- State management across chain steps

**Key Takeaways**:
- Chains are more reliable than single complex prompts
- Each step can use different models/temperatures
- Enables error handling at each stage

**Production Patterns**:
```python
# Step 1: Classify intent
classification = classify_intent(user_input)

# Step 2: Fetch relevant data
data = fetch_data(classification)

# Step 3: Generate response
response = generate_response(user_input, data, classification)
```

**Use Cases**:
- Customer support (classify → fetch context → respond)
- Content generation (outline → write → edit → polish)
- Research assistant (query → search → summarize → cite)

---

### F. Evaluating Outputs

**Core Concepts**:
- LLM-as-judge pattern
- Automated quality assessment
- Rubric-based evaluation

**Key Takeaways**:
- Use LLMs to evaluate other LLM outputs
- Define clear evaluation criteria (rubrics)
- Track quality metrics over time

**Production Patterns**:
```python
evaluation_prompt = """
Evaluate this response on a 1-5 scale for:
- Accuracy
- Helpfulness
- Tone appropriateness
- Completeness

Response: {response}
Output JSON with scores.
"""
```

---

### G. Customer Service Bot

**Core Concepts**:
- End-to-end system integration
- Product catalog integration
- Multi-turn conversation handling

**Key Takeaways**:
- Combines all previous techniques (classification, moderation, CoT, chaining)
- Maintains conversation context across turns
- Accesses external data sources (product DB)

**System Architecture**:
```
User Input
  ↓
Moderation (C)
  ↓
Classification (B)
  ↓
Context Retrieval (product data)
  ↓
Response Generation (A)
  ↓
Output Evaluation (F)
  ↓
User Response
```

---

### H. Evaluating LLM Performance

**Core Concepts**:
- Performance metrics (accuracy, latency, cost)
- A/B testing frameworks
- Regression testing for prompt changes

**Key Takeaways**:
- Track metrics: accuracy, F1, latency, tokens/request
- Use test sets for prompt iteration
- Monitor production metrics continuously

**Metrics to Track**:
```python
metrics = {
    "accuracy": 0.95,           # Correctness vs ground truth
    "avg_latency_ms": 1200,     # Response time
    "avg_tokens": 350,          # Cost per request
    "user_satisfaction": 4.2,   # User feedback score
    "escalation_rate": 0.03     # % sent to human agents
}
```

---

### I. Evaluation Using Rubric

**Core Concepts**:
- Multi-dimensional quality assessment
- Weighted scoring systems
- Automated grading at scale

**Key Takeaways**:
- Rubrics enable consistent evaluation
- Use LLM-as-judge for subjective metrics
- Weight criteria by importance

**Rubric Example**:
```python
rubric = {
    "accuracy": {"weight": 0.4, "description": "Factually correct"},
    "helpfulness": {"weight": 0.3, "description": "Solves user problem"},
    "tone": {"weight": 0.2, "description": "Professional and empathetic"},
    "conciseness": {"weight": 0.1, "description": "No unnecessary info"}
}
```

---

## Production Readiness Checklist

### Core Infrastructure
- [ ] **API Key Management** - Secure storage (environment variables, secrets manager)
- [ ] **Rate Limiting** - Implement exponential backoff
- [ ] **Error Handling** - Retry logic, fallback responses
- [ ] **Logging** - Track requests, responses, errors, token usage
- [ ] **Monitoring** - Latency, cost, error rates, user satisfaction

### Quality & Safety
- [ ] **Input Moderation** - Filter harmful content before processing
- [ ] **Output Validation** - Check responses meet quality standards
- [ ] **Prompt Injection Prevention** - Use delimiters, input sanitization
- [ ] **PII Detection** - Identify and handle sensitive data
- [ ] **Fallback Mechanisms** - Human escalation for edge cases

### Performance & Cost
- [ ] **Token Optimization** - Minimize prompt length, use cheaper models where possible
- [ ] **Caching** - Cache responses for repeated queries
- [ ] **Batch Processing** - Group requests when latency allows
- [ ] **Model Selection** - Use appropriate model for task complexity
- [ ] **Budget Alerts** - Track spending, set limits

### Evaluation & Improvement
- [ ] **Test Sets** - Curated examples for regression testing
- [ ] **Evaluation Pipeline** - Automated quality checks on prompt changes
- [ ] **User Feedback** - Collect ratings, escalations, corrections
- [ ] **A/B Testing** - Compare prompt variations
- [ ] **Performance Dashboards** - Real-time metrics visibility

---

## Cost Optimization Strategies

### Model Selection
| Task Complexity | Model | Cost/1M Tokens (Input/Output) |
|----------------|-------|-------------------------------|
| Simple classification | gpt-3.5-turbo | $0.50 / $1.50 |
| General tasks | gpt-4o-mini | $0.15 / $0.60 |
| Complex reasoning | gpt-4o | $2.50 / $10.00 |

### Optimization Tactics
1. **Classification First** - Use cheap model to route, expensive model only when needed
2. **Prompt Compression** - Remove unnecessary context
3. **Response Capping** - Set `max_tokens` to prevent overrun
4. **Caching** - Store responses for duplicate queries
5. **Batch API** - 50% cost reduction for non-real-time tasks

---

## Common Pitfalls & Solutions

### Issue: Inconsistent Outputs
**Cause**: Non-zero temperature  
**Solution**: Set `temperature=0` for deterministic tasks

### Issue: High Costs
**Cause**: Oversized prompts, wrong model selection  
**Solution**: Track token usage, optimize prompts, use cheaper models

### Issue: Prompt Injection
**Cause**: User input not isolated  
**Solution**: Wrap input with delimiters, validate format

### Issue: Low Quality Responses
**Cause**: Vague instructions, missing context  
**Solution**: Use system messages, provide examples, enable CoT reasoning

### Issue: Rate Limit Errors
**Cause**: Too many concurrent requests  
**Solution**: Implement exponential backoff, queue management

---

## Integration Patterns

### REST API Wrapper
```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/classify', methods=['POST'])
def classify_input():
    user_input = request.json['message']
    # Moderate
    if is_policy_violation(user_input):
        return jsonify({"error": "Content policy violation"}), 400
    # Classify
    classification = classify(user_input)
    return jsonify(classification)
```

### Async Processing
```python
import asyncio

async def process_batch(inputs):
    tasks = [classify_async(inp) for inp in inputs]
    results = await asyncio.gather(*tasks)
    return results
```

### Event-Driven Architecture
```python
# Consumer pulls from queue
def process_message(message):
    classification = classify(message['content'])
    if classification['primary'] == 'Urgent':
        send_to_priority_queue(message)
    else:
        send_to_standard_queue(message)
```

---

## Next Steps

1. **Complete all notebooks** in sequence
2. **Build a prototype** combining techniques from G-I
3. **Deploy to production** with full monitoring
4. **Iterate based on metrics** from H-I
5. **Scale incrementally** as usage grows

## Additional Resources

- [OpenAI API Documentation](https://platform.openai.com/docs)
- [Prompt Engineering Guide](https://www.promptingguide.ai/)
- [LangChain (for production orchestration)](https://python.langchain.com/)
