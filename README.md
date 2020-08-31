# Kustomization for Deploying mitmproxy on OpenShift

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
