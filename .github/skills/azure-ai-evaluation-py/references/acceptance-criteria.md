# Azure AI Projects SDK Acceptance Criteria

**SDK**: `azure-ai-projects`
**Repository**: https://github.com/Azure/azure-sdk-for-python
**Commit**: `main`
**Purpose**: Skill testing acceptance criteria for validating generated code correctness

---

## 1. Imports

### 1.1 ✅ CORRECT: Core SDK Imports
```python
from azure.ai.projects import AIProjectClient
from azure.identity import DefaultAzureCredential

# For inline data sources
from openai.types.evals.create_eval_jsonl_run_data_source_param import (
    CreateEvalJSONLRunDataSourceParam,
    SourceFileContent,
    SourceFileContentContent,
)
from openai.types.eval_create_params import DataSourceConfigCustom

# For custom evaluators
from azure.ai.projects.models import (
    EvaluatorVersion,
    EvaluatorCategory,
    EvaluatorType,
    CodeBasedEvaluatorDefinition,
    PromptBasedEvaluatorDefinition,
    EvaluatorMetric,
    EvaluatorMetricType,
    EvaluatorMetricDirection,
)
```

### 1.2 ❌ INCORRECT: Using Deprecated SDK
```python
# WRONG - azure-ai-evaluation is deprecated, use azure-ai-projects
from azure.ai.evaluation import evaluate
from azure.ai.evaluation import GroundednessEvaluator
from azure.ai.evaluation import AzureOpenAIModelConfiguration
```

---

## 2. Client Setup

### 2.1 ✅ CORRECT: AIProjectClient with Managed Identity
```python
import os
from azure.ai.projects import AIProjectClient
from azure.identity import DefaultAzureCredential

endpoint = os.environ["AZURE_AI_PROJECT_ENDPOINT"]

with (
    DefaultAzureCredential() as credential,
    AIProjectClient(endpoint=endpoint, credential=credential) as project_client,
):
    openai_client = project_client.get_openai_client()
    # Use openai_client.evals.*
```

### 2.2 ✅ CORRECT: Environment Variable
```python
# Endpoint format: https://<account>.services.ai.azure.com/api/projects/<project>
os.environ["AZURE_AI_PROJECT_ENDPOINT"] = "https://myaccount.services.ai.azure.com/api/projects/myproject"
os.environ["AZURE_AI_MODEL_DEPLOYMENT_NAME"] = "gpt-4o-mini"
```

### 2.3 ❌ INCORRECT: Wrong Client Setup
```python
# WRONG - not using context manager
client = AIProjectClient(endpoint=endpoint, credential=credential)
# Missing proper resource cleanup

# WRONG - using old SDK patterns
from azure.ai.evaluation import AzureOpenAIModelConfiguration
model_config = AzureOpenAIModelConfiguration(
    azure_endpoint=os.environ["AZURE_OPENAI_ENDPOINT"],
    ...
)
```

---

## 3. Data Sources

### 3.1 ✅ CORRECT: Inline JSONL Data
```python
data = [
    {"query": "What is Azure?", "response": "Azure is Microsoft's cloud platform."},
    {"query": "What is AI?", "response": "AI is artificial intelligence."},
]

data_source = CreateEvalJSONLRunDataSourceParam(
    type="jsonl",
    source=SourceFileContent(
        type="file_content",
        content=[
            SourceFileContentContent(
                item=item,
                sample={},  # Empty for non-agent evaluations
            )
            for item in data
        ],
    ),
)

data_source_config = DataSourceConfigCustom(
    type="custom",
    item_schema={
        "type": "object",
        "properties": {
            "query": {"type": "string"},
            "response": {"type": "string"},
        },
        "required": ["query", "response"],
    },
    include_sample_schema=False,  # Set to True for agent evaluations
)
```

### 3.2 ✅ CORRECT: Agent Data with Tool Calls
```python
data_source = CreateEvalJSONLRunDataSourceParam(
    type="jsonl",
    source=SourceFileContent(
        type="file_content",
        content=[
            SourceFileContentContent(
                item={"query": "Weather in Seattle?"},
                sample={
                    "output_text": "It's 55°F and cloudy in Seattle.",
                    "output_items": [
                        {
                            "type": "tool_call",
                            "tool_call_id": "call_123",
                            "name": "get_weather",
                            "arguments": {"location": "Seattle"},
                            "result": {"temp": "55", "condition": "cloudy"},
                        }
                    ],
                },
            )
        ],
    ),
)

data_source_config = DataSourceConfigCustom(
    type="custom",
    item_schema={
        "type": "object",
        "properties": {"query": {"type": "string"}},
        "required": ["query"],
    },
    include_sample_schema=True,  # Required for agent evaluations
)
```

### 3.3 ❌ INCORRECT: Missing Schema
```python
# WRONG - data_source_config must include item_schema
data_source_config = DataSourceConfigCustom(type="custom")

# WRONG - agent evaluations must have include_sample_schema=True
data_source_config = DataSourceConfigCustom(
    type="custom",
    item_schema={"type": "object", ...},
    include_sample_schema=False,  # Wrong for agent evaluations
)
```

---

## 4. Testing Criteria (Evaluators)

### 4.1 ✅ CORRECT: Built-in Evaluator with Data Mapping
```python
testing_criteria = [
    {
        "type": "azure_ai_evaluator",
        "name": "coherence",  # Your label for this evaluation
        "evaluator_name": "builtin.coherence",  # Built-in evaluator ID
        "data_mapping": {
            "query": "{{item.query}}",
            "response": "{{item.response}}",
        },
        "initialization_parameters": {"deployment_name": "gpt-4o-mini"},
    },
]
```

### 4.2 ✅ CORRECT: Safety Evaluators
```python
testing_criteria = [
    {
        "type": "azure_ai_evaluator",
        "name": "violence_check",
        "evaluator_name": "builtin.violence",
        "data_mapping": {
            "query": "{{item.query}}",
            "response": "{{item.response}}",
        },
    },
    {
        "type": "azure_ai_evaluator",
        "name": "groundedness_check",
        "evaluator_name": "builtin.groundedness",
        "data_mapping": {
            "query": "{{item.query}}",
            "context": "{{item.context}}",
            "response": "{{item.response}}",
        },
    },
]
```

### 4.3 ✅ CORRECT: Agent Evaluators with sample Mapping
```python
testing_criteria = [
    {
        "type": "azure_ai_evaluator",
        "name": "intent_resolution",
        "evaluator_name": "builtin.intent_resolution",
        "data_mapping": {
            "query": "{{item.query}}",
            "response": "{{sample.output_text}}",  # Use sample for agent outputs
        },
        "initialization_parameters": {"deployment_name": "gpt-4o-mini"},
    },
    {
        "type": "azure_ai_evaluator",
        "name": "tool_call_accuracy",
        "evaluator_name": "builtin.tool_call_accuracy",
        "data_mapping": {
            "query": "{{item.query}}",
            "response": "{{sample.output_items}}",  # JSON with tool calls
        },
        "initialization_parameters": {"deployment_name": "gpt-4o-mini"},
    },
]
```

### 4.4 ✅ CORRECT: OpenAI Graders
```python
testing_criteria = [
    # Label grader (classification)
    {
        "type": "label_model",
        "name": "sentiment_classifier",
        "model": "gpt-4o-mini",
        "input": [
            {"role": "user", "content": "Classify sentiment: {{item.response}}"}
        ],
        "labels": ["positive", "negative", "neutral"],
        "passing_labels": ["positive", "neutral"],
    },
    # String check grader (pattern matching)
    {
        "type": "string_check",
        "name": "contains_disclaimer",
        "input": "{{item.response}}",
        "operation": "contains",
        "reference": "Please consult a professional",
    },
    # Text similarity grader
    {
        "type": "text_similarity",
        "name": "semantic_match",
        "input": "{{item.response}}",
        "reference": "{{item.expected}}",
        "evaluation_metric": "fuzzy_match",
        "pass_threshold": 0.8,
    },
]
```

### 4.5 ❌ INCORRECT: Wrong Testing Criteria Format
```python
# WRONG - using old azure-ai-evaluation patterns
testing_criteria = {"groundedness": GroundednessEvaluator(model_config)}

# WRONG - missing evaluator_name prefix
testing_criteria = [
    {
        "type": "azure_ai_evaluator",
        "evaluator_name": "coherence",  # Must be "builtin.coherence"
        ...
    },
]

# WRONG - wrong data mapping syntax
testing_criteria = [
    {
        ...
        "data_mapping": {
            "query": "${data.query}",  # Old syntax, use {{item.query}}
        },
    },
]
```

---

## 5. Creating and Running Evaluations

### 5.1 ✅ CORRECT: Create Evaluation
```python
eval_object = openai_client.evals.create(
    name="My Evaluation",
    data_source_config=data_source_config,
    testing_criteria=testing_criteria,
)
print(f"Created eval: {eval_object.id}")
```

### 5.2 ✅ CORRECT: Run Evaluation
```python
run = openai_client.evals.runs.create(
    eval_id=eval_object.id,
    name="Run 1",
    data_source=data_source,
)
print(f"Run ID: {run.id}, Status: {run.status}")
```

### 5.3 ✅ CORRECT: Poll for Completion
```python
import time

while run.status not in ["completed", "failed", "cancelled"]:
    time.sleep(5)
    run = openai_client.evals.runs.retrieve(
        eval_id=eval_object.id,
        run_id=run.id,
    )
    print(f"Status: {run.status}")
```

### 5.4 ✅ CORRECT: Retrieve Results
```python
output_items = list(openai_client.evals.runs.output_items.list(
    eval_id=eval_object.id,
    run_id=run.id,
))

for item in output_items:
    for result in item.results:
        print(f"{result.name}: {result.score}")
```

### 5.5 ❌ INCORRECT: Using Old SDK evaluate() Function
```python
# WRONG - using deprecated azure-ai-evaluation
from azure.ai.evaluation import evaluate

result = evaluate(
    data="data.jsonl",
    evaluators={"groundedness": groundedness},
)
```

---

## 6. Custom Evaluators

### 6.1 ✅ CORRECT: Code-Based Evaluator
```python
evaluator = project_client.evaluators.create_version(
    name="word_count",
    evaluator_version=EvaluatorVersion(
        evaluator_type=EvaluatorType.CUSTOM,
        categories=[EvaluatorCategory.QUALITY],
        display_name="Word Count",
        description="Counts words in response",
        definition=CodeBasedEvaluatorDefinition(
            code_text='''
def grade(sample, item) -> dict:
    return {"word_count": len(item.get("response", "").split())}
''',
            data_schema={
                "type": "object",
                "properties": {"response": {"type": "string"}},
                "required": ["response"],
            },
            metrics={
                "word_count": EvaluatorMetric(
                    type=EvaluatorMetricType.ORDINAL,
                    min_value=0,
                    max_value=10000,
                ),
            },
        ),
    ),
)
```

### 6.2 ✅ CORRECT: Using Custom Evaluator in Testing Criteria
```python
testing_criteria = [
    {
        "type": "azure_ai_evaluator",
        "name": "word_count_check",
        "evaluator_name": "word_count",  # Custom evaluator name (no builtin. prefix)
        "data_mapping": {"response": "{{item.response}}"},
    },
]
```

### 6.3 ❌ INCORRECT: Old Custom Evaluator Pattern
```python
# WRONG - using decorator from deprecated SDK
from azure.ai.evaluation import evaluator

@evaluator
def my_evaluator(response: str) -> dict:
    return {"score": 1.0}

# WRONG - class-based evaluator from old SDK
class MyEvaluator:
    def __call__(self, response: str) -> dict:
        return {"score": 1.0}
```

---

## 7. Listing Built-in Evaluators

### 7.1 ✅ CORRECT: Discover Available Evaluators
```python
# List all built-in evaluators
evaluators = project_client.evaluators.list_latest_versions(type="builtin")
for e in evaluators:
    print(f"builtin.{e.name}: {e.description}")
    print(f"  Categories: {[str(c) for c in e.categories]}")
    print(f"  Data Schema: {e.definition.data_schema}")
```

### 7.2 ✅ CORRECT: Get Evaluator Details
```python
evaluator = project_client.evaluators.get_version(
    name="coherence",
    version="latest"
)
print(f"Required inputs: {evaluator.definition.data_schema}")
print(f"Metrics: {evaluator.definition.metrics}")
```

---

## 8. Agent Evaluation with Target

### 8.1 ✅ CORRECT: Evaluate Azure AI Agent
```python
# Data source that calls an agent target
data_source = {
    "type": "azure_ai_target_completions",
    "target": {
        "type": "azure_ai_agent",
        "agent_id": "my_agent_id",  # Your agent ID
    },
}

testing_criteria = [
    {
        "type": "azure_ai_evaluator",
        "name": "intent_resolution",
        "evaluator_name": "builtin.intent_resolution",
        "data_mapping": {
            "query": "{{item.query}}",
            "response": "{{sample.output_text}}",
        },
        "initialization_parameters": {"deployment_name": "gpt-4o-mini"},
    },
]

run = openai_client.evals.runs.create(
    eval_id=eval_object.id,
    name="Agent Evaluation Run",
    data_source=data_source,
)
```

### 8.2 ❌ INCORRECT: Using Old Target Pattern
```python
# WRONG - using deprecated evaluate() with target
from azure.ai.evaluation import evaluate

result = evaluate(
    data="queries.jsonl",
    target=my_agent_function,  # Old pattern
    evaluators={"intent": IntentResolutionEvaluator(model_config)},
)
```

---

## Summary: Key Differences from Deprecated SDK

| Deprecated (`azure-ai-evaluation`) | Current (`azure-ai-projects`) |
|---|---|
| `from azure.ai.evaluation import evaluate` | `openai_client.evals.create()` / `.runs.create()` |
| `GroundednessEvaluator(model_config)` | `"evaluator_name": "builtin.groundedness"` |
| `model_config = {...}` | `initialization_parameters = {...}` |
| `column_mapping: {"query": "${data.query}"}` | `data_mapping: {"query": "{{item.query}}"}` |
| `@evaluator` decorator | `project_client.evaluators.create_version()` |
| Synchronous evaluate() call | Async create eval → create run → poll → retrieve |
