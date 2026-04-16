# 🤖 AI Agent Project – LangChain / LangGraph

## 📌 Overview

This project demonstrates how to build **advanced AI agents** using:

- LangChain Agents (`create_agent`)
- LangGraph memory system
- OpenAI GPT models (`gpt-4o`, `gpt-4o-mini`)
- Middleware (dynamic routing, hooks, prompts)
- Tool usage (weather, employee info, web search, Python execution)
- Structured output extraction (Pydantic models)

The goal is to build **flexible, tool-augmented AI agents** capable of reasoning, memory, and external tool usage.

---

## ⚙️ Installation

```bash
pip install langchain langchain-openai langchain-experimental langgraph python-dotenv tavily-python
🔐 Environment Variables

Create a .env file:

OPENAI_API_KEY=your_api_key
TAVILY_API_KEY=your_api_key

Load environment:

from dotenv import load_dotenv
load_dotenv()
🧠 Basic Agent
from langchain.agents import create_agent
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o", temperature=0)

agent = create_agent(
    model=llm,
    system_prompt="You are a helpful assistant"
)

response = agent.invoke({
    "messages": [
        {"role": "user", "content": "Hello, my name is Mohamed"}
    ]
})

print(response["messages"][-1].content)
🔄 Dynamic Model Selection (Middleware)

Switch between models depending on environment:

from langchain.agents.middleware import ModelRequest, ModelResponse, wrap_model_call

basic_llm = ChatOpenAI(model="gpt-4o-mini")
advanced_llm = ChatOpenAI(model="gpt-4o")

@wrap_model_call
def dynamic_model_selection(request: ModelRequest, handler) -> ModelResponse:
    env = request.runtime.context.get("env", "test")

    if env == "prod":
        model = advanced_llm
    else:
        model = basic_llm

    return handler(request.override(model=model))
💾 Memory with LangGraph
from langgraph.checkpoint.memory import InMemorySaver

memory = InMemorySaver()

agent = create_agent(
    model="openai:gpt-4o",
    checkpointer=memory
)

config = {"configurable": {"thread_id": "1"}}

👉 The agent remembers previous messages in the same thread.

🛠️ Tools (Function Calling)
🌦️ Weather Tool
from langchain.tools import tool

@tool
def get_weather(city: str):
    return {
        "city": city,
        "temperature": "25°C",
        "humidity": 80
    }
👤 Employee Info Tool
@tool
def get_employee_info(employee_name: str):
    return {
        "name": employee_name,
        "salary": "5000$",
        "seniority": "5 years"
    }
🌐 Web Search Tool (Tavily)
from langchain_tavily import TavilySearch

tavily = TavilySearch(max_results=5)

@tool
def search_web(query: str):
    return tavily.invoke({"query": query})
🤖 Tool-Enabled Agent
agent = create_agent(
    model="openai:gpt-4o",
    tools=[get_weather, get_employee_info, search_web],
    system_prompt="Use tools to answer questions"
)
🐍 Python Execution Tool
from langchain_experimental.tools import PythonREPLTool

repl_tool = PythonREPLTool()

agent = create_agent(
    model="openai:gpt-4o",
    tools=[repl_tool],
    system_prompt="Execute Python code when needed"
)
🧩 Structured Output (Pydantic)
from pydantic import BaseModel
from langchain.agents.structured_output import ToolStrategy

class ContactInfo(BaseModel):
    name: str
    email: str
    phone: str

agent = create_agent(
    model="openai:gpt-4o-mini",
    response_format=ToolStrategy(ContactInfo)
)

👉 Extract structured data automatically from text.

🔍 Middleware Hooks (Debugging)
from langchain.agents.middleware import AgentMiddleware
import time

class HooksDemo(AgentMiddleware):

    def before_agent(self, state, runtime):
        print("Before agent")

    def before_model(self, state, runtime):
        print("Before model")

    def after_model(self, state, runtime):
        print("After model")

    def after_agent(self, state, runtime):
        print("After agent")
📊 Example Features Implemented

✔ Conversation memory
✔ Tool calling (weather, employee, web search)
✔ Dynamic model routing
✔ Structured extraction
✔ Python execution agent
✔ Middleware debugging hooks

🚀 Result

This project demonstrates a production-style AI agent architecture combining:

LLM reasoning (OpenAI GPT models)
Tool usage
Memory persistence
Middleware control layer
Structured outputs

## ILYAS HAJJI 
