# 🚀 Deploying a Gradio App on IBM Cloud Code Engine

A simple **Gradio** web app, containerized with **Docker** and deployed as a serverless application on **IBM Cloud Code Engine** with a public URL.

> Built as part of the IBM Skills Network lab *"Deploy your App with Code Engine"*.

---

## ✨ What the app does

A greeting app with two inputs:

- **Text box:** your name
- **Slider:** intensity, i.e. how many exclamation marks to add

Example: `Irem` + intensity `3` → `Hello, Irem!!!`

---

## 📁 Project Structure

```
.
├── demo.py           # Gradio app
├── requirements.txt  # Python dependencies
└── Dockerfile        # Container image definition
```

**demo.py**

```python
import gradio as gr

def greet(name, intensity):
    return "Hello, " + name + "!" * int(intensity)

demo = gr.Interface(
    fn=greet,
    inputs=["text", "slider"],
    outputs=["text"],
)

demo.launch(server_name="0.0.0.0", server_port=7860)
```

`server_name="0.0.0.0"` makes the app reachable from outside the container, which is required for Docker and Code Engine.

**Dockerfile**

```dockerfile
FROM python:3.10

WORKDIR /app
COPY requirements.txt requirements.txt
RUN pip3 install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "demo.py"]
```

`requirements.txt` is copied and installed before the rest of the code, so Docker can cache the dependency layer and rebuilds stay fast when only the code changes.

---

## 🛠️ Tech Stack

- Python 3.10
- Gradio 5.23.2
- Docker
- IBM Cloud Code Engine (serverless)
- IBM Cloud Container Registry

---

## ▶️ Run locally

**Without Docker**

```bash
pip install -r requirements.txt
python demo.py
```

**With Docker**

```bash
docker build -t myapp .
docker run -p 7860:7860 myapp
```

Then open http://localhost:7860

---

## ☁️ Deploy to IBM Cloud Code Engine

1. **Build the image** from the local source and push it to IBM Cloud Container Registry:

   ```bash
   ibmcloud ce buildrun submit --name myapp-build --source . --build-type local \
     --image us.icr.io/${SN_ICR_NAMESPACE}/myapp --registry-secret icr-secret --wait
   ```

2. **Create the application** from the image:

   ```bash
   ibmcloud ce application create --name demo1 \
     --image us.icr.io/${SN_ICR_NAMESPACE}/myapp --registry-secret icr-secret \
     --port 7860 --minscale 1
   ```

3. **Get the public URL:**

   ```bash
   ibmcloud ce application get --name demo1
   ```

`--minscale 1` keeps one instance always running, so the app doesn't have a cold start on the first request.

---

## 📚 What I learned

- The difference between container images and containers
- Writing a Dockerfile for a Python web app
- Testing a containerized app locally before deploying
- Building and pushing images to a container registry
- Deploying a serverless app on IBM Cloud Code Engine and exposing it with a public URL
