# ansible-kubernetes misc

## Install elasticsearch

This helm chart comes from [github.com/bitnami/charts](https://github.com/bitnami/charts/tree/master/bitnami/elasticsearch).

Elasticsearch install:

    cd helm/elasticsearch/
    helm install gros-elastic .


It will install this elasticsearch architecture :
```mermaid
graph TD;
    cp([Computer])-- port-forward :5601 -->Kibana
    podX-- :9200 -->cn[2 coordinating nodes];
    Kibana-- :9200 -->cn;
    cn-->mn[3 master nodes];
    mn[3 master nodes]-->dn[2 data nodes];
    mn[3 master nodes]-->mpvc[(1 PVC each)]
    dn-->dpvc[(1 PVC each)]
```

After installation, to access kibana:

    kubectl port-forward --namespace default svc/gros-elastic-kibana 5601:5601

Then open your web browser tp [http://localhost:5601/app/home](http://localhost:5601/app/home).
Or open persistent access with an Ingress or NodePort.
