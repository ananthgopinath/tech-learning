import requests
import time

DAPR_PORT = 3500
DAPR_STATE_URL = f"http://localhost:{DAPR_PORT}/v1.0/state/statestore"

def save_state(key, value):
    payload = [{"key": key, "value": value}]
    response = requests.post(DAPR_STATE_URL, json=payload)
    print(f"Save status: {response.status_code}")

def get_state(key):
    response = requests.get(f"{DAPR_STATE_URL}/{key}")
    if response.status_code == 200:
        print(f"Got value for '{key}': {response.text}")
    else:
        print(f"Failed to get state: {response.status_code}")

if __name__ == "__main__":
    time.sleep(2)  # wait for daprd
    save_state("message", "Hello from Python + Redis via Dapr")
    get_state("message")


apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: statestore
spec:
  type: state.redis
  version: v1
  metadata:
    - name: redisHost
      value: localhost:6379
    - name: redisDB
      value: "0"
