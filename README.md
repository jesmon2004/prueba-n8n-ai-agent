# Telegram RAG AI Agent 🤖🚀

Este repositorio contiene la configuración y arquitectura para desplegar un bot de Telegram inteligente con capacidades **RAG (Retrieval-Augmented Generation)** utilizando **n8n**, **Google Gemini** y **Pinecone**.

El agente es capaz de recordar el contexto de la conversación de forma individual por usuario y consultar documentos PDF indexados previamente en una base de datos vectorial para responder preguntas específicas.

---

## 📁 Archivos del Proyecto

El proyecto se divide en dos flujos de n8n independientes que debes importar en tu instancia:

### 1. `Document_Embedding_Process.json` (Fase de Ingesta)
Este es el flujo encargado del preprocesamiento de los datos. 
* Lee el documento PDF.
* Extrae el texto y lo divide en fragmentos manejables (chunks).
* Utiliza el modelo de Embeddings de Gemini para vectorizar el texto.
* Sube y almacena los vectores en el índice de **Pinecone**.

### 2. `telegram-rag-agent.json` (Fase Conversacional)
Este es el flujo principal del bot que interactúa con el usuario. Está compuesto por los siguientes nodos en una arquitectura avanzada de Agente de IA:
* **Telegram Trigger (`On message`)**: Escucha los mensajes entrantes en tiempo real.
* **AI Agent (Nodo Central)**: El "cerebro" del flujo. Coordina el modelo de lenguaje, la memoria y las herramientas.
   * *Input Prompt:* `={{ $json.message.text }}`
* **Google Gemini Chat Model (`gemini-2.5`)**: El modelo fundacional que procesa y genera las respuestas.
* **Simple Memory**: Proporciona memoria contextual.
   * *Session ID:* `={{ $json.message.from.id }}` (Garantiza que cada usuario tenga su propio historial aislado).
* **Vector Store Tool**: Herramienta que el Agente usa para buscar en los documentos.
   * *Pinecone Vector Store:* Conectado al índice donde reside el PDF.
   * *Embeddings Google Gemini:* Vectoriza la consulta del usuario en tiempo real.
* **Telegram Node (`Send Message`)**: Envía la respuesta generada de vuelta al usuario.

---

## 🌐 Despliegue en Entorno Local (Webhook Seguro)

Dado que Telegram requiere estrictamente una conexión cifrada (`https://`) y pública para enviar los mensajes mediante webhooks, y el servicio nativo de túneles de n8n v2 está discontinuado, se utiliza **Cloudflare Tunnels** de forma anónima y gratuita.

### Paso 1: Levantar el Túnel de Cloudflare
Ejecuta el siguiente comando en tu terminal local para exponer el puerto de n8n:
```bash
cloudflared tunnel --url http://localhost:5678
