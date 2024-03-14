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

$ yq --inplace ".spec.package = \"c8n.io/aghilish/function-add-k8s-labels-annotations:$TAG\"" example/production/functions.yaml
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
$ cd example/local
$ crossplane beta render xr.yaml composition.yaml functions.yaml -r
```

## Build runtime image 
```shell
# Build the function's runtime image - see Dockerfile
$ docker build . --tag=runtime

# Build a function package - see package/crossplane.yaml
$ crossplane xpkg build -f package --embed-runtime-image=runtime
```

## Production Deployment
```shell
$ kind create cluster --wait 5m

$ helm repo add crossplane-master https://charts.crossplane.io/master --force-update

$ helm upgrade --install crossplane --namespace crossplane-system --create-namespace crossplane-master/crossplane --devel --set "args={--debug,--enable-usages}"

$ cd example/production

$ kubectl apply -f functions.yaml

## wait untily healthy
$ kubectl get function -w

$ kubectl apply -f composition.yaml
$ kubectl apply -f xrd.yaml

$ kubectl apply -f aws-provider.yaml
$ kubectl apply -f aws-providerconfig.yaml

$ kubectl create secret generic aws-creds -n crossplane-system --from-file=creds=./aws-credentials.txt

$ kubectl apply -f claim.yaml

```