# erase-una-vez-5

[![English](https://img.shields.io/badge/Read_in-English-blue?style=flat-square)](README.en.md)

<div align="center">

<img src="./assets/book-cover.jpg" alt="Portada Libro Érase una vez Kubernetes" width="300"/>

### 📚 Volúmenes OCI de Datos

Su función es generar **Imágenes de Contenedor "Data-Only"**: imágenes que no tienen sistema operativo ni ejecutan nada, simplemente sirven como un "pendrive" para transportar tus archivos a cualquier clúster de Kubernetes o entorno Docker.

Este repositorio es un ejemplo práctico creado para el libro **"Érase una vez Kubernetes"**.

👇 **Consigue la edición actualizada 2025 aquí:** 👇

[![Amazon](https://img.shields.io/badge/Amazon-Comprar_en_Tapa_Blanda-orange?style=for-the-badge&logo=amazon)](https://www.amazon.es/dp/8409212765)
[![LeanPub](https://img.shields.io/badge/LeanPub-Descargar_Ebook-blue?style=for-the-badge&logo=leanpub)](https://leanpub.com/erase-una-vez-kubernetes)

</div>

## ⚡ ¿Qué tiene de especial?

* **Simplicidad Docker:** Se construye utilizando un `Dockerfile` estándar y la imagen base `scratch`.
* **Ultraligera:** Al usar `FROM scratch`, la imagen no contiene nada más que tus archivos. Su peso es exacto al de tus datos.
* **Compatibilidad Total:** Funciona con cualquier registro (Docker Hub, GHCR, AWS ECR) y herramienta que soporte imágenes OCI.
* **Multi-Arquitectura:** Configurado para generar imágenes compatibles con `amd64` y `arm64` automáticamente mediante Docker Buildx.

---

## 📦 Cómo consumir los datos

Como esta imagen no tiene sistema operativo (`scratch`), no tiene shell (`/bin/sh`) ni ejecutables. No puedes hacer `docker run` de forma tradicional. Úsala para:

### A) Copiar datos en tiempo de construcción (Dockerfile)

Ideal para inyectar datos en otras imágenes.

```dockerfile
# 1. Pull our data
FROM ghcr.io/mmorejon/erase-una-vez-5:main AS my-files

# 2. Copy our files to our real application (e.g. Nginx)
FROM nginx:alpine
COPY --from=my-files / /usr/share/nginx/html/
```

Construye la imagen e inicia el contenedor.

```bash
# Build the image
docker image build --tag server .

# Run the container
docker container run --detach --publish 8080:80 server
```

Accede a los ficheros incluidos en la imagen.

```bash
curl http://localhost:8080/example-1.txt
> Érase una vez Kubernetes
```

### B) Usar como Volumen en Kubernetes (v1.35+)

Si tu clúster admite la funcionalidad Image Volumes, puedes montarla directamente. Si no tienes un clúster de Kubernetes, puedes usar el repositorio de ejercicios del libro Érase una vez Kubernetes.

> ℹ️ Referencia: Para más detalles sobre esta funcionalidad, consulta el anuncio oficial en el blog de Kubernetes: [Support for mounting OCI images as volumes (v1.35 Sneak Peek)](https://kubernetes.io/blog/2025/11/26/kubernetes-v1-35-sneak-peek/#support-for-mounting-oci-images-as-volumes).

<https://github.com/mmorejon/erase-una-vez-k8s>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: erase-una-vez-5
spec:
  containers:
  - name: server
    image: nginx:alpine
    volumeMounts:
    - name: volume
      mountPath: /usr/share/nginx/html/
  volumes:
  - name: volume
    image:
      reference: ghcr.io/mmorejon/erase-una-vez-5:main
      pullPolicy: IfNotPresent
```

Para probarlo, puedes usar el siguiente comando:

```bash
kubectl apply -f https://raw.githubusercontent.com/mmorejon/erase-una-vez-5/refs/heads/main/manifest.yaml

kubectl exec erase-una-vez-5 -- cat /usr/share/nginx/html/example-1.txt
> Érase una vez Kubernetes
```

---

## 🤝 Comunidad y Feedback

1.  ⭐ **¿Te ha sido útil?** Dale una **estrella** al repositorio (arriba a la derecha). Nos ayuda a llegar a más ingenieros.
2.  📚 **¿Aún no tienes el libro?** Compra el libro en Amazon o Leanpub.

<div align="center">
    <a href="https://www.amazon.es/dp/8409212765">
        <img src="https://img.shields.io/badge/Amazon-Ver_Precio_y_Opiniones-orange?style=for-the-badge&logo=amazon" />
    </a>
</div>
