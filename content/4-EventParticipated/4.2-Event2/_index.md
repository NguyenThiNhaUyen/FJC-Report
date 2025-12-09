---
title: "Event 2"
# date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
--- 
# Event Summary: “Building Agentic AI – Context Optimization with Amazon Bedrock”

### Event Objectives

The event aimed to provide an end-to-end overview of how to design and implement **Agentic AI systems** using **Amazon Bedrock**, combining architectural concepts with hands-on practices. The key objectives included:
- Presenting a structured journey for Agentic AI, from high-level architecture to practical implementation
- Introducing core concepts for building autonomous AI agents with Amazon Bedrock
- Sharing real-world use cases of agentic workflows on AWS
- Exploring agentic orchestration and **context optimization** strategies (L300 session)
- Creating opportunities for hands-on learning through workshops and professional networking

### Speakers

- **Nguyen Gia Hung** – Head of Solutions Architect  
- **Kien Nguyen** – Solutions Architect  
- **Viet Pham** – Founder & CEO  
- **Thang Ton** – Co-Founder & COO  
- **Henry Bui** – Head of Engineering  
- **Kha Van** – Community Leader  

### Key Content

#### Event Overview and Logistics
- **Date:** **December 5, 2025**  
- **Location:** **26th Floor, Bitexco Financial Tower**, Ho Chi Minh City, Vietnam  
- **Theme:** Building autonomous AI agents using Amazon Bedrock through practical use cases and hands-on experience

#### AWS Bedrock Agent Core

This session introduced the foundational building blocks required for designing agentic systems on Amazon Bedrock, including:
- Selecting appropriate foundation models for specific problem domains
- Integrating tools or functions that allow agents to take actions
- Applying guardrails and constraints to improve safety and reliability
- Using tracing and evaluation to prepare systems for production environments

#### Use Case: Building Agentic Workflows on AWS

Speakers demonstrated how agentic workflows can be applied to real-world scenarios:
- Agents can execute multi-step tasks rather than simple single-turn question–answer interactions
- Iterative loops of retrieval, reasoning, and action enable flexible agent behavior
- Human-in-the-loop checkpoints are essential for actions with high impact or risk

#### CloudThinker – Agentic Orchestration & Context Optimization (L300)

The L300 session focused on the importance of **context quality** in agentic systems:
- Context directly affects accuracy, latency, and operating costs
- Key approaches to context optimization include:
  - Maintaining minimal yet sufficient context to reduce noise and hallucinations
  - Separating short-term context from long-term memory or knowledge bases
  - Improving retrieval quality through metadata-based filtering (scope, recency, relevance)
  - Applying context policies such as allowlists, redaction, and access control

#### CloudThinker Hack: Hands-on Workshop

Participants were guided through building a complete agentic workflow:
- Defining the agent’s goal
- Designing task steps and orchestration logic
- Integrating tools and testing agent behavior
- Iterating to improve reliability and performance

The workshop also emphasized debugging techniques:
- Inspecting prompts and tool calls
- Verifying retrieval results to reduce unnecessary context
- Adding safety constraints and guardrails

#### Networking and Expert Discussions

The networking session enabled in-depth discussions on:
- Real-world deployment constraints such as security, governance, and cost control
- Observability practices including logging and tracing
- Quality evaluation strategies and integration into existing engineering workflows

### Key Takeaways

#### Agentic AI Mindset
- Agentic AI is more than a chatbot; it requires orchestration, tool usage, memory, and guardrails
- Context is a critical lever that determines both output quality and operational cost

#### Technical Approach
- Clear separation of responsibilities is essential:
  - Planner / Orchestrator
  - Tools and actions
  - Memory and retrieval
  - Guardrails and safety controls
- Context optimization reduces hallucinations and improves system controllability

#### Responsible Deployment
- Human-in-the-loop remains important for high-risk or high-impact actions
- Success metrics such as accuracy, latency, and cost must be clearly defined and continuously evaluated

### Practical Applications

Based on the event content, potential applications in real work environments include:
- Prototyping internal agents for:
  - Incident triage
  - Runbook-guided troubleshooting
  - Log summarization and analysis
- Applying context optimization techniques:
  - Tightening retrieval scope
  - Reducing prompt and context bloat
  - Using structured memory and validation checkpoints
- Strengthening governance:
  - Implementing guardrails for tool execution
  - Establishing approval workflows for critical actions
  - Enabling logging and tracing for compliance and debugging

### Event Experience

The event provided a practical, end-to-end perspective on building agentic AI systems, effectively connecting architectural design with real implementation challenges. The L300 session clearly demonstrated how context optimization improves system reliability and cost efficiency. The hands-on workshop reinforced best practices in designing, testing, and debugging agentic workflows. Networking discussions helped relate theoretical concepts to real-world operational constraints.

#### Lessons Learned
- Context quality is as important as model selection when building AI agents
- Agentic systems should be treated as production-grade systems with safety, auditability, and evaluation
- Human-in-the-loop is still necessary for autonomous actions with significant impact
- Rapid iteration through trace → diagnosis → refinement is key to improving agent behavior
