# coolify-apps

Welcome to **`coolify-apps`**, a repository of Docker Compose files for applications I personally self-host through [Coolify](https://coolify.io).

## 🤔 Motivation

Although most self-hostable applications are easy to deploy (especially with the bundled [one-click services](https://coolify.io/docs/services/overview)) — I constantly find myself having to go through multiple, extra steps to be able to customize these deployments so it's applicable for my personal use case.

This involves...

- having to read through more documentation
- setting specific environment variables
- running ad-hoc scripts
- orchestrating additional containers/services
- _etc., etc._

Hence, this repository aims to take all the above customizations, and "document" it in replicable `docker-compose.yaml` files — so the next time I need to make any of these deployments, I'm able to do it in a standardized and automated manner.

## ⚙️ Process

The process I follow for each application is...

1. Find a **"Starting Point"** — a pre-made Docker Compose file, maybe from the application's official repository.

2. Customize the "Starting Point" based on my use-case. This will become the `docker-compose.yaml` file you'll find in each application directory within this repository.

3. Document the customizations I made in #2. This will be come the `DOCS.md` file you'll find in each application directory as well.

## 📂 Structure

The repository is structured like so:

```
coolify-apps/
├── [[ App Directory ]]/
│   ├── docker-compose.yaml
│   └── DOCS.md
├── DOCS.example.md
└── README.md
```

Each **App Directory** contains exactly two files —

- `docker-compose.yaml` - The Custom Docker Compose file of the application to be deployed to Coolify.
- `DOCS.md` - Documents the customizations present in each application's Docker Compose file.

The `DOCS.example.md` file is a template for how each App Directory's `DOCS.md` file is to be structured and formatted.

## 🚀 Applications

| Application         | Official Website                             | Directory              |                      Custom Docker Compose                       |              Customizations              |
| ------------------- | -------------------------------------------- | ---------------------- | :--------------------------------------------------------------: | :--------------------------------------: |
| docker-web-redirect | https://github.com/MorbZ/docker-web-redirect | `/docker-web-redirect` | [docker-compose.yaml](./docker-web-redirect/docker-compose.yaml) | [DOCS.md](./docker-web-redirect/DOCS.md) |
| Logto               | https://logto.io/                            | `/logto`               |        [docker-compose.yaml](./logto/docker-compose.yaml)        |        [DOCS.md](./logto/DOCS.md)        |
| Open WebUI          | https://openwebui.com/                       | `/open-webui`          |     [docker-compose.yaml](./open-webui/docker-compose.yaml)      |     [DOCS.md](./open-webui/DOCS.md)      |
