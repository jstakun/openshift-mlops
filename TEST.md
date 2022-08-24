ROUTE=$(oc get route | grep object-detection-rest | awk '{print $2}') && echo $ROUTE

(echo -n '{"image": "'; base64 image.jpg; echo '"}') | curl -H "Content-Type: application/json" -d @- https://$ROUTE/predictions
