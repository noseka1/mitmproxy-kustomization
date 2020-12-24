# Kustomization for Deploying mitmproxy on OpenShift

## Deploy a proxy accessible from outside of the OpenShift cluster

Deploy the proxy to OpenShift:

```
$ oc apply --kustomize mitmproxy/overlays/loadbalancer
```

Obtain the external hostname of the load balancer:

```
$ oc get svc --namespace mitmproxy mitmproxy --output jsonpath='{.status.loadBalancer.ingress[0].hostname}'
```

Test connecting through the proxy:

```
$ HTTPS_PROXY=<loadbalancer_hostname>:8080 curl --proxy-cacert mitmproxy/base/conf/mitmproxy-ca-crt.pem --cacert mitmproxy/base/conf/mitmproxy-ca-crt.pem --verbose https://google.com
```

## Deploy a proxy for inspecting internal OpenShift traffic

Deploy the proxy to OpenShift:

```
$ oc apply --kustomize mitmproxy/overlays/nodeport
```

Prepare proxy configuration:

```
$ PROXY_PORT=$(oc get svc --namespace mitmproxy mitmproxy --output jsonpath='{.spec.ports[0].nodePort}')
$ PROXY_HOST=$(oc get node --selector node-role.kubernetes.io/master= --output jsonpath='{.items[0].status.addresses[?(@.type=="InternalIP")].address}')
$ PROXY_URL="http://$PROXY_HOST:$PROXY_PORT"
$ sed \
    --in-place \
    --expression "s#@@HTTP_PROXY_URL@@#$PROXY_URL#" \
    --expression "s#@@HTTPS_PROXY_URL@@#$PROXY_URL#" \
    openshift/base/cluster-proxy.yaml
```

Use the mitmproxy as the OpenShift cluster-wide proxy:

```
$ oc apply --kustomize openshift/base/
```
