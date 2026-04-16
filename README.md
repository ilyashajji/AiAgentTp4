🤖 LangChain AI Agent – Complete Project Guide

---

## ⚙️ Installation des dépendances

```bash
pip install langchain langchain-openai langchain-experimental langgraph python-dotenv tavily-python
📌 Explication

Ces bibliothèques permettent de construire des agents IA avancés :

langchain → framework principal pour les agents IA
langchain-openai → connexion aux modèles OpenAI (GPT-4o, GPT-4o-mini)
langchain-experimental → outils avancés (Python REPL, structured output, etc.)
langgraph → gestion de la mémoire et des graphes d’exécution
python-dotenv → chargement des variables d’environnement
tavily-python → moteur de recherche web pour les agents
🔐 Configuration des variables d’environnement

Créer un fichier .env :

OPENAI_API_KEY=your_api_key
TAVILY_API_KEY=your_api_key
📌 Explication

Les clés API permettent à l’agent de :

accéder aux modèles OpenAI
effectuer des recherches web via Tavily
📥 Chargement de l’environnement
from dotenv import load_dotenv
load_dotenv()
📌 Explication

Cette étape charge automatiquement les clés API depuis le fichier .env afin d’éviter de les écrire directement dans le code (sécurité).

🧠 Basic Agent (Agent simple)
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
📌 Explication

Cet agent :

utilise un modèle GPT-4o
répond directement aux messages utilisateur
n’a pas encore de mémoire ni d’outils
🔄 Dynamic Model Selection (Middleware)
from langchain.agents.middleware import ModelRequest, ModelResponse, wrap_model_call

basic_llm = ChatOpenAI(model="gpt-4o-mini")
advanced_llm = ChatOpenAI(model="gpt-4o")
📌 Explication

On définit deux modèles :

gpt-4o-mini → rapide et économique
gpt-4o → plus puissant et précis
🧠 Middleware de sélection dynamique
@wrap_model_call
def dynamic_model_selection(request: ModelRequest, handler) -> ModelResponse:
    env = request.runtime.context.get("env", "test")

    if env == "prod":
        model = advanced_llm
    else:
        model = basic_llm

    return handler(request.override(model=model))
📌 Explication

Ce middleware :

change automatiquement le modèle utilisé
selon l’environnement (test ou prod)
permet d’optimiser coût vs performance
💾 Memory with LangGraph
from langgraph.checkpoint.memory import InMemorySaver

memory = InMemorySaver()

agent = create_agent(
    model="openai:gpt-4o",
    checkpointer=memory
)

config = {"configurable": {"thread_id": "1"}}
📌 Explication
La mémoire permet à l’agent de se souvenir des conversations
thread_id identifie une session utilisateur
utile pour les chatbots persistants
🛠️ Tools (Fonctions externes)
🌦️ Weather Tool
from langchain.tools import tool

@tool
def get_weather(city: str):
    return {
        "city": city,
        "temperature": "25°C",
        "humidity": 80
    }

📌 Permet à l’agent de simuler des données météo.

👤 Employee Info Tool
@tool
def get_employee_info(employee_name: str):
    return {
        "name": employee_name,
        "salary": "5000$",
        "seniority": "5 years"
    }

📌 Permet de récupérer des informations RH simulées.

🌐 Web Search Tool (Tavily)
from langchain_tavily import TavilySearch

tavily = TavilySearch(max_results=5)

@tool
def search_web(query: str):
    return tavily.invoke({"query": query})
📌 Explication
Permet à l’agent de chercher sur Internet en temps réel
améliore fortement les réponses sur l’actualité
🤖 Tool-Enabled Agent
agent = create_agent(
    model="openai:gpt-4o",
    tools=[get_weather, get_employee_info, search_web],
    system_prompt="Use tools to answer questions"
)
📌 Explication

Cet agent peut :

appeler des fonctions externes
choisir automatiquement le bon outil
combiner IA + outils réels
🐍 Python Execution Tool
from langchain_experimental.tools import PythonREPLTool

repl_tool = PythonREPLTool()

agent = create_agent(
    model="openai:gpt-4o",
    tools=[repl_tool],
    system_prompt="Execute Python code when needed"
)
📌 Explication
Permet à l’agent d’exécuter du code Python
utile pour calculs, tri, data processing
🧩 Structured Output (Pydantic)
from pydantic import BaseModel
from langchain.agents.structured_output import ToolStrategy

class ContactInfo(BaseModel):
    name: str
    email: str
    phone: str
📌 Explication
Force l’IA à retourner un format structuré
utile pour extraction de données (CRM, parsing, etc.)
🔍 Middleware Hooks (Debugging)
from langchain.agents.middleware import AgentMiddleware
import time

class HooksDemo(AgentMiddleware):

    def before_agent(self, state, runtime):
        print("Before agent execution")

    def before_model(self, state, runtime):
        print("Before model call")

    def after_model(self, state, runtime):
        print("After model response")

    def after_agent(self, state, runtime):
        print("After agent finished")
📌 Explication

Ces hooks permettent de :

surveiller le cycle complet de l’agent
mesurer les performances
debug les appels LLM
📊 Fonctionnalités du projet

✔ Agents conversationnels
✔ Mémoire persistante
✔ Utilisation d’outils externes
✔ Recherche web en temps réel
✔ Exécution Python
✔ Middleware intelligent
✔ Extraction structurée de données

🚀 Résultat

Ce projet démontre une architecture complète d’agent IA moderne combinant :

🧠 Intelligence LLM (OpenAI GPT)
🛠️ Outils externes
💾 Mémoire conversationnelle
🔄 Middleware dynamique
📊 Output structuré
👤 Auteur

ILYAS HAJJI
