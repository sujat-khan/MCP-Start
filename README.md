# Expense Tracker MCP Server

A lightweight, local **Model Context Protocol (MCP)** server built with Python and [FastMCP](https://github.com/jlowin/fastmcp). It enables AI assistants (like Claude Desktop, Cursor, or custom LLM agents) to track personal expenses, categorize spending, and generate financial summaries directly using a local SQLite database.

---

## 📖 New to MCP? Start Here!

### What is the Model Context Protocol (MCP)?
Think of MCP like a **USB port for AI**. 

By default, an AI model only knows text. It cannot query your bank, read your local files, or update your spreadsheets. MCP gives the AI a standard, secure way to connect to external systems.

In MCP, there are two primary concepts used in this project:

1. **Tools (`@mcp.tool`)** — Functions the AI can execute.
   * *Example:* "Add an expense of $42 for groceries today." → The AI calls `add_expense(...)`.
2. **Resources (`@mcp.resource`)** — Data or files the AI can read for reference.
   * *Example:* "What subcategories exist under entertainment?" → The AI reads `expense://categories`.

---

## 📂 Project Structure

```text
Expense-Tracker-MCP/
├── main.py              # Core MCP server logic (tools, resources & SQLite schema)
├── categories.json      # Structured expense categories and subcategories
├── expenses.db          # SQLite database (auto-created on first run)
├── pyproject.toml       # Project metadata & dependencies
├── uv.lock              # Deterministic lockfile for reproducible installs
└── README.md            # Documentation and setup guide
```

---

## 🛠️ Features & Tools

### Available Tools

| Tool | Parameters | Description |
| :--- | :--- | :--- |
| `add_expense` | `date`, `amount`, `category`, `subcategory`, `note` | Inserts a new expense record into the SQLite database. |
| `list_expenses` | `start_date`, `end_date` | Retrieves all expenses between two dates (inclusive). |
| `summarize` | `start_date`, `end_date`, `category` (optional) | Aggregates and groups spending by category over a date range. |

### Available Resources

| URI | MIME Type | Description |
| :--- | :--- | :--- |
| `expense://categories` | `application/json` | Provides the full list of allowed categories & subcategories from `categories.json`. |

---

## 🚀 Getting Started

### Prerequisites

* **Python 3.12+**
* **[uv](https://docs.astral.sh/uv/)** (recommended package and environment manager)

If you don't have `uv` installed, install it in PowerShell:
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

---

### Step 1: Install Dependencies

Navigate into your project folder and sync the virtual environment:

```powershell
cd C:\Users\sujat\OneDrive\Desktop\Expense-Tracker-MCP
uv sync
```

---

### Step 2: Testing with FastMCP Inspector (Interactive Web UI)

FastMCP includes a built-in browser inspector to test your tools without needing an AI client configured:

```powershell
uv run fastmcp dev inspector main.py
```

> [!TIP]
> **Which browser URL to open?**
> * Open **`http://localhost:6274`** (or `http://127.0.0.1:6274`).
> * *Note:* You might see port `6275` mentioned in terminal logs. Port `6275/sandbox` is an internal background frame. Always access the dashboard through **`6274`**.

In the inspector:
1. Click **Connect** (Transport: `stdio`).
2. Go to the **Tools** tab and try running `add_expense`:
   * `date`: `2026-10-10`
   * `amount`: `25.50`
   * `category`: `food`
   * `subcategory`: `dining_out`
   * `note`: `Lunch with friends`
3. Check the **Resources** tab to view your category tree from `categories.json`.

---

### Step 3: Quick Terminal Checks

You can also inspect the server directly from your terminal:

* **List registered tools and resources:**
  ```powershell
  uv run fastmcp list main.py
  ```

* **Run the server in standard stdio mode:**
  ```powershell
  uv run fastmcp run main.py
  ```

---

## 🤖 Connecting to Claude Desktop (or Cursor / Other Clients)

To let an AI assistant actually manage your expenses using conversational language, configure your MCP client.

### Claude Desktop Configuration

1. Open your Claude configuration file:
   * Windows path: `%APPDATA%\Claude\claude_desktop_config.json`
2. Add your server under `mcpServers`:

```json
{
  "mcpServers": {
    "expense-tracker": {
      "command": "uv",
      "args": [
        "--directory",
        "C:\\Users\\sujat\\OneDrive\\Desktop\\Expense-Tracker-MCP",
        "run",
        "main.py"
      ]
    }
  }
}
```

3. Restart Claude Desktop.
4. You will see a hammer icon (🛠️) showing the Expense Tracker tools are active!

---

## 💬 Example Prompts to Ask Your AI

Once connected, you can talk naturally:

* *"I just bought groceries for $64.20. Please record that for today."*
* *"Show me all my expenses from this week."*
* *"How much have I spent on food vs utilities this month?"*
* *"What categories are available for transport?"*

---

## 💡 Troubleshooting & Common Gotchas

### 1. `'fastmcp' is not recognized as an internal or external command`
* **Why it happens:** The package is installed inside the project's virtual environment (`.venv`), which isn't in your global Windows PATH.
* **Fix:** Either prefix commands with `uv run` (e.g., `uv run fastmcp ...`) or activate the virtual environment first (`.venv\Scripts\activate`).

### 2. Blank screen on `127.0.0.1:6275/sandbox`
* **Why it happens:** The `/sandbox` route is an isolated iframe meant to be embedded by the inspector UI.
* **Fix:** Open **`http://localhost:6274`** instead.

### 3. Unknown command `"main.py"`
* **Why it happens:** In FastMCP 4.x, `dev` requires a subcommand.
* **Fix:** Use `uv run fastmcp dev inspector main.py` instead of `fastmcp dev main.py`.

---

## 📝 Customization

* **Add/Edit Categories:** Edit [`categories.json`](file:///c:/Users/sujat/OneDrive/Desktop/Expense-Tracker-MCP/categories.json). The resource reads the file on every request, so you don't even need to restart the server!
* **Database Location:** By default, data is stored locally in [`expenses.db`](file:///c:/Users/sujat/OneDrive/Desktop/Expense-Tracker-MCP/expenses.db). You can open it using any SQLite viewer (such as [DB Browser for SQLite](https://sqlitebrowser.org/)).

---

## 📜 License

MIT License. Feel free to modify and expand for your personal finance workflows!
