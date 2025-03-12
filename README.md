podman run --rm -d \
  --pull=never \
  --network=host \
  --name daprd \
  -v $(pwd)/components:/components \
  daprio/dapr:latest \
  ./daprd \
    --app-id myapp \
    --app-port 8000 \
    --dapr-http-port 3500 \
    --components-path /components
