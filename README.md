# 📚 RAG mínimo — políticas internas

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Sentence Transformers](https://img.shields.io/badge/Embeddings-Sentence%20Transformers-blue?style=flat-square)
![Qwen](https://img.shields.io/badge/LLM-Qwen2.5%200.5B-purple?style=flat-square)
![Academic project](https://img.shields.io/badge/Scope-Academic%20practice-green?style=flat-square)

Proyecto académico de práctica de **Retrieval-Augmented Generation (RAG)** con datos simulados. Responde preguntas sobre políticas internas de vacaciones y formación a partir de documentos filtrados por país y nivel de acceso.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/xfalconix/rag-internal-policies/blob/main/RAG_minimo_politicas_internas.ipynb)

---

## 🎯 Objetivo

Comprender cada etapa de un RAG sin ocultarla detrás de un framework: seleccionar documentos permitidos, generar embeddings, recuperar contexto y producir una respuesta con un modelo de lenguaje.

El perfil del usuario se **simula con un diccionario**. Se practica autorización mediante filtros; no se implementan autenticación, contraseñas ni SSO. Todos los documentos son ficticios y no representan normativa laboral real.

## ⚡ Flujo

```text
Documentos simulados + perfil de usuario
    → filtro por país y nivel de acceso
    → embeddings de los textos permitidos en memoria

Pregunta → embedding con el mismo encoder
    → similitud coseno → documento top-1
    → prompt con contexto → Qwen → respuesta
```

Cada documento ya es un texto corto y actúa como un fragmento. No se aplica chunking automático. El corpus tiene cuatro documentos; el perfil inicial permite consultar dos.

## 🛠 Stack y decisiones

| Componente | Elección | Motivo para esta práctica |
|---|---|---|
| Entorno | Python / Google Colab | Ejecutar e inspeccionar el flujo por celdas. |
| Encoder | `sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2` | Modelo multilingüe compacto, adecuado para experimentar con búsqueda semántica en español; vectores de 384 dimensiones. |
| Recuperación | Similitud coseno con scikit-learn | Hace visible cómo se selecciona el documento. |
| Almacenamiento vectorial | Array en memoria | Suficiente para este corpus pequeño; no requiere una base externa. |
| Generación | `Qwen/Qwen2.5-0.5B-Instruct` con Transformers | Modelo pequeño que sigue instrucciones y puede ejecutarse en el runtime de Colab sin una API de inferencia de pago. |

Estas elecciones priorizan sencillez y recursos modestos, no una superioridad demostrada frente a otros modelos. No se hace fine-tuning. No se utilizan ChromaDB ni LangChain.

## 🚀 Ejecución

1. Abrir el notebook mediante **Open in Colab**.
2. Ejecutar las celdas en orden, desde la instalación de dependencias.
3. Esperar la descarga inicial de los modelos desde Hugging Face. Se necesita conexión y espacio para los pesos.
4. Revisar los documentos permitidos, las similitudes y las respuestas finales.

La ejecución usa el cómputo del runtime, no un endpoint de inferencia externo. CPU puede servir para esta demo pequeña, aunque la generación será más lenta que con GPU. No requiere introducir claves de API para estos modelos públicos.

El notebook imprime las versiones instaladas, pero no incluye un entorno de dependencias bloqueado. Cambios de bibliotecas pueden requerir ajustes. Se publica **sin salidas ni contadores de ejecución** para evitar resultados obsoletos y metadatos de sesión.

## 🔎 Casos de prueba

| Consulta o comprobación | Qué se revisa |
|---|---|
| Perfil empleado / España | Solo se permiten `ES-VAC-01` y `ES-FOR-01`. |
| Perfil sin documentos | El filtro devuelve una lista vacía; no se debe continuar con una recuperación vacía. |
| Días de vacaciones | Recuperar la política de vacaciones; la respuesta esperada es 22 días laborables en este corpus ficticio. |
| Horas de formación | Recuperar la política de formación, que indica 20 horas anuales. |
| Salario mensual | No hay información suficiente: revisar que el LLM se abstenga. |

Son comprobaciones educativas, no un benchmark. La consulta de salario muestra un límite importante: `argmax()` devuelve un candidato incluso cuando ninguno responde a la pregunta. La instrucción de abstención del prompt no garantiza que el LLM siempre la cumpla. Los asserts de generación comprueban texto no vacío, no exactitud factual.

## 📌 Alcance y límites

- Permisos simulados con coincidencia exacta de país y rol, sin herencia entre roles.
- Si cambia el perfil, hay que volver a filtrar e indexar sus documentos permitidos.
- Sin autenticación real, despliegue web, persistencia vectorial ni versionado documental.
- Recuperación top-1, sin umbral calibrado ni reranking.
- Un documento recuperado no siempre respalda la respuesta; la puntuación no es una probabilidad.
- El modelo generativo pequeño puede omitir información o alucinar. El prompt no sustituye controles de seguridad.

Posibles ampliaciones: evaluación con más preguntas, top-k, abstención evaluada, índice persistente y autenticación real.

## 📁 Contenido

```text
.
├── RAG_minimo_politicas_internas.ipynb
└── README.md
```

## 📖 Referencias

- [MiniLM multilingüe — ficha del modelo](https://huggingface.co/sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2)
- [Qwen2.5-0.5B-Instruct — ficha del modelo](https://huggingface.co/Qwen/Qwen2.5-0.5B-Instruct)

---

Carlos Falconi · Práctica académica de RAG con datos simulados.
