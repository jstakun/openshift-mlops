1. git clone https://github.com/jstakun/openshift-mlops.git
2. oc new-project mlapps
3. for f in *.yaml; do oc create -f $f; done
4. Run object-detection-app and object-detection-rest pipelines
5. oc new-app --name mongodb docker.io/bitnami/mongodb:4.4
6. oc new-app --name=dbapi -e QUARKUS_MONGODB_CONNECTION_STRING=mongodb://mongodb:27017 quay.io/jstakun/camel-quarkus-mongodb-client:latest
7. oc expose service/dbapi
8. oc new-app --name object-detection-proxy -e BACKEND_URL=http://object-detection-rest:8080/predictions -e LOGGER_URL=http://dbapi:8080/camel/v1/cache/object-detection-log quay.io/jstakun/json-proxy:latest 
9. oc set env deployment/object-detection-app OBJECT_DETECTION_URL=http://object-detection-proxy:8080/predictions
10. Go to object-detection-app url and take picture
echo https://$(oc get route | grep object-detection-app | awk '{print $2}')
11. ROUTE=http://$(oc get route | grep dbapi | awk '{print $2}') && echo $ROUTE
12. curl -v $ROUTE/camel/v1/cache/object-detection-log/10
