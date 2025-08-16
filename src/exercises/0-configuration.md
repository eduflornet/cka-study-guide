## Configuration

Learning never exhausts the mind.
– Leonardo da Vinci

#### Cambiar el editor por defecto a nano
Puedes configurar nano como tu editor predeterminado de dos formas:
🔹 Temporalmente (solo para la sesión actual)

```shell

export EDITOR=nano

kubectl edit deployment <nombre-del-deployment>

```
#### Permanentemente (para futuras sesiones)
Agrega esta línea al final de tu archivo ``` ~/.bashrc, ~/.zshrc``` o el que uses:

```shell
export EDITOR=nano
```

Después ejecuta:

```shell
source ~/.bashrc  # o ~/.zshrc según tu shell
```

#### Acceder al node control-plane dentro de KinD

Identifica el contenedor:

```bash
docker ps | grep control-plane
13d245f77d3e   kindest/node:v1.33.1   "/usr/local/bin/entr…"   50 seconds ago   Up 45 seconds   127.0.0.1:40527->6443/tcp   kind-
```

Entra al contenedor:

```bash
docker exec -it kind-control-plane bash
```
#### Localizar la ruta de kubelet

```bash
ps aux | grep kubelet
```
Verificar que existe el fichero config.yaml de kubelet y la ruta para los pods estaticos

```bash
cat /var/lib/kubelet/config.yaml | grep -i staticPodPath
staticPodPath: /etc/kubernetes/manifests
```

Verifico el contenido de la ruta localizada para pods estaticos:

```bash
ls /etc/kubernetes/manifests
etcd.yaml  kube-apiserver.yaml	kube-controller-manager.yaml  kube-scheduler.yaml
```

#### 🛠 Crear una imagen personalizada con editor nano
Si quieres que nano esté disponible cada vez que crees un clúster KIND, puedes crear una imagen personalizada:

Crea un Dockerfile:

```dockerfile
FROM kindest/node:v1.33.0  # Usa la versión que necesites
RUN apt update && apt install nano -y
```

Construye la imagen:

```bash
docker build -t kindest/node:nano .
```

Usa esa imagen al crear el clúster:

```yaml
# kind-config.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    image: kindest/node:nano
```

Luego crea el clúster con:

```bash
kind create cluster --config kind-config.yaml
```

✅ Verificación
Una vez creado el clúster, accede al nodo de control:

```bash
docker exec -it kind-control-plane bash
```

```bash
nano
```






