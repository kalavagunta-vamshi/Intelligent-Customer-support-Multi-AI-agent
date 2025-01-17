# Intelligent Customer Support System

This project implements an intelligent customer support workflow using the LangGraph framework and Gradio for an interactive interface. The system is designed to categorize customer queries, analyze their sentiment, and provide appropriate responses based on the query type and sentiment. It uses a state graph to manage the workflow logic.

---

## Features

1. **Query Categorization**:
   - Classifies queries into one of three categories: `Technical`, `Billing`, or `General`.
2. **Sentiment Analysis**:
   - Analyzes the sentiment of the query: `Positive`, `Neutral`, or `Negative`.
3. **Dynamic Routing**:
   - Routes queries based on category and sentiment.
   - Escalates queries with `Negative` sentiment to a human agent.
4. **Custom Responses**:
   - Provides tailored responses for technical, billing, and general queries.
5. **Interactive Interface**:
   - Gradio-based GUI for user interaction.
6. **Visualization**:
   - Displays the workflow as a state graph using Mermaid diagrams.

---

## Project Structure

### Main Components

1. **Categorization and Sentiment Analysis**:
   - Categorizes queries and determines sentiment using a language model (Groq-based Llama model).

2. **Routing and Escalation**:
   - Routes queries to appropriate handlers based on category and sentiment.

3. **Handlers**:
   - **Technical Support**: Provides responses to technical queries.
   - **Billing Support**: Addresses billing-related queries.
   - **General Support**: Handles general queries.
   - **Escalation**: Escalates queries with negative sentiment to human agents.

4. **Interactive App**:
   - A Gradio-based interface for query input and response display.

---

## Prerequisites

- Python 3.9+
- Installed dependencies:
  - `langgraph`
  - `langchain-core`
  - `langchain-groq`
  - `gradio`

Install the dependencies using:
```bash
pip install langgraph langchain-core langchain-groq gradio
```

---

## How It Works

### Workflow Logic

1. **Categorization**:
   - Classifies the customer query into `Technical`, `Billing`, or `General` categories.

2. **Sentiment Analysis**:
   - Analyzes the sentiment as `Positive`, `Neutral`, or `Negative`.

3. **Routing**:
   - Routes the query based on the category and sentiment.
   - Negative sentiment queries are escalated.

4. **Response Generation**:
   - Custom responses are generated for each query type.

### Workflow Visualization

The workflow is represented as a state graph using Mermaid diagrams, which can be rendered as a PNG.

---

## Usage

### Running the Application

1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd <repository-name>
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Run the application:
   ```bash
   python app.py
   ```

### Using the Gradio Interface

- Open the provided URL in a web browser.
- Enter a query in the text box.
- View the categorized response, sentiment analysis, and generated reply.

---

## Key Code Functions

### Categorization
```python
def categorize(state: State) -> State:
    """Categorize the query."""
    prompt = ChatPromptTemplate.from_template(
        "Categorize the following customer query into one of these categories: "
        "Technical, Billing, General. Query: {query}"
    )
    chain = prompt | llm
    category = chain.invoke({"query": state["query"]}).content
    return {"category": category}
```

### Sentiment Analysis
```python
def analyze_sentiment(state: State) -> State:
    """Analyze sentiment of the query."""
    prompt = ChatPromptTemplate.from_template(
        "Analyze the sentiment of the following customer query "
        "Response with either 'Positive', 'Neutral', or 'Negative'. Query: {query}"
    )
    chain = prompt | llm
    sentiment = chain.invoke({"query": state["query"]}).content
    return {"sentiment": sentiment}
```

### Routing
```python
def route_query(state: State) -> State:
    """Route query based on category and sentiment."""
    if state["sentiment"] == "Negative":
        return "escalate"
    elif state["category"] == "Technical":
        return "handle_technical"
    elif state["category"] == "Billing":
        return "handle_billing"
    else:
        return "handle_general"
```

---

## Gradio Integration
```python
def run_customer_support(query: str) -> Dict[str, str]:
    results = app.invoke({"query": query})
    return {
        "Category": results['category'],
        "Sentiment": results['sentiment'],
        "Response": results['response']
    }

gui = gr.Interface(
    fn=gradio_interface,
    theme='Yntec/HaleyCH_Theme_Orange_Green',
    inputs=gr.Textbox(lines=2, placeholder="Enter your query here..."),
    outputs=gr.Markdown(),
    title="Intelligent Customer Support",
    description="Provide a query and receive a categorized response. The system analyzes sentiment and routes to the appropriate support channel.",
)

gui.launch()
```








