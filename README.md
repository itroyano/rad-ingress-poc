# rad-ingress-poc
Creating an ingress controller for OpenShift with mounted HAProxy configmaps

## Edit the default router to ignore the kwaf-secured routes

```yaml
oc edit ingresscontrollers default -n openshift-ingress-operator

spec:
  routeSelector:
    matchExpressions:
    - {key: "k8s.io/route-type", operator: NotIn, values: ["kwaf-secured"]}
```

## Note the following selector in deployment yaml

```yaml
spec:
  template:
    spec:
      containers:
      - env:
        - name: ROUTE_LABELS
          value: "k8s.io/route-type=kwaf-secured"
```

## Deploy the helm chart

```sh
helm upgrade haproxy-spoe
```

This will create:
- 2 Config Maps for HAProxy with SPOE enabled: `haproxy-template` and `mirror`.
- A custom HAProxy deployment with the 2 configmaps mounted, to serve as a custom Router/Ingress-controller.