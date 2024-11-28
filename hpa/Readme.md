### Get resource of deployment 
```
kubectl get deployment nginx -o yaml | grep -A5 resources
```

### Step 3: Create the HPA
## Use kubectl to create an HPA that scales based on CPU usage.

Create an HPA targeting the Nginx deployment:
```
kubectl autoscale deployment nginx --cpu-percent=50 --min=1 --max=5
```

### Verify the HPA:
```
kubectl get hpa
```

### Step 4: Generate Load
Simulate CPU load to trigger scaling.

Deploy a load generator (e.g., busybox):
```
kubectl run -i --tty load-generator --image=busybox --restart=Never -- /bin/sh
```
In the busybox shell, send continuous requests to the Nginx service:
```
while true; do wget -q -O- http://nginx.default.svc.cluster.local; done
```
(You can terminate this loop with Ctrl+C.)

### Step 5: Monitor HPA Behavior
Watch the HPA status:
```
kubectl get hpa -w
```
You'll see the TARGET column update with the current CPU usage percentage and the number of replicas adjust accordingly.
Verify the number of pods:
```
kubectl get pods
```
The number of pods should increase or decrease based on the load.


### Step 6: Clean Up
Once you're done testing, clean up the resources:
``` 
kubectl delete hpa nginx
kubectl delete deployment nginx
kubectl delete pod load-generator
kubectl delete service nginx
```
### Key Notes
- Metrics Availability: Ensure that Metrics Server is providing metrics:
``` 
kubectl top pods
kubectl top nodes
```
- Fine-Tuning:
Adjust the --cpu-percent target in the HPA definition for your specific scenario.
- Test memory-based scaling if needed (you'll need to adjust resource limits).
- Load Testing Tools: For more realistic load testing, consider tools like Apache Benchmark (ab), wrk, or k6.
