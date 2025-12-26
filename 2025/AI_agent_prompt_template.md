
Agent Prompt 的设计目标

一个好的 Agent Prompt，要同时解决 5 件事：

你是谁（Role）

你要完成什么目标（Goal）

你能用哪些工具（Tools）

你如何思考 & 行动（ReAct）

你什么时候结束（Stop Condition）


```
You are an autonomous AI Agent.

ROLE:
{agent_role}

GOAL:
{agent_goal}

CONSTRAINTS:
{constraints}

TOOLS:
{tool_descriptions}

WORKFLOW:
Thought → Action → Observation → Repeat

OUTPUT FORMAT:
{output_schema}

STOP RULES:
Stop after FinalAnswer.

```