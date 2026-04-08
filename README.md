# Day-60-Capstone-Deploy-WordPress-MySQL-on-Kubernetes

# Task

Ten days of Kubernetes — clusters, Pods, Deployments, Services, ConfigMaps, Secrets, storage, StatefulSets, resource management, autoscaling, and Helm. Today you put it all together. Deploy a real WordPress + MySQL application using every major concept you have learned.

# Expected Output

- A complete WordPress + MySQL stack in a capstone namespace
- Self-healing and data persistence verified
- A markdown file: day-60-capstone.md
- Screenshot of the running WordPress site and kubectl get all -n capstone


# Challenge Tasks

# Task 1: Create the Namespace (Day 52)

1. Create a capstone namespace
2. Set it as your default: kubectl config set-context --current --namespace=capstone

<img width="791" height="203" alt="Image" src="https://github.com/user-attachments/assets/100bce2c-0216-43a2-a86d-74cf226cbfc8" />

# Task 2: Deploy MySQL (Days 54-56)

1. Create a Secret with MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER, and MYSQL_PASSWORD using stringData
2. Create a Headless Service (clusterIP: None) for MySQL on port 3306
3. Create a StatefulSet for MySQL with:
   - Image: mysql:8.0
   - envFrom referencing the Secret
   - Resource requests (cpu: 250m, memory: 512Mi) and limits (cpu: 500m, memory: 1Gi)
   - A volumeClaimTemplates section requesting 1Gi of storage, mounted at /var/lib/mysql
4. Verify MySQL works: kubectl exec -it mysql-0 -- mysql -u <user> -p<password> -e "SHOW DATABASES;"

<img width="780" height="203" alt="Image" src="https://github.com/user-attachments/assets/ad5d79b0-0384-4c76-97b6-14cd143c5bbf" />

<img width="824" height="123" alt="Image" src="https://github.com/user-attachments/assets/e51f594c-3c74-41c0-8b68-ef0102f26fcd" />

# Task 3: Deploy WordPress (Days 52, 54, 57)

1. Create a ConfigMap with WORDPRESS_DB_HOST set to mysql-0.mysql.capstone.svc.cluster.local:3306 and WORDPRESS_DB_NAME
2. Create a Deployment with 2 replicas using wordpress:latest that:
   - Uses envFrom for the ConfigMap
   - Uses secretKeyRef for WORDPRESS_DB_USER and WORDPRESS_DB_PASSWORD from the MySQL Secret
   - Has resource requests and limits
   - Has a liveness probe and readiness probe on /wp-login.php port 80
3. Wait until both pods show 1/1 Running

<img width="767" height="803" alt="Image" src="https://github.com/user-attachments/assets/ec06ba3a-0d1f-49f9-9809-9d4ba9ff2426" />

<img width="1016" height="563" alt="Image" src="https://github.com/user-attachments/assets/ddc58975-02bb-4588-8f55-38b5121d460d" />

# Task 4: Test Self-Healing and Persistence

1. Delete a WordPress pod — watch the Deployment recreate it within seconds. Refresh the site.
2. Delete the MySQL pod: kubectl delete pod mysql-0 -n capstone — watch the StatefulSet recreate it
3. After MySQL recovers, refresh WordPress — your blog post should still be there

<img width="919" height="463" alt="Image" src="https://github.com/user-attachments/assets/59903fca-994b-4279-a38f-41a5844eca88" />

# Task 5: Set Up HPA (Day 58)

1. Write an HPA manifest targeting the WordPress Deployment with CPU at 50%, min 2, max 10 replicas
2. Apply and check: kubectl get hpa -n capstone
3. Run kubectl get all -n capstone for the complete picture

<img width="1097" height="629" alt="Image" src="https://github.com/user-attachments/assets/422c6b51-ca1e-46ee-be78-a212597f87be" />

# Task 6: (Bonus) Compare with Helm (Day 59)

1. Install WordPress using helm install wp-helm bitnami/wordpress in a separate namespace
2. Compare: how many resources did each approach create? Which gives more control?
3. Clean up the Helm deployment

<img width="933" height="147" alt="Image" src="https://github.com/user-attachments/assets/08cc2c02-5f43-4a67-b23e-a4e8cfd3367f" />

<img width="896" height="226" alt="Image" src="https://github.com/user-attachments/assets/c8d744a2-ff99-4599-89f5-eee7887edd5c" />

<img width="927" height="442" alt="Image" src="https://github.com/user-attachments/assets/71695c7e-0cc2-493c-a886-5ecb13309192" />

<img width="952" height="458" alt="Image" src="https://github.com/user-attachments/assets/5c3a025b-7552-4085-8070-c35eceead470" />

# Task 7: Clean Up and Reflect

1. Take a final look: kubectl get all -n capstone
2. Count the concepts you used: Namespace, Secret, ConfigMap, PVC, StatefulSet, Headless Service, Deployment, NodePort Service, Resource Limits, Probes, HPA, Helm — twelve concepts in one deployment
3. Delete the namespace: kubectl delete namespace capstone
4. Reset default: kubectl config set-context --current --namespace=default

<img width="937" height="652" alt="Image" src="https://github.com/user-attachments/assets/4c9b9283-1895-4c3c-afb1-f0b91425223d" />
