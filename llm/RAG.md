# RAG

RAG（Retrieval-Augmented Generation，检索增强生成）是一种结合了**信息检索（Retrieval）** 和 **文本生成（Generation）** 的大语言模型应用架构，它的核心思想是：

> 在生成回答之前，先从外部知识源中检索相关信息，再将检索到的信息与问题一同喂给大语言模型进行生成，从而提升准确性与事实性。

---

## 1. 为什么需要 RAG？

大语言模型虽然强大，但有几个固有限制：

| 限制                    | RAG 提供的解决方案           |
| --------------------- | --------------------- |
| 模型知识是**静态的**（训练时定死）   | 实时检索外部信息（如数据库、文档、网页）  |
| 容易产生幻觉（Hallucination） | 给模型明确的上下文，让它只基于真实数据生成 |
| 无法记住**个人化/私有知识**      | 可以接入私有文档、知识库、用户笔记等    |

---

## 2. RAG 的典型流程（4 步）

1. **用户提问（Query）**

   > 比如：“公司的数据备份策略是怎样的？”

2. **Retriever：信息检索**

   > 系统从一个“知识库”中查找与问题最相关的文档段落（通常用向量检索，如 Faiss、Weaviate、Qdrant、Milvus）

3. **Augmentation：构造 Prompt**

   > 将用户问题与检索结果一起拼成上下文，喂给语言模型
   > 示例 Prompt：

   ```
   已知资料：
   文档片段1：公司每晚11点进行数据备份，备份存储于S3……
   问题：公司的数据备份策略是怎样的？
   ```

4. **LLM：生成答案**

   > 模型根据检索结果生成更加准确、符合事实的回答

---

### 3. RAG 架构常用组件

| 组件              | 技术栈                                              |
| --------------- | ------------------------------------------------ |
| 嵌入模型（Embedding） | OpenAI Embedding、BGE、E5、GTE 等                    |
| 向量数据库           | Faiss、Qdrant、Weaviate、Milvus、Chroma              |
| 大语言模型           | GPT-4、LLaMA、Mistral、ChatGLM、Gemma 等              |
| 框架              | LangChain、LlamaIndex、Haystack、llama-cpp + custom |

---
