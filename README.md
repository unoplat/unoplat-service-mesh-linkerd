# unoplat-service-mesh-linkerd
Linkerd for unoplat

# installation


## Linkerd Cli Installation

curl --proto '=https' --tlsv1.2 -sSfL https://run.linkerd.io/install | sh

# Generate certificates on own

1. brew install step
2. step ca init --name "MyCA" --provisioner admin --dns localhost --address ":8443" --password-file <(echo "password")
3. step certificate create --profile root-ca "identity.linkerd.cluster.local" root.crt root.key (Root Certificate)
4. step certificate create --profile intermediate-ca --ca root.crt --ca-key root.key "identity.linkerd.cluster.local" issuer.crt issuer.key (Intermediate Certificate)
5. helm repo add jetstack https://charts.jetstack.io
6. helm repo update
7. helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.13.1 \
  --set installCRDs=true
8.  kubectl wait --for=condition=available --timeout=2m -n cert-manager deployment/cert-manager deployment/cert-manager-cainjector deployment/cert-manager-webhook
9. 

## Linkerd installation

1. helm repo add linkerd https://helm.linkerd.io/stable
2. helm repo update
3. helm install linkerd-cni -n linkerd-cni --create-namespace linkerd/linkerd2-cni
4. helm install linkerd-crds linkerd/linkerd-crds -n linkerd --create-namespace
5. helm install linkerd-control-plane -n linkerd \
  --set-file identityTrustAnchorsPEM=ca.crt \
  --set-file identity.issuer.tls.crtPEM=issuer.crt \
  --set-file identity.issuer.tls.keyPEM=issuer.key \
  --set cniEnabled=true \
  linkerd/linkerd-control-plane

##  
