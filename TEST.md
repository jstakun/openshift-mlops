ROUTE=$(oc get route | grep object-detection-rest | awk '{print $2}') && echo $ROUTE

IMAGE=image.jpg

(echo -n '{"image": "'; base64 $IMAGE; echo '"}') | curl -H "Content-Type: application/json" -d @- https://$ROUTE/predictions | grep label
