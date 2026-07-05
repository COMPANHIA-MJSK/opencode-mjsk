# LLM Agents Skill

## Purpose
Expert LLM integration, RAG systems, AI agents, and prompt engineering. Covers building production-grade AI applications with OpenAI, Anthropic, and open-source models.

## LLM Integration

### OpenAI API
```python
from openai import OpenAI

client = OpenAI()

# Chat completion
response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Explain quantum computing"}
    ],
    temperature=0.7,
    max_tokens=1000
)

# Function calling
tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "Get weather for a location",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {"type": "string"}
            },
            "required": ["location"]
        }
    }
}]
```

### Anthropic Claude API
```python
import anthropic

client = anthropic.Anthropic()

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Hello, Claude"}
    ]
)
```

## RAG (Retrieval-Augmented Generation)

### Vector Store Setup
```python
from langchain.vectorstores import Chroma
from langchain.embeddings import OpenAIEmbeddings
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.document_loaders import DirectoryLoader

# Load documents
loader = DirectoryLoader('./docs', glob="**/*.md")
documents = loader.load()

# Split text
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    length_function=len,
)
chunks = text_splitter.split_documents(documents)

# Create vector store
vectorstore = Chroma.from_documents(
    chunks,
    OpenAIEmbeddings(),
    persist_directory="./chroma_db"
)

# Query
retriever = vectorstore.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 3}
)
results = retriever.get_relevant_documents("How to deploy?")
```

### RAG Chain
```python
from langchain.chat_models import ChatOpenAI
from langchain.chains import RetrievalQA
from langchain.prompts import PromptTemplate

prompt = PromptTemplate(
    template="""Use the following context to answer the question.
Context: {context}
Question: {question}
Answer:""",
    input_variables=["context", "question"]
)

llm = ChatOpenAI(model="gpt-4", temperature=0)
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    chain_type="stuff",
    retriever=retriever,
    chain_type_kwargs={"prompt": prompt}
)

answer = qa_chain.run("How to deploy?")
```

## AI Agents

### Tool-Use Agent
```python
from langchain.agents import initialize_agent, Tool
from langchain.llms import OpenAI

def search_web(query: str) -> str:
    # Implement web search
    return f"Results for: {query}"

def calculate(expression: str) -> str:
    return str(eval(expression))

tools = [
    Tool(
        name="WebSearch",
        func=search_web,
        description="Search the web for information"
    ),
    Tool(
        name="Calculator",
        func=calculate,
        description="Calculate mathematical expressions"
    )
]

llm = OpenAI(temperature=0)
agent = initialize_agent(
    tools, llm, agent="zero-shot-react-description",
    verbose=True
)

agent.run("What is the capital of France?")
```

### Multi-Agent System
```python
from langchain.agents import AgentExecutor, create_openai_tools_agent
from langchain.memory import ConversationBufferMemory

# Define agents
researcher_agent = create_openai_tools_agent(
    llm=researcher_llm,
    tools=research_tools,
    prompt=researcher_prompt
)

coder_agent = create_openai_tools_agent(
    llm=coder_llm,
    tools=coding_tools,
    prompt=coder_prompt
)

# Orchestrate
class Orchestrator:
    def __init__(self):
        self.agents = {
            "researcher": researcher_agent,
            "coder": coder_agent
        }
    
    async def execute(self, task):
        if "research" in task.lower():
            return await self.agents["researcher"].arun(task)
        elif "code" in task.lower():
            return await self.agents["coder"].arun(task)
```

## Prompt Engineering

### System Prompts
```python
system_prompt = """You are an expert software engineer.

Rules:
1. Always write clean, maintainable code
2. Follow SOLID principles
3. Include error handling
4. Add comments for complex logic
5. Use type hints

When writing code:
- Use descriptive variable names
- Keep functions small and focused
- Write tests for critical paths
"""
```

### Chain-of-Thought
```python
cot_prompt = """Answer the question step by step.

Question: {question}

Let me think through this:
1. First, I need to understand...
2. Then, I should consider...
3. Finally, I can conclude...

Answer: """
```

## Best Practices
- Implement proper error handling for API calls
- Use streaming for better UX
- Cache LLM responses when appropriate
- Implement rate limiting
- Monitor token usage and costs
- Use proper prompt templates
- Implement guardrails for safety
- Log all LLM interactions for debugging
