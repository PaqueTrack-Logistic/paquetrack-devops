# Instrucciones de despliegue - Kubernetes local (Docker Desktop + kind)

## Requisitos previos

- Docker Desktop instalado y corriendo
- Kubernetes habilitado en Docker Desktop con **kind** (Settings → Kubernetes → kind → Apply & Restart)
- kind CLI instalado — verificar con `kind version`
  - Si no está: `winget install Kubernetes.kind`
  - Si no reconoce el comando tras instalar, copiar el ejecutable:
    ```
    copy "%LOCALAPPDATA%\Microsoft\WinGet\Packages\Kubernetes.kind_Microsoft.Winget.Source_8wekyb3d8bbwe\kind.exe" "C:\Windows\System32\kind.exe"
    ```
- kubectl disponible

> **¿Por qué kind?**
> Kind es el estándar profesional para Kubernetes local. Permite reproducir
> entornos de forma consistente y es el método usado en pipelines de CI/CD.
> Los servicios externos (Neon, CloudAMQP, Render) son accesibles desde kind
> sin configuración adicional.

---

## Paso 1 — Verificar el clúster

```bash
kubectl get nodes
kind get clusters
```

Debe mostrar un nodo `desktop-control-plane` en estado `Ready`
y un clúster llamado `desktop`.

---

## Paso 2 — Construir las imágenes localmente

Desde la carpeta raíz del proyecto:

```bash
docker build -t authentication-service:latest ./authentication-service
docker build -t shipment-service:latest ./shipment-service
docker build -t tracking-service:latest ./tracking-service
docker build -t gateway-service:latest ./gateway-service
docker build -t ui-service:latest ./ui-service
```

---

## Paso 3 — Cargar las imágenes en el clúster kind

Kind no comparte imágenes con Docker automáticamente.
Deben cargarse explícitamente al nodo del clúster:

```bash
kind load docker-image authentication-service:latest --name desktop
kind load docker-image shipment-service:latest --name desktop
kind load docker-image tracking-service:latest --name desktop
kind load docker-image gateway-service:latest --name desktop
kind load docker-image ui-service:latest --name desktop
```

> Este paso debe repetirse cada vez que se reconstruya una imagen.

---

## Paso 4 — Llenar secrets.yml con las credenciales reales

Edita `k8s/secrets.yml` y reemplaza todos los valores
`TU_PASSWORD`, `TU_USER`, etc. con los valores reales de tu `.env`

---

## Paso 5 — Crear el namespace

```bash
kubectl create namespace paquetrack
```

---

## Paso 6 — Aplicar los manifiestos en orden

```bash
# Primero configuración y secretos
kubectl apply -f k8s/secrets.yml
kubectl apply -f k8s/configmap.yml

# Luego los servicios (en este orden)
kubectl apply -f k8s/authentication-service/
kubectl apply -f k8s/shipment-service/
kubectl apply -f k8s/tracking-service/
kubectl apply -f k8s/gateway-service/
kubectl apply -f k8s/ui-service/
```

---

## Paso 7 — Verificar que todo esté corriendo

```bash
kubectl get pods -n paquetrack
kubectl get services -n paquetrack
```

Todos los pods deben estar en estado `Running` y `READY 1/1`.

---

## Paso 8 — Exponer los servicios (port-forward)

Kind no expone NodePorts directamente a localhost.
Abre dos terminales separadas y ejecuta:

```bash
# Terminal 1 — UI
kubectl port-forward service/ui-service 30000:80 -n paquetrack

# Terminal 2 — Gateway
kubectl port-forward service/gateway-service 30080:8080 -n paquetrack
```

## Acceso desde el navegador

- UI:      http://localhost:30000
- Gateway: http://localhost:30080

---

## Flujo completo al hacer cambios en el código

Cuando modifiques código de un servicio, repite estos pasos:

```bash
# 1. Reconstruir la imagen
docker build -t <nombre-servicio>:latest ./<nombre-servicio>

# 2. Recargar en kind
kind load docker-image <nombre-servicio>:latest --name desktop

# 3. Reiniciar el deployment
kubectl rollout restart deployment/<nombre-servicio> -n paquetrack
```

---

## Comandos útiles

```bash
# Ver logs de un pod
kubectl logs -f deployment/authentication-service -n paquetrack

# Ver estado detallado de un pod
kubectl describe pod <nombre-del-pod> -n paquetrack

# Reiniciar un deployment
kubectl rollout restart deployment/authentication-service -n paquetrack

# Ver todos los recursos
kubectl get all -n paquetrack

# Eliminar todo
kubectl delete -f k8s/ -n paquetrack
```