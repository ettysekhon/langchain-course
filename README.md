# LangChain Course

Welcome to the LangChain course by Aurelio AI!

## Getting Started

### Python Environment (IMPORTANT)

This course repo contains everything you need to install an exact duplicate Python environment as used during the course creation.

#### Installing Python Venvs

The Python packages are managed using the [uv](https://github.com/astral-sh/uv) package manager, and so we must install `uv` as a prerequisite for the course. We do so by following the [installation guide](https://docs.astral.sh/uv/#getting-started). For Mac users, as of 22 Oct 2024 enter the following in your terminal:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Once `uv` is installed and available in your terminal you can navigate to the course root directory and execute:

```bash
uv python install 3.12.7
uv venv --python 3.12.7
uv sync
```

> ❗️ You may need to restart the terminal if the `uv` command is not recognized by your terminal.

With that we have our chapter venv installed. When working through the code for a specific chapter, always create a new venv to avoid dependency hell.

#### Using Venv in VS Code / Cursor

To use our new venv in VS Code or Cursor we simply execute:

```bash
cd example-chapter
cursor .  # run via Cursor
code .    # run via VS Code
```

This command will open a new code window, from here you open the relevant files (like Jupyter notebook files), click on the top-right **Select Environment**, click **Python Environments...**, and choose the top `.venv` environment provided.

#### Uninstalling Venvs

Naturally, we might not want to keep all of these venvs clogging up the memory on our system, so after completing the course we recommend removing the venv with:

```bash
deactivate
rm -rf .venv -r
```

### Ollama

The course can be run using OpenAI or Ollama. If using Ollama, you must go to [ollama.com](https://ollama.com/) and install Ollama for your respective OS (MacOS is recommended).

Whenever an LLM is used via Ollama you must:

1. Ensure Ollama is running by executing `ollama serve` in your terminal or running the Ollama application. Make sure to keep note of the port the server is running on, by default Ollama runs on `http://localhost:11434`

2. Download the LLM being used in your current example using `ollama pull`. For example, to download Llama 3.2 3B, we execute `ollama pull llama 3.2:3b` in our terminal.

### Fixes

Please see additional fixes. When running locally with `uv` there are some missing dependencies - these have been updated on the `pyproject.toml`. Just `uv sync` as per normal.

There is a bug related to the quality parameter on the `DallEAPIWrapper`. A [langchain-community issue](https://github.com/langchain-ai/langchain-community/pull/179) was raised and fixed on this [commit 2 days ago](https://github.com/langchain-ai/langchain-community/commit/22e4798aa127933c82b622612abb3617b70f3999). You can simply use updated wrapper here which removes the quality parameter.

```python
class FixedDallEAPIWrapper(DallEAPIWrapper):
    def run(self, query: str) -> str:
        if self.client is None:
            self.validate_environment()

        # Assume OpenAI v1, strip "quality"
        kwargs = {
            "prompt": query,
            "n": self.n,
            "size": self.size,
            "model": self.model_name,
            # No 'quality'
        }

        response = self.client.generate(**kwargs)
        image_urls = self.separator.join([item.url for item in response.data])
        return image_urls
```
