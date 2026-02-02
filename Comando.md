



---

0) Arrancar Minikube en Docker

minikube start --driver=docker
kubectl get nodes

(Deberías ver 1 nodo “Ready”.)


---

1) Crear el Deployment (nginxdemos/hello:latest, 1 pod)

Opción A: 100% por comando

kubectl create deployment hello-nginx --image=nginxdemos/hello:latest --replicas=1
kubectl get deploy,pods -o wide

(Opcional) ver YAML

kubectl get deploy hello-nginx -o yaml


---

2) Exponer como Service (ClusterIP → validar dentro del cluster → cambiar a NodePort)

2.1 Crear Service tipo ClusterIP

kubectl expose deployment hello-nginx --name=hello-svc --port=80 --target-port=80 --type=ClusterIP
kubectl get svc hello-svc

2.2 Confirmar que responde dentro del clúster

La forma más fácil: port-forward (simula acceso interno sin complicarte)

kubectl port-forward svc/hello-svc 8080:80

En otra terminal:

curl http://localhost:8080

(Deberías ver HTML y/o texto de “Hello” de la demo.)

> Detén el port-forward con Ctrl + C.



2.3 Cambiar el Service a NodePort

kubectl patch svc hello-svc -p '{"spec":{"type":"NodePort"}}'
kubectl get svc hello-svc

2.4 Obtener URL accesible desde tu PC (Minikube)

Con Minikube, lo más práctico es:

minikube service hello-svc --url

Te va a imprimir una URL. Prueba:

curl $(minikube service hello-svc --url)

En navegador: pega esa URL.

> Nota: En Minikube con driver Docker, a veces NodePort directo no queda “bonito” desde fuera; minikube service ... --url siempre te da una ruta que funciona.




---

3) Escalar el Deployment a 4 réplicas

kubectl scale deployment hello-nginx --replicas=4
kubectl get deploy hello-nginx
kubectl get pods -l app=hello-nginx -o wide

Verifica que tengas 4 pods en Running.


---

4) Actualizar el Deployment a nginx:alpine y observar el rollout

4.1 Cambiar imagen

kubectl set image deployment/hello-nginx hello-nginx=nginx:alpine

4.2 Observar el proceso de actualización (rolling update)

kubectl rollout status deployment/hello-nginx
kubectl get pods -l app=hello-nginx -o wide

4.3 Confirmar que los nuevos pods funcionan

Sigue usando el mismo Service hello-svc:

curl $(minikube service hello-svc --url)

Ahora deberías ver la página default de NGINX (no la demo “hello”).


---

Ver diferencias entre versiones (demo vs nginx:alpine)

Antes (nginxdemos/hello): normalmente devuelve una página “Hello” de demo con info.

Después (nginx:alpine): devuelve el Welcome to nginx! (default).


Si quieres una prueba rápida tipo “marca”:

# ver headers (a veces cambia el Server o contenido)
curl -i $(minikube service hello-svc --url)


---

Comandos de verificación útiles (por si algo falla)

kubectl describe deploy hello-nginx
kubectl describe svc hello-svc
kubectl logs -l app=hello-nginx --tail=50
kubectl get events --sort-by=.metadata.creationTimestamp


---

Limpieza (opcional)

kubectl delete svc hello-svc
kubectl delete deploy hello-nginx
# o apagar minikube
minikube delete


---

Si me dices si estás en Windows (PowerShell) o Linux/macOS, te pongo exactamente cómo abrirlo en navegador con el comando correcto (pero con minikube service hello-svc --url ya lo tienes resuelto).
