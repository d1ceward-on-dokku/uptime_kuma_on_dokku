![](.github/images/repo_header.png)

[![Uptime Kuma](https://img.shields.io/badge/Uptime_Kuma-2.4.0-blue.svg)](https://github.com/louislam/uptime-kuma/releases/tag/2.4.0)
[![Dokku](https://img.shields.io/badge/Dokku-Repo-blue.svg)](https://github.com/dokku/dokku)

# Run Uptime Kuma on Dokku

## Overview

This guide explains how to deploy [Uptime Kuma](https://github.com/louislam/uptime-kuma), a self-hosted monitoring tool similar to "Uptime Robot," on a [Dokku](http://dokku.viewdocs.io/dokku/) host. Dokku is a lightweight PaaS that simplifies deploying and managing applications using Docker.

## Prerequisites

Before proceeding, ensure you have the following:

- A working [Dokku host](http://dokku.viewdocs.io/dokku/getting-started/installation/).
- (Optional) The [Let's Encrypt plugin](https://github.com/dokku/dokku-letsencrypt) for SSL certificates.

## Setup Instructions

### 1. Create the App

Log into your Dokku host and create the `uptime-kuma` app:

```bash
dokku apps:create uptime-kuma
```

### 2. Configure Persistent Storage

To persist user uploads (e.g., avatars) between restarts, create a folder on the host machine and mount it to the app container:

```bash
sudo mkdir -p /var/lib/dokku/data/storage/uptime-kuma
dokku storage:mount uptime-kuma /var/lib/dokku/data/storage/uptime-kuma:/app/data
```

### 3. Configure the Domain and Ports

Set the domain for your app to enable routing:

```bash
dokku domains:set uptime-kuma uptime.example.com
```

Map the internal port `3001` to the external port `80`:

```bash
dokku ports:set grafana http:80:3001
```

### 4. Deploy the App

You can deploy the app to your Dokku server using one of the following methods:

#### Option 1: Deploy Using `dokku git:sync`

If your repository is hosted on a remote Git server with an HTTPS URL, you can deploy the app directly to your Dokku server using `dokku git:sync`. This method also triggers a build process automatically. Run the following command:

```bash
dokku git:sync --build uptime-kuma https://github.com/d1ceward-on-dokku/uptime_kuma_on_dokku.git
```

This will fetch the code from the specified repository, build the app, and deploy it to your Dokku server.

#### Option 2: Clone the Repository and Push Manually

If you prefer to work with the repository locally, you can clone it to your machine and push it to your Dokku server manually:

1. Clone the repository:

    ```bash
    # Via SSH
    git clone git@github.com:d1ceward-on-dokku/uptime_kuma_on_dokku.git

    # Via HTTPS
    git clone https://github.com/d1ceward-on-dokku/uptime_kuma_on_dokku.git
    ```

2. Add your Dokku server as a Git remote:

    ```bash
    git remote add dokku dokku@example.com:uptime-kuma
    ```

3. Push the app to your Dokku server:

    ```bash
    git push dokku master
    ```

Choose the method that best suits your workflow.

### 5. Enable SSL (Optional)

Secure your app with an SSL certificate from Let's Encrypt:

1. Add the HTTPS port:

    ```bash
    dokku ports:add grafana https:443:3001
    ```

2. Install the Let's Encrypt plugin:

    ```bash
    dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
    ```

3. Set the contact email for Let's Encrypt:

    ```bash
    dokku letsencrypt:set uptime-kuma email you@example.com
    ```

4. Enable Let's Encrypt for the app:

    ```bash
    dokku letsencrypt:enable uptime-kuma
    ```

## Wrapping Up

Congratulations! Your Uptime Kuma instance is now up and running. You can access it at [https://uptime.example.com](https://uptime.example.com).
