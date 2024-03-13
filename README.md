# function-add-k8s-labels-annotations

This is a sample xfn (crossplane function) that is developed with the [function-template-go](https://github.com/crossplane/function-template-go)

### Build and Push the OCI container image


```shell
# Run code generation - see input/generate.go
$ go generate ./...
# Run tests - see fn_test.go
$ go test ./...

$ export TAG=v0.0.1

$ docker image build --tag c8n.io/aghilish/function-add-k8s-labels-annotations:$TAG .

$ docker image push c8n.io/aghilish/function-add-k8s-labels-annotations:$TAG

$ yq --inplace ".spec.package = \"c8n.io/aghilish/function-add-k8s-labels-annotations:$TAG\"" example/functions.yaml
```

## Running locally
You can run your function locally and test it using `crossplane beta render`
with these example manifests.

```shell
# Run the function locally
$ go run . --insecure --debug
```

```shell
# Then, in another terminal, call it with these example manifests
$ crossplane beta render xr.yaml composition.yaml functions-dev.yaml -r
```

## Build runtime image 
```shell
# Build the function's runtime image - see Dockerfile
$ docker build . --tag=runtime

# Build a function package - see package/crossplane.yaml
$ crossplane xpkg build -f package --embed-runtime-image=runtime
```