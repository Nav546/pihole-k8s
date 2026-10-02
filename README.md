# Pi-hole + Unbound on Kubernetes (Kind)

Migration of my Docker Compose Pi-hole setup (Nav546/pihole-docker) to Kubernetes.

## Architecture
Client pod -> Pi-hole (Service: pihole) -> Unbound (Service: unbound, ClusterIP) -> internet

## What it demonstrates
- Deployments and ClusterIP Services for Pi-hole and Unbound
- Pi-hole upstream set to Unbound via the injected service env var
- Admin password stored in a Kubernetes Secret (not in git)
- NetworkPolicy so only Pi-hole can reach Unbound on port 53
- Verified: direct query to Unbound from a test pod times out, query via Pi-hole succeeds

## Run it
    kind create cluster --name pihole
    kubectl apply -f manifests/unbound.yaml
    kubectl create secret generic pihole-secret --from-literal=password='CHANGE_ME'
    kubectl apply -f manifests/pihole.yaml
    kubectl apply -f manifests/unbound-netpol.yaml
    kubectl port-forward svc/pihole 8080:80   # http://localhost:8080/admin
