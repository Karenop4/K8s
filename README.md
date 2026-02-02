# Ejercicio Kubernetes: Deployment, Service, escalado y actualización

Este repositorio contiene manifiestos base y una guía de comandos `kubectl` para completar el ejercicio solicitado con la imagen `nginxdemos/hello` y luego actualizarla a `nginx:alpine`.

## 1) Creación de un Deployment

Manifiesto base con 1 réplica y la imagen `nginxdemos/hello:latest`:

```bash
kubectl apply -f k8s/deployment-nginxdemos.yaml
```

Verificación:

```bash
kubectl get deploy hello-nginx
kubectl get pods -l app=hello-nginx
```

## 2) Exposición del Deployment como Servicio

### 2.1 Crear Service ClusterIP

```bash
kubectl apply -f k8s/service-clusterip.yaml
```

Comprobar el servicio dentro del clúster (ejemplo usando un pod temporal):

```bash
kubectl run curl --rm -i --tty --image=curlimages/curl --restart=Never \
  -- http://hello-nginx.default.svc.cluster.local
```

### 2.2 Cambiar a NodePort

```bash
kubectl apply -f k8s/service-nodeport.yaml
```

Obtener el NodePort y validar desde fuera del clúster (o desde el nodo):

```bash
kubectl get svc hello-nginx
# Ejemplo: curl http://<NODE_IP>:<NODE_PORT>
```

## 3) Escalado del Deployment

Modificar a 4 réplicas:

```bash
kubectl scale deploy hello-nginx --replicas=4
kubectl get pods -l app=hello-nginx -o wide
```

## 4) Actualización del Deployment

Actualizar a `nginx:alpine` con manifiesto dedicado:

```bash
kubectl apply -f k8s/deployment-nginx-alpine.yaml
```

Observar el rollout:

```bash
kubectl rollout status deploy hello-nginx
kubectl get pods -l app=hello-nginx
```

Verificar la diferencia mediante HTTP (la respuesta ya no será la página de `nginxdemos/hello`):

```bash
# Desde dentro del clúster
kubectl run curl --rm -i --tty --image=curlimages/curl --restart=Never \
  -- http://hello-nginx.default.svc.cluster.local

# Desde fuera (NodePort)
# curl http://<NODE_IP>:<NODE_PORT>
```
