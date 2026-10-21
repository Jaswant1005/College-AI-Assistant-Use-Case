# College AI Assistant

An AI-powered academic learning assistant built with **Python**,
**Streamlit**, and the **Cohere Chat API**. The app lets students ask
academic questions, receive explanations in simple English, and continue
a conversation using chat history.

## Features

-   Chat interface for asking academic questions.
-   Simple-English explanations with examples and step-by-step guidance.
-   Conversation history maintained during the current app session.
-   Clear-conversation button in the sidebar.
-   Cohere-powered responses.
-   Can be run in Google Colab and exposed temporarily through an ngrok
    tunnel.

## Technology Stack

-   **Python** --- application logic
-   **Streamlit** --- web interface
-   **Cohere API / Python SDK** --- AI responses
-   **pyngrok** --- temporary public tunnel when running in Colab
-   **Google Colab** --- notebook environment used for development

## Project Structure

``` text
college-ai-assistant/
├── app.py          # Streamlit application
├── README.md       # Project documentation
└── logs.txt        # Optional Streamlit logs created during Colab execution
```

## Requirements

-   Python 3.10 or newer (use a Python version supported by the
    installed dependencies).
-   A Cohere API key with access to the selected chat model.
-   An ngrok account and authtoken if you want to expose the app from
    Colab.

## Setup and Run Locally

### 1. Clone or download the project

Place `app.py` in your project folder.

### 2. Install dependencies

``` bash
python -m pip install streamlit cohere
```

If you plan to create an ngrok tunnel, also install:

``` bash
python -m pip install pyngrok
```

### 3. Configure your API key securely

Do **not** hardcode API keys in `app.py`, commit them to Git, or publish
them in screenshots or notebooks.

For a local terminal session, set an environment variable.

**macOS / Linux:**

``` bash
export COHERE_API_KEY="your_cohere_api_key"
```

**Windows PowerShell:**

``` powershell
$env:COHERE_API_KEY="your_cohere_api_key"
```

Then update the app to read the key from the environment:

``` python
import os
import streamlit as st
import cohere

api_key = os.getenv("COHERE_API_KEY")
if not api_key:
    st.error("Cohere API key is missing. Set COHERE_API_KEY before starting the app.")
    st.stop()

client = cohere.ClientV2(api_key=api_key)
```

### 4. Start the application

``` bash
streamlit run app.py
```

Streamlit will print a local address, commonly `http://localhost:8501`.
Open it in your browser.

## Run in Google Colab

1.  Open the notebook in Google Colab.

2.  Install the required packages:

    ``` python
    %pip -q install streamlit cohere pyngrok
    ```

3.  Save the Streamlit application code to `app.py` using a notebook
    cell with `%%writefile app.py`.

4.  Store credentials using Colab Secrets or environment variables
    rather than writing them directly into the notebook. For example:

    ``` python
    import os
    from google.colab import userdata

    os.environ["COHERE_API_KEY"] = userdata.get("COHERE_API_KEY")
    os.environ["NGROK_AUTHTOKEN"] = userdata.get("NGROK_AUTHTOKEN")
    ```

    Add `COHERE_API_KEY` and `NGROK_AUTHTOKEN` in Colab's Secrets panel
    and grant the notebook access.

5.  Make sure `app.py` reads `COHERE_API_KEY` from the environment as
    shown above. Configure pyngrok with the token from the environment:

    ``` python
    from pyngrok import ngrok
    import os

    ngrok.set_auth_token(os.environ["NGROK_AUTHTOKEN"])
    ```

6.  Start Streamlit in the background and connect the tunnel:

    ``` python
    !streamlit run app.py --server.port 8501 --server.address 0.0.0.0 --server.headless true > logs.txt 2>&1 &
    ```

    ``` python
    from pyngrok import ngrok
    tunnel = ngrok.connect(8501, "http")
    print(tunnel.public_url)
    ```

The ngrok URL is temporary and may change when the tunnel is restarted.
Anyone who has the public URL may be able to access the app while the
tunnel is active, so do not use it to expose private data.

## How It Works

1.  The user enters a question in Streamlit's chat input.
2.  The question is added to the session's conversation history.
3.  The app sends the system instructions and conversation messages to
    Cohere's chat endpoint.
4.  The app extracts text from the response and displays it in the chat
    interface.
5.  The assistant's response is added to the session history so it can
    be used in follow-up questions.

## Model Configuration

The model name is specified in `app.py`. The model in the original
notebook is `command-a-plus-05-2026`. Confirm that this model identifier
is available to your Cohere account. If the API reports a model or
access error, choose a currently supported chat model from Cohere's
official documentation.

## Security Notes

-   **Rotate the Cohere API key and ngrok authtoken if they have been
    shared or published.** The notebook code originally contained
    credentials directly in the source. Revoke or replace those
    credentials in their respective dashboards, then update Colab
    Secrets or environment variables.
-   Never commit API keys to a public repository.
-   Consider adding usage limits, authentication, and other access
    controls before sharing a deployed app.
-   A Colab/ngrok tunnel is intended for temporary testing, not as a
    permanent production deployment.
-   Avoid sending sensitive student or personal information to the
    assistant.

## Troubleshooting

### Streamlit shows `missing ScriptRunContext`

This warning can appear when Streamlit code is run directly in a
notebook cell. Run the application with `streamlit run app.py` instead
of executing the app as ordinary Python code.

### The app does not open

-   Check `logs.txt` for startup errors.
-   Confirm that port `8501` is available and Streamlit started
    successfully.
-   Recreate the ngrok tunnel if the public URL no longer works.
-   Keep the Colab runtime running while testing the tunnel.

### Cohere API request fails

-   Verify that `COHERE_API_KEY` is set and valid.
-   Check that your Cohere account can access the configured model.
-   Review the error shown by the app and consult Cohere's current API
    documentation.
-   Do not print or share API keys while debugging.

## Future Improvements

-   Add subject-wise modes (for example, programming, databases, and
    networking).
-   Add PDF or notes upload with document-based question answering.
-   Add quiz generation and practice tests.
-   Add persistent chat history if required.
-   Add authentication and deploy to a suitable hosting service.

## License

No license has been specified yet. Add a license file before allowing
others to reuse or redistribute the project.
