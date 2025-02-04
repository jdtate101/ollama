# ollama

This is a basic kubernetes example of a Retrieval-Augmented Generation (RAG) application using ollama, langchain and streamlit. I didn't write the python, I just adapted the work from:

https://github.com/paquino11/chatpdf-rag-deepseek-r1

Into a working k8s deployment.

## Instructions

---Create namespace for ollama---
oc create ns ollama

add helm repo

helm repo add ollama-helm https://otwld.github.io/ollama-helm/
helm repo update

download values https://github.com/otwld/ollama-helm/blob/main/values.yaml
and adjust for persistence and include GPU if you have one (recommended)



helm install ollama ollama-helm/ollama -n ollama
![image](https://github.com/user-attachments/assets/782b5a7e-d4b5-4639-9e8b-bc3fe69f2d23)
