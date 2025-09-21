---
title: 利用认知工具激发LLM的推理能力
date: 2025-07-08 09:44:35
tags:
- agent
- 提示词

excerpt: 将认知心理学中的模块化认知操作理论引入现代LLM中，通过赋予模型一组‘认知工具’来执行特定的推理操作。这些工具由LLM自身实现，并在工具调用框架下进行序列化执行。这种方法与当前主流的链式思维或强化学习不同，强调通过结构化方式激发LLM的潜在推理能力

categories: 文献阅读
thumbnail: https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250708163522345.png?imageSlim
---
{% notel blue  %}

- Paper：[Eliciting Reasoning in Language Models with Cognitive Tools](https://arxiv.org/abs/2506.12115)
- Medium上的[分析](https://cobusgreyling.medium.com/defining-cognitive-tools-to-make-language-models-reason-96986342bf46)

{% endnotel %}

传统的Agent框架中，工具是与外部世界连接的窗口——比如MCP服务器、API等

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250708154717876.png?imageSlim"/>

这篇IBM的研究模仿 AI 代理的模块化架构，将认知心理学中的**模块化认知操作理论引入现代LLM**中，通过赋予模型一组认知工具来执行特定的推理操作。这些工具由LLM自身实现，并在工具调用框架下进行**序列化执行**。

这种方法与当前主流的链式思维或强化学习不同，强调**通过结构化方式激发LLM的潜在推理能力**

### What is Cognitive Tools

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250708161652611.png?imageSlim"/>

通过将推理阶段封装到模块化的提示驱动工具中，将认知架构中的内部认知操作重新表述为现代工具调用代理框架中的工具，主要分为：

- **Understand Question**：将问题分解为其核心组成部分，识别关键概念、变量和相关定理
- **Recall Related**：检索类似问题和它们的解决方案，通过例子指导推理
- **Examine Answer**：通过self-reflexion的形式检查答案正确性，包括可能的缺陷、错误的假设与计算，以及没有考虑在内的约束

  - 同时使用解决当前问题的思路检验类似的问题，判断正确性
- **Backtracking** ：识别推理过程中存在缺陷的步骤，并给出替代的解决方案，类似于在解决问题时探索新的路径。

这几个工具都作为*prompt-driven*的模块运行，由同一个LLM执行；工具的输出结果被反馈到主要的推理循环中，使模型能够动态地优化其方法

论文中给出了各个工具详细的提示词，在此给出一例：

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250708162447108.png?imageSlim"/>

#### Compared With Cognitive Prompting

**Cognitive Prompting**： 通过模拟人类认知过程中的结构化思维操作，来引导大型语言模型（LLMs）解决复杂问题的新型提示方法

> 相关的[Paper]()

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250708162243132.png?imageSlim"/>

认知提示的特点是结构化提示，暗示与引导LLM进行分步思考，但**缺乏显式模块化**

IBM的研究认为他们的认知工具通过参考认知提示的经验，在效果上超越了后者：

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250708162811108.png?imageSlim"/>

### Why Cognitive Tools Matters

> The introduction of cognitive tools addresses a critical limitation of traditional prompting methods, such as flat prompts or monolithic chain-of-thought (CoT) approaches.

引入认知工具解决了传统提示方法，如扁平提示或整体式思维链（CoT）方法的关键局限性

#### The shortcomings of traditional CoT

**CoT** (chain-of-thought)  通过在提示词中加入类似于“让我们一步步思考”的指令，鼓励LLM生成推理的中间步骤

- **缺乏灵活性和适应性**：虽然CoT能够展现推理过程，但是作为预设的、线性的生成过程，难以回溯纠正过程中存在的错误，缺乏中间阶段的自我纠正能力；同理，对于需要反复审视、多角度分析、甚至回溯重试的问题，单一的CoT很难有效地管理这些复杂的逻辑流
- 另外，**黑盒问题依旧存在**：尽管CoT展示了推理步骤，但这些步骤仍然是LLM内部生成的结果，我们无法直接控制或干预其中的特定认知操作。如果CoT中出现了推理错误，我们也很难知道具体是哪个“认知功能”出了问题；

#### Improvements in cognitive tools

- **引入模块化和显式控制**：
  - 将复杂任务从一个黑盒分解为人类思考时经历的一系列可识别、独立的认知操作 **cognitive operation**
  - 将这些操作封装成独立的、可调用的“工具”。每个工具专注于完成一个特定的认知子任务
- **实现动态编排和自我修正**：主LLM可以根据需要，动态地选择和调用这些工具
  - 这使得推理过程不再是线性的，而是可以进行分支、循环、回溯和自我修正。
  - 例如，当LLM发现推理有误时，可以显式调用“检查答案”工具进行反思，然后调用“回溯”工具返回并重新尝试
- **提高可解释性和鲁棒性**：通过将推理分解为模块化的步骤，并赋予LLM自主调用这些工具的能力，使得推理过程更加透明和可控，也更容易纠错和优化，从而显著提高了LLM在复杂推理任务上的性能和鲁棒性

#### Other features

> By compartmentalising reasoning steps, cognitive tools reduce interference between operations, enabling clearer and more focused problem-solving.

1. 通过将推理步骤模块化，认知工具**减少了操作之间的干扰**，从而实现更清晰和专注的问题解决

> First, modularity helps the LLM focus on implementing the specific cognitive operation at hand, in isolation from the rest of the context window that has been provided so far

2. 模块化的设计有助于LLM专注于当前的任务，**避免受到长上下文的干扰**

#### Comparisons with baseline

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250708161105427.png?imageSlim"/>

与flat prompts进行比较，cognitive tools方法下的回答正确率都有提高

### 思考与总结

#### 启示

除了强化学习，还有其他方法可以激活LLM的推理能力，比如本文的**modular cognitive tools**，就证明了在高难度推理任务上的可行性

从 AI 代理的角度来看，这种方法弥补了传统工具调用（依赖外部 API 和函数）与模块化内部推理需求之间的gap

认知工具的发现被认为与情境学习同等重要且意义重大，尤其对于需要结构化推理和透明度的任务

> The discovery of **cognitive tools** can be argued to be as important and significant as **in-context learning**, particularly for tasks requiring structured reasoning and transparency

#### 更多问题

文章没有给出主LLM内部的提示词，给出Gemini拟合的参考：

```bash
You are an advanced AI assistant designed to solve complex problems by orchestrating a set of specialized cognitive tools. Your primary goal is to accurately and comprehensively answer the user's query by breaking it down, analyzing it deeply, utilizing available resources, and ensuring the correctness of your reasoning.

**Here are the cognitive tools at your disposal, and their functions:**

1.  **Understand Question (`understand_question`)**:
    * **Purpose**: To thoroughly analyze and deconstruct a complex problem, identify key components, extract known information, and recognize potential methodologies.
    * **Input**: `problem_statement` (The full problem text).
    * **Output**: Structured analysis including sub-problems, key concepts, extracted data, and suggested approaches.

2.  **Recall Related (`recall_related`)**:
    * **Purpose**: To retrieve and leverage relevant prior knowledge, similar problems, related theorems, formulas, or common solution patterns that could aid in the current problem-solving process.
    * **Input**: `current_context` (Current problem state or analysis).
    * **Output**: List of recalled knowledge, similar examples, applicable theories, and common pitfalls.

3.  **Examine Answer (`examine_answer`)**:
    * **Purpose**: To meticulously review a proposed solution or ongoing reasoning chain for logical consistency, erroneous assumptions, calculation errors, or unaddressed constraints.
    * **Input**: `reasoning_chain` (The step-by-step reasoning process), `current_answer` (The derived answer or intermediate result).
    * **Output**: Detailed critique highlighting issues, missing elements, or confirming correctness.

4.  **Backtracking (`backtracking`)**:
    * **Purpose**: To revert to a previous correct or promising state in the reasoning process when the current path is deemed incorrect or unproductive, and to suggest alternative approaches from that point.
    * **Input**: `error_report` (Details from 'examine_answer' or other identified errors), `last_valid_state` (The point in the reasoning chain to return to).
    * **Output**: Identification of the backtrack point, analysis of the error's root cause, and suggestions for new strategies.

**Your Operating Protocol (Think-Act Loop):**

1.  **Thought**: Carefully consider the current problem, the progress made so far, and the output from the last tool. Determine the most logical next step. Are you trying to understand the problem better? Recall relevant information? Generate a solution? Or verify your work?
2.  **Action**: Based on your 'Thought', choose *one* of the available tools and call it with the appropriate parameters. If you believe the problem is fully solved and verified, state "FINISH" followed by your final answer.

**Example Action Format:**
`Action: understand_question(problem_statement="Solve the equation x^2 + 2x - 3 = 0")`
`Action: examine_answer(reasoning_chain="Step 1:...", current_answer="x=1, x=-3")`
`Action: FINISH: [Your Final Answer Here]`

**Initial Problem:**
[USER'S ORIGINAL PROBLEM STATEMENT GOES HERE]
```

