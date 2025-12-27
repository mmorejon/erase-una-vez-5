# erase-una-vez-5

[![es](https://img.shields.io/badge/Leer_en-Español-blue.svg?style=flat-square)](README.md)

<div align="center">

<img src="./assets/book-cover.jpg" alt="Once Upon a Time Kubernetes Book Cover" width="300"/>

### 📚 OCI Data Volumes

Their function is to generate **"Data-Only" Container Images**: images that do not have an operating system or run anything; they simply serve as a "pen drive" to transport your files to any Kubernetes cluster or Docker environment.

This repository is a practical example created for the book **"Once Upon a Time Kubernetes"**.

👇 **Get the updated 2025 edition here:** 👇

[![Amazon](https://img.shields.io/badge/Amazon-Buy_Paperback-orange?style=for-the-badge&logo=amazon)](https://www.amazon.es/dp/B0F9VPCJ7X)
[![LeanPub](https://img.shields.io/badge/LeanPub-Download_Ebook-blue?style=for-the-badge&logo=leanpub)](https://leanpub.com/once-upon-a-time-kubernetes)

</div>

## ⚡ What makes it special?

* **Docker Simplicity:** It is built using a standard `Dockerfile` and the `scratch` base image.
* **Ultralight:** By using `FROM scratch`, the image contains nothing but your files. Its size is exactly that of your data.
* **Total Compatibility:** Works with any registry (Docker Hub, GHCR, AWS ECR) and tool that supports OCI images.
* **Multi-Architecture:** Configured to automatically generate images compatible with `amd64` and `arm64` via Docker Buildx.

---

## 📦 How to consume the data

Since this image has no operating system (`scratch`), it has no shell (`/bin/sh`) or executables. You cannot run `docker run` in the traditional way. Use it to:

### A) Copy data at build time (Dockerfile)

Ideal for injecting data into other images.

```dockerfile
# 1. Pull our data
FROM ghcr.io/mmorejon/erase-una-vez-5:main AS my-files

# 2. Copy our files to our real application (e.g. Nginx)
FROM nginx:alpine
COPY --from=my-files / /usr/share/nginx/html/
```

Build the image and start the container.

```bash
# Build the image
docker image build --tag server .

# Run the container
docker container run --detach --publish 8080:80 server
```

Access the files included in the image.

```bash
curl http://localhost:8080/example-1.txt
> Érase una vez Kubernetes
```

### B) Use as Volume in Kubernetes (v1.35+)

If your cluster supports the Image Volumes functionality, you can mount it directly. If you do not have a Kubernetes cluster, you can use the exercise repository from the book "Once Upon a Time Kubernetes".

> ℹ️ Reference: For more details about this functionality, see the official announcement in the Kubernetes blog: [Support for mounting OCI images as volumes (v1.35 Sneak Peek)](https://kubernetes.io/blog/2025/11/26/kubernetes-v1-35-sneak-peek/#support-for-mounting-oci-images-as-volumes).

<https://github.com/mmorejon/once-upon-a-time-k8s>

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

To test it, you can use the following command:

```bash
kubectl apply -f https://raw.githubusercontent.com/mmorejon/erase-una-vez-5/refs/heads/main/manifest.yaml

kubectl exec erase-una-vez-5 -- cat /usr/share/nginx/html/example-2.txt
> The scratch image was used to create the OCI artifact.
```

---

## 🤝 Community and Feedback

1.  ⭐ **Has this been useful to you?** Give a **star** to the repository (top right). It helps us reach more engineers.
2.  📚 **Still don't have the book?** Buy the book on Amazon or Leanpub.

<div align="center">
    <a href="https://www.amazon.es/dp/B0F9VPCJ7X">
        <img src="https://img.shields.io/badge/Amazon-See_price_and_reviews-orange?style=for-the-badge&logo=amazon" />
    </a>
</div>
