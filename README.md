# 🤖 AI Agent RAG Architecture con n8n y Telegram

Este repositorio documenta la construcción de un Agente de Inteligencia Artificial utilizando la arquitectura RAG (Retrieval-Augmented Generation), automatizado completamente a través de un servidor local de n8n desplegado en Docker.

## 🛠️ Fase 1: Pipeline de Vectorización (Data Ingestion)

El archivo `Document_Embedding_Process.json` contiene el flujo encargado de procesar la base de conocimiento del bot.

**Flujo técnico:**
1. Extracción de texto plano a partir de documentos PDF.
2. Fragmentación semántica (*Chunking*) configurada con un tamaño de 500 y un solapamiento (*overlap*) de 50 para preservar el contexto de las frases.
3. Generación de Embeddings utilizando la API de Google Gemini (`models/embedding-001`).
4. Almacenamiento en base de datos vectorial (Pinecone).

**💡 Resolución de problemas técnicos:**
Inicialmente, la base de datos vectorial se instanció con 1536 dimensiones (estándar de OpenAI). Sin embargo, al pivotar el motor de IA hacia Google Gemini para optimizar costes, se detectó un error de incompatibilidad de tensores: el modelo `embedding-001` genera vectores mucho más densos (3072 dimensiones). 
La solución implementada fue la reestructuración completa del índice de Pinecone a 3072 dimensiones, permitiendo una inserción exitosa de los datos.
