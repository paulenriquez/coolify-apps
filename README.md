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

Hence, this repository aims to take all the above customizations, and "document" it in replicable `docker-compose.yml` files — so the next time I need to make any of these deployments, I'm able to do it in a standardized and automated manner.

## 📂 Structure

The repository is structured like so:

```
coolify-apps/
├── [[ App Directory ]]/
│   ├── docker-compose.yml
│   └── docs.md
├── docs.example.md
└── README.md
```

Each **App Directory** contains exactly two files —

- `docker-compose.yml` - The Docker Compose file to be used in Coolify.
- `docs.md` - Outlines the customizations made in the Docker Compose file.

The `docs.example.md` file is a template for how each App Directory's `docs.md` file is to be structured and formatted.

## 🚀 Applications

| Application | Website                | Directory     | Docker Compose                                        | Documentation                   |
| ----------- | ---------------------- | ------------- | ----------------------------------------------------- | ------------------------------- |
| Logto       | https://logto.io/      | `/logto`      | [docker-compose.yml](./logto/docker-compose.yml)      | [docs.md](./logto/docs.md)      |
| Open WebUI  | https://openwebui.com/ | `/open-webui` | [docker-compose.yml](./open-webui/docker-compose.yml) | [docs.md](./open-webui/docs.md) |
