1. git clone https://github.com/jstakun/openshift-mlops.git
2. oc new-project mlapps
3. for f in *.yaml; do oc create -f $f; done
4. Run object-detection-app and object-detection-rest pipelines
5. Go to object-detection-app url and take picture
echo https://$(oc get route | grep object-detection-app | awk '{print $2}')

--- optional setup for proxy which persists all json requests and responses to mongodb database ---

1. oc new-app --name mongodb docker.io/bitnami/mongodb:4.4
2. oc set volume deployment/mongodb --add --name=db -m /bitnami/mongodb -t pvc --claim-size=1G --overwrite
3. oc new-app --name=dbapi -e QUARKUS_MONGODB_CONNECTION_STRING=mongodb://mongodb:27017 -e QUARKUS_MONGODB_DATABASE=mlops quay.io/jstakun/camel-quarkus-mongodb-client:latest
4. oc create route edge --service=dbapi
5. oc new-app --name object-detection-proxy -e BACKEND_URL=http://object-detection-rest:8080/predictions -e LOGGER_URL=http://dbapi:8080/camel/v1/cache/object-detection-log quay.io/jstakun/json-proxy:latest 
6. oc set env deployment/object-detection-app OBJECT_DETECTION_URL=http://object-detection-proxy:8080/predictions
7. ROUTE=https://$(oc get route | grep dbapi | awk '{print $2}') && echo $ROUTE
8. curl -v $ROUTE/camel/v1/cache/object-detection-log/10

--- optional secure pipeline setup ---

1. Create crda secret
2. Create stackrox secret
3. Create cosign secret
4. Create registry secret and bind it to pipeline service account
```
oc patch serviceaccount pipeline -p '{"secrets": [{"name": "quay-creds"}]}'
```

