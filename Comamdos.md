
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

Crear Deployment usando nginxdemos/hello:latest:

kubectl create deployment hello-nginx \
  --image=nginxdemos/hello:latest \
  --replicas=1

Verificar:

kubectl get deployment hello-nginx
kubectl get pods

Opcional: ver YAML generado

kubectl get deployment hello-nginx -o yaml


---

2️⃣ Exponer el Deployment como Servicio

2.1 Crear Service tipo ClusterIP

kubectl expose deployment hello-nginx \
  --name=hello-svc \
  --port=80 \
  --target-port=80 \
  --type=ClusterIP

Verificar:

kubectl get svc hello-svc

---

index dentro del pod:
- kubectl exec -it <POD_NAME> -- sh -lc "echo '<h1>NGINX alpine OK</h1>' > /usr/share/nginx/html/index.html"

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

kubectl scale deployment hello-nginx --replicas=4

Verificar:

kubectl get pods -o wide

Debe haber 4 pods en Running.


---

4️⃣ Actualizar el Deployment a nginx:alpine

Cambiar imagen:

kubectl set image deployment/hello-nginx \
  hello-nginx=nginx:alpine

Ver estado del rollout:

kubectl rollout status deployment/hello-nginx
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

kubectl describe deployment hello-nginx
kubectl describe svc hello-svc
kubectl logs -l app=hello-nginx --tail=50
kubectl get events --sort-by=.metadata.creationTimestamp


---

🧹 Limpieza (opcional)

kubectl delete svc hello-svc
kubectl delete deployment hello-nginx
minikube delete
