
Verifica con:

```powershell
docker --version
minikube version
kubectl version --client


---

0️⃣ Arrancar Minikube con Docker

minikube start --driver=docker
kubectl get nodes

Salida esperada:

NAME       STATUS   ROLES    AGE   VERSION
minikube   Ready    control-plane   ...


---

1️⃣ Creación del Deployment (1 réplica)

Crear Deployment usando nombre-imagen:latest:

kubectl create deployment nombre-deployment \
  --image=nombre-imagen:latest \
  --replicas=1

Verificar:

kubectl get deployment nombre-deployment
kubectl get pods

Opcional: ver YAML generado

kubectl get deployment nombre-deployment -o yaml


---

2️⃣ Exponer el Deployment como Servicio

2.1 Crear Service tipo ClusterIP

kubectl expose deployment nombre-deployment \
  --name=hello-svc \
  --port=80 \
  --target-port=80 \
  --type=ClusterIP

Verificar:

kubectl get svc hello-svc

---

index dentro del pod:
- kubectl exec -it <POD_NAME> -- sh -lc "echo '<h1>HOLA - FUNCIONANDO</h1>' > /usr/share/nginx/html/index.html"

---

2.2 Probar acceso dentro del clúster (port-forward)

En una PowerShell:

kubectl port-forward svc/hello-svc 8080:80

En OTRA PowerShell:

curl http://localhost:8080

O abrir en el navegador:

http://localhost:8080

Luego detener con Ctrl + C.


---

2.3 Cambiar a NodePort (acceso externo)

kubectl patch svc hello-svc -p '{"spec":{"type":"NodePort"}}'
kubectl get svc hello-svc

Obtener URL en Minikube:

minikube service hello-svc --url

Probar en navegador o con curl:

curl <URL_GENERADA>


---

3️⃣ Escalar el Deployment a 4 réplicas

kubectl scale deployment nombre-dployment --replicas=4

Verificar:

kubectl get pods -o wide

Debe haber 4 pods en Running.


---

4️⃣ Actualizar el Deployment a nuevo:actualizacion

Cambiar imagen:

kubectl set image deployment/nombre deployment \
  nombre-deployment=nuevo:actualizacion

Ver estado del rollout:

kubectl rollout status deployment/nombre-deployment
kubectl get pods


---

4.1 Verificar nueva versión

Obtener URL nuevamente:

minikube service hello-svc --url

Probar:

curl <URL>

Diferencias esperadas

Antes	Después

Página nginxdemos/hello (demo)	Página Welcome to nginx!


Ver headers:

curl -i <URL>


---

🧠 Comandos útiles para depuración

kubectl describe deployment nombre-deployment
kubectl describe svc hello-svc
kubectl logs -l app=nombre-deployment --tail=50
kubectl get events --sort-by=.metadata.creationTimestamp


---

🧹 Limpieza 

kubectl delete svc hello-svc
kubectl delete deployment nombre-deployment
minikube delete


---

Dashboard
minikube addons enable dashboard
minikube addons enable metrics-server

minikube dashboard

