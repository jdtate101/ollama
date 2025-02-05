# Ollama RAG application with Deepseek-R1 on k8s

This is a basic kubernetes example of a Retrieval-Augmented Generation (RAG) application using ollama, langchain and streamlit. I didn't write the python, I just adapted the work from:

https://github.com/paquino11/chatpdf-rag-deepseek-r1

Into a working k8s deployment. I've used the oc commandline (RedHat OpenShift), but kubectl will work just as well for other distributions. 

## Instructions

Create namespace for ollama
```
oc create ns ollama
```

add helm repo

```
helm repo add ollama-helm https://otwld.github.io/ollama-helm/

helm repo update
```

download values from this repo and adjust for persistence and include GPU if you have one (recommended)

```
helm install ollama ollama-helm/ollama -f values.yaml -n ollama
```

Next download and create the configmap in the ollama namespace (called script-config-map.yaml) and apply it:

```
oc apply -f script-config-map.yaml -n ollama
```

After the Ollama deployment comes up you need to adjust it to match the deployment example, specifically the volumeMounts and lifecycle sections:

```
lifecycle:
          postStart:
            exec:
              command:
                - /bin/sh
                - -c
                - |
                  while ! /bin/ollama ps > /dev/null 2>&1; do
                    sleep 5
                  done
                  echo "deepseek-r1:latest" | xargs -n1 /bin/ollama pull
                  echo "mxbai-embed-large" | xargs -n1 /bin/ollama pull
                  /bin/sh /scripts/post-start.sh
```

```
volumeMounts:
        - mountPath: /root/.ollama
          name: ollama-data
        - name: script-volume
          mountPath: /scripts
```

This will run a set of additional requirements installs post startup of the pod. It's recommended to scale down and scale up the replicas to reflect this change.

Next we create a clusterIP service for the deployment selector:

```
oc apply -f rag-service.yaml -n ollama
```

Once this is up you can create a route/ingress to point to the new RAG service and you should have access to the application. 


