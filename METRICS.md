ROUTE=$(oc get route | grep object-detection-rest | awk '{print $2}') && echo $ROUTE


curl https://$ROUTE/metrics
