
# Vaultwarden with Multiple YubiKey Support

🔐 **A streamlined, secure, and user-friendly setup of [Vaultwarden](https://github.com/dani-garcia/vaultwarden) with support for multiple YubiKey devices, providing enhanced 2FA security and account protection.**

## Table of Contents
- [Vaultwarden with Multiple YubiKey Support](#vaultwarden-with-multiple-yubikey-support)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Features](#features)
  - [Requirements](#requirements)
  - [Installation](#installation)
    - [1. Clone the repository](#1-clone-the-repository)
    - [2. Create and configure the `.env` file](#2-create-and-configure-the-env-file)
  - [Configuration](#configuration)
    - [YubiKey Setup](#yubikey-setup)
    - [Vaultwarden Configuration](#vaultwarden-configuration)
    - [Docker Compose Configuration](#docker-compose-configuration)
  - [Usage](#usage)
  - [Troubleshooting](#troubleshooting)
  - [Contributing](#contributing)
  - [License](#license)

---

## Introduction

🔐 **This repository provides a step-by-step guide to configure Vaultwarden with support for multiple YubiKey devices, allowing users to enhance their password management security.** This setup is ideal for individual users and teams who require multi-factor authentication (MFA) with hardware tokens.

---

## Features

✨ **Key Features:**

- **Self-hosted Password Management** – Securely store and manage your passwords with Vaultwarden.
- **Enhanced 2FA with YubiKey** – Protect your account with multiple YubiKeys for flexible authentication options.
- **Open Source and Privacy-Centric** – No third-party dependencies, you own your data.
- **Easy to Use** – Set up and manage with Docker and Docker Compose.

---

## Requirements

🖥️ **Requirements:**

- Docker & Docker Compose installed on your machine.
- YubiKey devices (minimum of 2 for multiple-factor authentication).
- Basic knowledge of Docker Compose and Vaultwarden.

---

## Installation

### 1. Clone the repository
```bash
git clone https://github.com/your-repo/vaultwarden-yubikey.git
cd vaultwarden-yubikey
```

### 2. Create and configure the `.env` file
Create a `.env` file from the template:
```bash
cp .env.example .env
```
Edit the `.env` file to set the values of necessary environment variables, such as YubiKey `CLIENT_ID` and `SECRET_KEY`.

---

## Configuration

### YubiKey Setup

🛠️ **To enable YubiKey support for 2FA, follow these steps:**

1. Create a YubiKey developer account at [Yubico's developer portal](https://upgrade.yubico.com/getapikey/).
2. Obtain two sets of YubiKey `CLIENT_ID` and `SECRET_KEY` for your devices.
3. Update the `.env` file with the `CLIENT_ID` and `SECRET_KEY` for both YubiKeys:

   ```ini
   YUBICO_CLIENT_ID=your_first_client_id
   YUBICO_SECRET_KEY=your_first_secret_key
   YUBICO_CLIENT_ID2=your_second_client_id
   YUBICO_SECRET_KEY2=your_second_secret_key
   ```

### Vaultwarden Configuration

🔧 **Vaultwarden uses environment variables to configure its settings.** Update the `.env` file to match your desired configuration:

- **WebSocket support** – Set `WEBSOCKET_ENABLED=true` if you need WebSocket support.
- **Signup settings** – Set `SIGNUPS_ALLOWED=false` to disable signups.
- **Mail server settings** – Uncomment and configure mail settings for email-based notifications.

The environment variables are automatically loaded by Docker Compose and passed to the Vaultwarden container.

### Docker Compose Configuration

📝 **The `docker-compose.yml` file includes the necessary configuration for both Vaultwarden and Nginx services:**

```yaml
version: '3.9'

services:
  vaultwarden:
    image: vaultwarden/server:1.25.2-alpine
    volumes:
      - './Data/Vaultwarden:/data'
    restart: on-failure
    environment:
      WEBSOCKET_ENABLED: '${WEBSOCKET_ENABLED}'
      SIGNUPS_ALLOWED: '${SIGNUPS_ALLOWED}'
      SIGNUPS_VERIFY: '${SIGNUPS_VERIFY}'
      INVITATIONS_ALLOWED: '${INVITATIONS_ALLOWED}'
      SHOW_PASSWORD_HINT: '${SHOW_PASSWORD_HINT}'
      YUBICO_CLIENT_ID: '${YUBICO_CLIENT_ID}'
      YUBICO_SECRET_KEY: '${YUBICO_SECRET_KEY}'
      YUBICO_CLIENT_ID2: '${YUBICO_CLIENT_ID2}'
      YUBICO_SECRET_KEY2: '${YUBICO_SECRET_KEY2}'

  nginx:
    build: './nginx'
    links:
      - vaultwarden
    environment:
      VIRTUAL_HOST: '${VIRTUAL_HOST}'
    command: /bin/sh -c "/run_nginx.sh"
    restart: on-failure
    networks:
      default:
    ports:
      - 8484:80

networks:
  default:
    driver: bridge
```

This configuration starts Vaultwarden and Nginx with a reverse proxy, exposing the service on port `8484`.

---

## Usage

🚀 **Once the container is running, access Vaultwarden through your web browser at `http://your-server-ip:8484`.**

1. Log in with your Vaultwarden credentials.
2. During login, you will be prompted for your YubiKey authentication. Insert your YubiKey and press the button to complete the authentication.

---

## Troubleshooting

⚠️ **Common Issues:**

- **Vaultwarden container fails to start** – Check logs for errors related to environment variables or missing files:
  ```bash
  docker-compose logs vaultwarden
  ```
- **YubiKey not recognized** – Ensure that both YubiKeys are correctly registered with Yubico and their keys are correctly configured in the `.env` file.

---

## Contributing

💡 **We welcome contributions to improve the configuration and functionality of Vaultwarden with YubiKey support.** Please fork the repository, make changes, and create a pull request.

---

## License

📜 **This project is licensed under the MIT License** – see the [LICENSE](LICENSE) file for details.
