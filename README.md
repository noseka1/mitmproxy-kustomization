# Kustomization for Deploying mitmproxy on OpenShift

Deploy the proxy to OpenShift:

```
$ oc apply --kustomize mitmproxy/overlays/loadbalancer
```

Test connecting through the proxy:

```
$ HTTPS_PROXY=a44e35f507c404cee8e23dfb9c38837c-137495819.us-west-2.elb.amazonaws.com:8080 curl --proxy-cacert mitmproxy/base/conf/mitmproxy-ca-crt.pem --cacert mitmproxy/base/conf/mitmproxy-ca-crt.pem --verbose https://google.com
```
