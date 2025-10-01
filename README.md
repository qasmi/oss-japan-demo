# Demo

## Running on Linux

If you are on Linux, you can start the setup right away.

## Infrastructure Setup

Run the following to provision the required infrastructure:

```bash
playgrounds/kind/infra-setup/setup.sh
```

## Kubernetes Cluster + ArgoCD + GitOps Bootstrap

To run this lab with [Devbox](https://www.jetify.com/devbox):

* Set up the cluster and bootstrap GitOps:

```bash
cd playgrounds/kind
devbox run setup
```

* Tear down and clean up resources:

```bash
cd playgrounds/kind
devbox run shutdown
```

## Add MCP & Agent

1. Create an OpenAI API key secret in the `kagent` namespace:

```bash
kubectl create secret generic kagent-openai -n kagent --from-literal OPENAI_API_KEY=YOUR-KEY
```

2. Replace the GitHub personal token value in `agents/github-mcp.yaml`.
3. Apply the MCP and agent manifests:

```bash
kubectl apply -f agents/github-mcp.yaml 
kubectl apply -f agents/gitops-agent.yaml
```

## Expose Kagent Controller

Forward the controller service:

```bash
kubectl port-forward svc/kagent-controller 8083:8083 -n kagent
```

## Invoke GitOps Agent

Run the GitOps agent against your manifests:

```bash
kagent --api-url "http://localhost:8083/api" invoke --agent gitops-v3-agent --file demo-cluster/.k8s-gen/manifests.yaml 
```

# GitOps

Add your config to `demo-cluster/team-platform`

run:

```bash
cd demo-cluster
make
```

This will generate kubernetes manifests in `demo-cluster/.k8s-gen/manifests.yaml`

The make file is using `gitops-chart` to create the kubernetes templates.