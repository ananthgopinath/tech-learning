Here is a detailed playbook for using Dapr StateStore with Redis in a Python application. This guide walks you through:

🧾 Overview
Goal: Build a Python application that uses Dapr's State Management API with Redis as the backend.

📦 Prerequisites
Install Dapr CLI

bash
Copy
Edit
brew install dapr/tap/dapr         # macOS
choco install dapr-cli             # Windows
Initialize Dapr (installs Redis by default for local testing)

bash
Copy
Edit
dapr init
Install Python requirements

bash
Copy
Edit
pip install dapr dapr-ext-grpc fastapi uvicorn
Ensure Redis is running locally
If not:

bash
Copy
Edit
docker run -d --name redis -p 6379:6379 redis
📁 Project Structure
plaintext
Copy
Edit
dapr-python-redis/
├── app.py
├── components/
│   └── statestore.yaml
└── requirements.txt
📄 1. components/statestore.yaml (Dapr Component Config)
yaml
Copy
Edit
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: statestore
  namespace: default
spec:
  type: state.redis
  version: v1
  metadata:
  - name: redisHost
    value: localhost:6379
  - name: redisPassword
    value: ""
✅ Name must be statestore to match your app logic.

🐍 2. app.py (Python App Using Dapr Client)
python
Copy
Edit
from dapr.clients import DaprClient
from fastapi import FastAPI
import uvicorn

app = FastAPI()
store_name = "statestore"

@app.post("/save/{key}")
async def save_state(key: str, value: str):
    with DaprClient() as client:
        client.save_state(store_name, key, value.encode('utf-8'))
    return {"status": "saved", "key": key, "value": value}

@app.get("/get/{key}")
async def get_state(key: str):
    with DaprClient() as client:
        result = client.get_state(store_name, key)
        if result.data:
            return {"key": key, "value": result.data.decode('utf-8')}
        else:
            return {"key": key, "value": None}

@app.delete("/delete/{key}")
async def delete_state(key: str):
    with DaprClient() as client:
        client.delete_state(store_name, key)
    return {"status": "deleted", "key": key}

if __name__ == "__main__":
    uvicorn.run("app:app", host="0.0.0.0", port=8000)
📦 3. requirements.txt
nginx
Copy
Edit
dapr
dapr-ext-grpc
fastapi
uvicorn
▶️ 4. Run the App with Dapr Sidecar
bash
Copy
Edit
dapr run \
  --app-id dapr-py-redis \
  --app-port 8000 \
  --components-path ./components \
  -- uvicorn app:app --port 8000
🔍 5. Testing API with curl or Postman
Save State
bash
Copy
Edit
curl -X POST http://localhost:8000/save/user1 -d "hello world"
Get State
bash
Copy
Edit
curl http://localhost:8000/get/user1
Delete State
bash
Copy
Edit
curl -X DELETE http://localhost:8000/delete/user1
📌 6. Notes
You can change Redis configuration in statestore.yaml to point to a remote Redis or Redis with password.

DaprClient() uses gRPC (faster); for HTTP, you can also use Dapr’s REST API via http://localhost:3500/v1.0/state/statestore.

🧪 7. Using Dapr REST (Optional)
Save state via Dapr HTTP API:
bash
Copy
Edit
curl -X POST http://localhost:3500/v1.0/state/statestore \
  -H "Content-Type: application/json" \
  -d '[{"key":"test", "value":"hello"}]'
Get state:
bash
Copy
Edit
curl http://localhost:3500/v1.0/state/statestore/test
🚀 8. Deploy to Production
If deploying on Kubernetes:

Redis as a Helm chart or managed Redis (like AWS ElastiCache).

Create a Kubernetes secret for Redis password.

Mount component YAML with proper Redis hostname.

Deploy Python app with Dapr annotations.

Would you like a version of this as a GitHub repo template or a Dockerized setup next?









Ask ChatGPT
