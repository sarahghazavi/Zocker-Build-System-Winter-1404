# Zocker — Docker-Inspired Image Build System

> Operating Systems Course Project
> Sharif University of Technology - Winter 1404

---

## 📝 Description

**Zocker** is a lightweight container image build system inspired by Docker and implemented in **C**.

It parses a custom `Zockerfile`, creates layered filesystems using **OverlayFS**, stores image metadata, and reuses previously generated layers through a hash-based caching mechanism.

The project was developed to explore container image construction, filesystem isolation, layer management, build caching, and multi-stage builds.

---

## ⚙️ Features

* Layer-based image construction using **OverlayFS**
* Hash-based build cache with incremental invalidation
* Support for Docker images, Zocker images, and local directories as base filesystems
* Execution of build commands inside isolated filesystems using mount namespaces and `chroot`
* Image metadata and build history stored in manifest files
* Image listing, removal, history inspection, and unused-layer pruning
* Multi-stage builds with file transfer between stages

### Supported Zockerfile Instructions

`FROM` · `BASEDIR` · `RUN` · `COPY` · `ADD` · `WORKDIR` · `ARG` · `CMD`

---

## 🔄 Build Process

For each filesystem-changing instruction, Zocker:

1. Calculates a hash from the instruction and its parent layer.
2. Reuses an existing layer when a cache match is found.
3. Otherwise, creates and mounts a new OverlayFS layer.
4. Executes the instruction inside the merged filesystem.
5. Stores the resulting layer and updates the image manifest.

Metadata-only instructions such as `ARG` and `CMD` do not directly create filesystem layers.

---

## 🧪 Zockerfile Example

```dockerfile
FROM ubuntu:22.04

ARG BUILD_MODE=release

WORKDIR /app

COPY src/ /app/

RUN gcc main.c -o application

CMD /app/application
```

Build the image:

```bash
sudo ./zocker build \
  -t example-image \
  -f Zockerfile \
  --build-arg BUILD_MODE=release
```

Run the image:

```bash
sudo ./zocker run example-image
```

---

## 🧱 Multi-Stage Build Example

```dockerfile
FROM ubuntu:22.04 AS builder

WORKDIR /build
COPY src/ /build/
RUN gcc main.c -o application

FROM ubuntu:22.04 AS runtime

WORKDIR /app
COPY --from=builder /build/application /app/application
CMD /app/application
```

Only the final stage is stored as the resulting image, reducing unnecessary build dependencies in the output filesystem.

---

## 🗂️ Image Management

```bash
sudo ./zocker images
sudo ./zocker history example-image
sudo ./zocker rmi example-image
sudo ./zocker prune
```

These commands list images, display build history, remove an image, and delete unused layers.

---

## 🚀 Build and Installation

Clone the repository:

```bash
git clone git@github.com:sarahghazavi/Zocker-Build-System-Winter-1404.git
cd Zocker-Build-System-Winter-1404
```

Compile the project:

```bash
make
```

---

## ✅ Requirements

Zocker relies on Linux-specific filesystem and isolation mechanisms.

* Linux with OverlayFS support
* GCC and Make
* Docker
* `jq`
* `curl`
* Root privileges or appropriate mount permissions

Install the main dependencies on Debian or Ubuntu:

```bash
sudo apt update
sudo apt install build-essential docker.io jq curl
```

---

## 🛠️ Technical Details

* **Language:** C
* **Platform:** Linux
* **Filesystem:** OverlayFS
* **Isolation:** Mount namespaces and `chroot`
* **Caching:** Hash-based chained layer cache
* **Metadata:** Image manifest files
* **External tools:** Docker CLI, `jq`, and `curl`

---

## 📚 Documentation

The complete project report is available at:

```text
docs/Zocker_Project_Report.pdf
```
---

## 👥 Contributors

* **Sara Ghazavi**
* **Zahra Ghasabi**
* **Arash Ghavami**
* **Seyed Moein Hosseini**

Sharif University of Technology
Course: Operating Systems - Winter 1404
