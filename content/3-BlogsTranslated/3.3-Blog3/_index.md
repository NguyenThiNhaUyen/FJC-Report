---
title: "Blog 3"
# date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---
## Ground truth generation and review best practices for evaluating generative AI question-answering with FMEval
---

Generative AI question-answering applications are pushing the boundaries of enterprise productivity. These assistants can be powered by various backend architectures including Retrieval Augmented Generation (RAG), agentic workflows, fine-tuned large language models (LLMs), or a combination of these techniques. However, building and deploying trustworthy AI assistants requires a robust ground truth and evaluation framework.

Ground truth data in AI refers to data that is known to be factual, representing the expected use case outcome for the system being modeled. By providing an expected outcome to measure against, ground truth data unlocks the ability to deterministically evaluate system quality. Running deterministic evaluation of generative AI assistants against use case ground truth data enables the creation of custom benchmarks.

<!--more-->

## The Importance of Ground Truth

These benchmarks are essential for tracking performance drift over time and for statistically comparing multiple assistants in accomplishing the same task. Additionally, they enable quantifying performance changes as a function of enhancements to the underlying assistant, all within a controlled setting. With deterministic evaluation processes such as the Factual Knowledge and QA Accuracy metrics of FMEval, ground truth generation and evaluation metric implementation are tightly coupled.

In this post, we discuss best practices for applying LLMs to generate ground truth for evaluating question-answering assistants with FMEval on an enterprise scale. FMEval is a comprehensive evaluation suite from Amazon SageMaker Clarify, and provides standardized implementations of metrics to assess quality and responsibility.

## Generating Ground Truth for FMEval Question-Answering Evaluation

One option to get started with ground truth generation is human curation of a small question-answer dataset. The human curated dataset should be small (based on bandwidth), high in signal, and ideally prepared by use case subject matter experts (SMEs).

### Outcomes of Initial Ground Truth Generation

The exercise of generating this dataset forces a data alignment exercise early in the evaluation process. The outcomes for this exercise are three-fold:

1. **Stakeholder alignment** on the top N important questions
2. **Stakeholder awareness** of the evaluation process
3. A **high-fidelity starter ground truth dataset** for the first proof of concept evaluation

### Scaling with LLMs

While an SME ground truth curation exercise is a strong start, at the scale of an enterprise knowledge base, pure SME generation of ground truth will become prohibitively time and resource intensive. To scale ground truth generation and curation, you can apply a risk-based approach in conjunction with a prompt-based strategy using LLMs.

> **Important:** LLM-generated ground truth isn't a substitute for use case SME involvement. SMEs will still be needed to identify which questions are fundamental to the business and align the ground truth with business value as part of a human-in-the-loop process.

## Example: Amazon 2023 Shareholder Letter

To demonstrate, we provide a step-by-step walkthrough using Amazon's 2023 letter to shareholders as source data. In keeping with ground truth curation best practices for FMEval question-answering, ground truth is curated as **question-answer-fact triplets**.

### Source Document

> Dear Shareholders: Last year at this time, I shared my enthusiasm and optimism for Amazon's future. Today, I have even more. The reasons are many, but start with the progress we've made in our financial results and customer experiences, and extend to our continued innovation and the remarkable opportunities in front of us. In 2023, Amazon's total revenue grew 12% year-over-year ("YoY") from $514B to $575B. By segment, North America revenue increased 12% YoY from $316B to $353B, International revenue grew 11% YoY from $118B to $131B, and AWS revenue increased 13% YoY from $80B to $91B.

### Generated Ground Truth Examples

| Question | Ground Truth Answer | Fact |
|----------|---------------------|------|
| What was Amazon's total revenue growth in 2023? | Amazon's total revenue grew 12% year-over-year from $514B to $575B in 2023. | 12%<OR>$514B to $575B |
| How much did North America revenue increase in 2023? | North America revenue increased 12% year-over-year from $316B to $353B. | 12%<OR>$316B to $353B |
| What was the growth in International revenue for Amazon in 2023? | International revenue grew 11% year-over-year from $118B to $131B. | 11%<OR>$118B to $131B |
| How much did AWS revenue increase in 2023? | AWS revenue increased 13% year-over-year from $80B to $91B. | 13%<OR>$80B to $91B |
| What was Amazon's operating income improvement in 2023? | Operating income in 2023 improved 201% year-over-year from $12.2B to $36.9B. | 201%<OR>$12.2B to $36.9B |
| What was Amazon's operating margin in 2023? | Amazon's operating margin in 2023 was 6.4%. | 6.4% |

## LLM Prompt Template for Ground Truth Generation

To convert the source document excerpt into ground truth, we provide a base LLM prompt template. For our example, we work with Anthropic's Claude LLM on Amazon Bedrock.

```text
You are an expert in ground truth curation for generative AI application evaluation on AWS.

Follow the instructions provided in the <instructions> XML tag for generating 
question answer fact triplets from a source document excerpt.

<instructions>
- Let's work this out in a step-by-step way to be sure we have the right answer.
- Review the source document excerpt provided in <document> XML tags below
- For each meaningful domain fact in the <document>, extract an unambiguous 
  question-answer-fact set in JSON format including a question and answer pair 
  encapsulating the fact in the form of a short sentence, followed by a minimally 
  expressed fact extracted from the answer.

<domain_knowledge_focus>
- Focus ONLY on substantive domain knowledge contained within the document content
- Ignore all metadata and structural elements including but not limited to:
  - Document dates, versions, page numbers
  - Section numbers or titles
  - Table structure or row/column positions
  - List positions or ordering
- Questions must reference specific domain entities rather than generic document elements
</domain_knowledge_focus>

<best_practices>
- Questions, answers, and facts should not refer to the subject entity as "it" or "they"
- Facts should be represented in 3 or fewer words describing an entity
- If there are units in the fact, provide multiple versions using <OR> as delimiter

<unit_variations>
- Dollar Unit Equivalencies: `1,234 million<OR>1.234 billion`
- Date Format Equivalencies: `2024-01-01<OR>January 1st 2024`
- Number Equivalencies: `1<OR>one`
</unit_variations>
</best_practices>

- Start your response immediately with the question-answer-fact set JSON
</instructions>

<document>
{context_document}
</document>

Now, extract the question answer pairs and fact from the document excerpt.
```

### Output Format

The generation output is provided as fact-wise JSONLines records:

```json
{
  "question": "[Question]",
  "ground_truth_answer": "[Ground Truth Answer]",
  "fact": "[Fact]"
}
```

## Scaling Ground Truth Generation with a Pipeline

To automate ground truth generation, we provide a serverless batch pipeline architecture. At a high level, the AWS Step Functions pipeline accepts source data in Amazon S3, and orchestrates AWS Lambda functions for ingestion, chunking, and prompting on Amazon Bedrock.

### Pipeline Input Parameters

There are three user inputs to the step function:

```json
{
  "dataset_name": "YOUR_DATASET_NAME",
  "input_prefix": "YOUR_INPUT_PREFIX",
  "review_percentage": "REVIEW_PERCENTAGE"
}
```

Additional configurations are set by Lambda environment variables, such as:
- S3 source bucket
- Amazon Bedrock Model ID to invoke on generation

### Pipeline Event Payload Structure

After validation, the system assembles the global event payload:

```json
{
  "system_input": {
    "run_id": "<AWS Step Function execution ID>",
    "input_bucket": "<Input data Amazon S3 bucket>",
    "output_bucket": "<Output data Amazon S3 bucket>",
    "output_document_chunks_prefix": "<S3 Prefix to store chunks>",
    "chunk_size": "<Document chunk size>",
    "chunk_overlap": "<Number of tokens overlap>"
  },
  "user_input": {
    "dataset_name": "<Dataset name>",
    "input_prefix": "<S3 prefix for input data>",
    "review_percentage": "<Percent for human review>"
  }
}
```

### Pipeline Stages

1. **First Distributed Map**: Iterates over files in the input bucket to start document ingestion and chunking with horizontal scaling
2. **Second Distributed Map**: Each chunk is fed to the ground truth generation prompt on Amazon Bedrock
3. **Aggregation Step**: SageMaker Processing job concatenates JSONLines records and samples for human review

## Judging Ground Truth for FMEval Question-Answering Evaluation

Measuring ground truth quality is an essential component of the evaluation lifecycle. There are two key components:

### 1. Human-in-the-Loop (HITL)

The level of ground truth human review required is determined by the risk of having incorrect ground truth. The HITL process consists of four steps:

#### Step 1: Classify Risk

Perform a risk analysis to establish the severity and likelihood of negative events. Assign the ground truth dataset a risk level: **Low**, **Medium**, **High**, or **Critical**.

**Risk Assessment Matrix:**

| Likelihood | Very Low | Low | Moderate | Major | Extreme |
|------------|----------|-----|----------|-------|---------|
| **Frequent** | Low | Medium | High | Critical | Critical |
| **Likely** | Very low | Low | Medium | High | Critical |
| **Possible** | Very low | Low | Medium | Medium | High |
| **Unlikely** | Very low | Very low | Low | Low | Medium |
| **Rare** | Very low | Very low | Very low | Very low | Low |

#### Step 2: Human Review

Based on the assigned risk level, use-case expert reviewers examine a proportional amount of the ground truth. Organizations can set acceptability thresholds for percentage of HITL intervention based on their tolerance for risk.

#### Step 3: Identify Findings

Reviewers identify:
- **Hallucinations** relative to source data
- **Information veracity** challenges according to their expertise
- Other criteria set by the organization

#### Step 4: Action Results

Reviewers take business actions such as:
- Updating and deleting records
- Re-writing applicable source documents
- Bringing in LLMOps SMEs to apply dataset curation best practices

### Example: Hallucination Detection

| Source Data | Hallucinated Output | Corrected Output |
|-------------|---------------------|------------------|
| Amazon's total revenue grew **12%** YoY from $514B to $575B | "grew **15%** year-over-year" | "grew **12%** year-over-year" |

### Example: SME Veracity Review

| Source Data | SME Review | Remediation |
|-------------|-----------|-------------|
| "Effective June 1st, 2023, AnyCompany is pleased to announce Casual Friday..." | "As an HR Specialist, this looks incorrect. We did not implement the Casual Friday policy—the source data must be out of date." | Delete incorrect ground truth; Update source document |

### 2. LLM-as-a-Judge

When scaling HITL, LLM reviewers can perform hallucination detection and remediation. This idea is known as **self-reflective RAG**, and can be used to decrease—but not eliminate—the level of human effort in the process.

Amazon Bedrock now offers:
- The ability to use LLM reviewers
- Automated reasoning checks with Amazon Bedrock Guardrails for mathematically sound self-validation

> **Golden Rule:** Make sure the organization's review process aligns with the accepted risk level for the ground truth dataset.

## Conclusion

In this post, we provided guidance on generating and reviewing ground truth for evaluating question-answering applications using FMEval. We explored best practices for applying LLMs to scale ground truth generation while maintaining quality and accuracy.

### Key Takeaways

- Use **SME-curated datasets** as a starting point for high-quality ground truth
- Apply **LLM-based generation** to scale across enterprise knowledge bases
- Implement **serverless batch pipelines** for automation
- Establish **risk-based HITL processes** for quality assurance
- Leverage **LLM-as-a-judge** patterns for scalable review

By following these guidelines, organizations can follow responsible AI best practices for creating high-quality ground truth datasets for deterministic evaluation of question-answering assistants. Use case-specific evaluations supported by well-curated ground truth play a crucial role in developing and deploying AI solutions that meet the highest standards of quality and responsibility.

Whether you're developing an internal tool, a customer-facing virtual assistant, or exploring the potential of generative AI for your organization, we encourage you to adopt these best practices. Start implementing robust ground truth generation and review processes for your generative AI question-answering evaluations today with FMEval.

---

## About the Authors

**Samantha Stuart** is a Data Scientist with AWS Professional Services, and has delivered for customers across generative AI, MLOps, and ETL engagements. Samantha has a research master's degree in engineering from the University of Toronto, where she authored several publications on data-centric AI for drug delivery system design. Outside of work, she is most likely spotted playing music, spending time with friends and family, at the yoga studio, or exploring Toronto.

**Philippe Duplessis-Guindon** is a cloud consultant at AWS, where he has worked on a wide range of generative AI projects. He has touched on most aspects of these projects, from infrastructure and DevOps to software development and AI/ML. After earning his bachelor's degree in software engineering and a master's in computer vision and machine learning from Polytechnique Montreal, Philippe joined AWS to put his expertise to work for customers. When he's not at work, you're likely to find Philippe outdoors—either rock climbing or going for a run.

**Rahul Jani** is a Data Architect with AWS Professional Service. He collaborates closely with enterprise customers building modern data platforms, generative AI applications, and MLOps. He is specialized in the design and implementation of big data and analytical applications on the AWS platform. Beyond work, he values quality time with family and embraces opportunities for travel.

**Ivan Cui** is a Data Science Lead with AWS Professional Services, where he helps customers build and deploy solutions using ML and generative AI on AWS. He has worked with customers across diverse industries, including software, finance, pharmaceutical, healthcare, IoT, and entertainment and media. In his free time, he enjoys reading, spending time with his family, and traveling.

## References

- [Evaluate large language models for quality and responsibility of LLMs](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-foundation-model-evaluate.html)
- [Generative AI Security Scoping Matrix](https://docs.aws.amazon.com/security/)
- [Amazon Bedrock documentation on LLM prompt design](https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-engineering.html)
- [Learn how to assess the risk of AI systems](https://aws.amazon.com/ai/responsible-ai/)
- [New RAG evaluation and LLM-as-a-judge capabilities in Amazon Bedrock](https://aws.amazon.com/blogs/machine-learning/)