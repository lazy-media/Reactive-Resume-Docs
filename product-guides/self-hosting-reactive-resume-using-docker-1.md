---
description: >-
  This should be an in-depth guide on how to set up Reactive Resume on your
  Virtual Private Server running a run-of-the-mill Linux distribution.
icon: docker
---

# Self-Hosting Reactive Resume using Docker - NGINX

I'll be setting the application today using a Hetzner Cloud server, but you could use any VPS. A good place to find a decent server can be [https://lowendbox.com/](https://lowendbox.com/). They are a great resource to find deals and dirt cheap offers on VPSes, especially active during Black Friday.

The server I am going to set up Reactive Resume is a simple one, with these specifications:

```
CPU: Intel (2vCPU)
RAM: 4 GB
SSD: 40 GB
OS: Debian 12
Price: 3.92 EUR per month
```

I'm going to assume you have already set up your server along with a user account (with sudo access) and that you have Docker and Docker Compose setup on your machine. If you haven't, these links should help you much better than I can:

* [https://www.digitalocean.com/community/tutorials/initial-server-setup-with-debian-11](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-debian-11)
* [https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-debian-10](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-debian-10)

Copy the following code to the home folder (or any specific project folder) on your machine, to a file named `compose.yml`or `docker-compose.yml`. If you choose to name the file anything else, you would need to run the docker compose command along with the `-f [file path]` flag.

**BE SURE TO CHANGE THE ONE ITEM UNDER THE MINIO SECTION WITH YOUR DOMAIN!**

```yaml
# In this Docker Compose example, we use Nginx Proxy Manager to manage the reverse proxy and SSL certificates.
# There's very little configuration to be made on the compose file itself, and most of it is done on the Nginx Proxy Manager UI.

services:
  # Database (Postgres)
  postgres:
    image: postgres:16-alpine
    restart: ${RESTART_POLICY:-unless-stopped}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: ${POSTGRES_USER:-postgres}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-postgres}
      POSTGRES_DB: ${POSTGRES_DB:-postgres}
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres -d postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Storage (for image uploads)
  minio:
    image: minio/minio:latest
    restart: ${RESTART_POLICY:-unless-stopped}
    command: server /data
    volumes:
      - minio_data:/data
    environment:
      MINIO_ROOT_USER: ${MINIO_ROOT_USER:-minioadmin}
      MINIO_ROOT_PASSWORD: ${MINIO_ROOT_PASSWORD:-minioadmin}
    labels:
      - traefik.enable=true
      - traefik.http.routers.storage.rule=Host(`storage.example.com`) # Update this to your domain instead of example.com
      - traefik.http.routers.storage.entrypoints=websecure
      - traefik.http.routers.storage.tls.certresolver=letsencrypt
      - traefik.http.services.storage.loadbalancer.server.port=9000

  # Chrome Browser (for printing and previews)
  chrome:
    image: ghcr.io/browserless/chromium:latest
    restart: ${RESTART_POLICY:-unless-stopped}
    environment:
      HEALTH: "true"
      TOKEN: ${CHROME_TOKEN:-chrome_token}
      PROXY_HOST: ${PROXY_HOST}
      PROXY_PORT: ${PROXY_PORT:-443}
      PROXY_SSL: ${PROXY_SSL}

  app:
    image: ${IMAGE:-pickit420/reactive-resume}:${IMAGE_TAG:-latest}
    restart: ${RESTART_POLICY:-unless-stopped}
    depends_on:
      - postgres
      - minio
      - chrome
    environment:
      # -- Environment Variables --
      PORT: ${APP_PORT:-3000}
      NODE_ENV: ${NODE_ENV:-production}

      # -- URLs --
      PUBLIC_URL: ${PUBLIC_URL}
      STORAGE_URL: ${STORAGE_URL}

      # -- Printer (Chrome) --
      CHROME_TOKEN: ${CHROME_TOKEN}
      CHROME_URL: ${CHROME_URL}

      # -- Database (Postgres) --
      DATABASE_URL: ${DATABASE_URL}

      # -- Auth --
      ACCESS_TOKEN_SECRET: ${ACCESS_TOKEN_SECRET}
      REFRESH_TOKEN_SECRET: ${REFRESH_TOKEN_SECRET}

      # -- Emails --
      MAIL_FROM: ${MAIL_FROM}
      # SMTP_URL: smtp://${SMTP_USER}:${SMTP_PASS}@${SMTP_ADDR}:${SMTP_PORT} # Optional

      # -- Storage (Minio) --
      STORAGE_ENDPOINT: ${STORAGE_ENDPOINT}
      STORAGE_PORT: ${STORAGE_PORT:-9000}
      STORAGE_REGION: ${STORAGE_REGION} # Optional
      STORAGE_BUCKET: ${STORAGE_BUCKET}
      STORAGE_ACCESS_KEY: ${STORAGE_ACCESS_KEY}
      STORAGE_SECRET_KEY: ${STORAGE_SECRET_KEY}
      STORAGE_USE_SSL: ${STORAGE_USE_SSL}
      STORAGE_SKIP_BUCKET_CHECK: ${STORAGE_SKIP_BUCKET_CHECK}

      # -- Crowdin (Optional) --
      # CROWDIN_PROJECT_ID: ${CROWDIN_PROJECT_ID}
      # CROWDIN_PERSONAL_TOKEN: ${CROWDIN_PERSONAL_TOKEN}

      # -- Feature Flags (Optional) --
      # DISABLE_SIGNUPS: ${DISABLE_SIGNUPS}
      # DISABLE_EMAIL_AUTH: ${DISABLE_EMAIL_AUTH}

      # -- GitHub (Optional) --
      # GITHUB_CLIENT_ID: ${GITHUB_CLIENT_ID}
      # GITHUB_CLIENT_SECRET: ${GITHUB_CLIENT_SECRET}
      # GITHUB_CALLBACK_URL: ${GITHUB_CALLBACK_URL}

      # -- Google (Optional) --
      # GOOGLE_CLIENT_ID: ${GOOGLE_CLIENT_ID}
      # GOOGLE_CLIENT_SECRET: ${GOOGLE_CLIENT_SECRET}
      # GOOGLE_CALLBACK_URL: ${GOOGLE_CALLBACK_URL}

      # -- OpenID (Optional) --
      # VITE_OPENID_NAME: ${VITE_OPENID_NAME}
      # OPENID_AUTHORIZATION_URL: ${OPENID_AUTHORIZATION_URL}
      # OPENID_CALLBACK_URL: ${OPENID_CALLBACK_URL}
      # OPENID_CLIENT_ID: ${OPENID_CLIENT_ID}
      # OPENID_CLIENT_SECRET: ${OPENID_CLIENT_SECRET}
      # OPENID_ISSUER: ${OPENID_ISSUER}
      # OPENID_SCOPE: ${OPENID_SCOPE}
      # OPENID_TOKEN_URL: ${OPENID_TOKEN_URL}
      # OPENID_USER_INFO_URL: ${OPENID_USER_INFO_URL}

  nginx:
    image: jc21/nginx-proxy-manager
    restart: ${RESTART_POLICY:-always}
    ports:
      - "80:80"
      - "443:443"
      - "81:81" # Port 81 is used for Proxy Manager's Web UI
    volumes:
      - nginx_data:/data
      - letsencrypt_data:/etc/letsencrypt
    environment:
      DISABLE_IPV6: "true"

volumes:
  minio_data:
  nginx_data:
  postgres_data:
  letsencrypt_data:
```

Use this .env example file. Replacing the values with the correct ones.

```yaml
###################################
##### RESTART POLICY SETTINGS #####
###################################

RESTART_POLICY=unless-stopped

#############################
##### POSTGRES SETTINGS #####
#############################

### -- You can change the right side of the `=` for these if you want to -- ###
POSTGRES_DB=postgres
POSTGRES_USER=postgres

### -- Recommended to change the default password (right side of `=`) to something else before starting the container for the first time. -- ###
POSTGRES_PASSWORD=postgres

### -- Do not change this -- ###
DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}

####################################
##### MINIO / STORAGE SETTINGS #####
####################################
### Please do not change these settings, pending investigation ###

MINIO_ROOT_USER=minioadmin
MINIO_ROOT_PASSWORD=minioadmin

###########################
##### CHROME SETTINGS #####
##################################################
### Change `chrome_token` to anything you want ###
##################################################

CHROME_TOKEN=chrome_token

## -- Nginx Specific Settings -- ##
PROXY_HOST=resume.example.com ### Recommended to change to your domain
PROXY_PORT=443
PROXY_SSL="true"

## -- Traefik (Not Secure Version) Specific Settings -- ##
# PROXY_HOST=resume.example.com ### Recommended to change to your domain
# PROXY_PORT=80
# PROXY_SSL="false"

#############################
##### MAIN APP SETTINGS #####
#############################

### -- ENVIRONMENT VARIABLES -- ##

NODE_ENV=production
IMAGE=pickit420/reactive-resume
IMAGE_TAG=latest
APP_PORT=3000

### -- URL SETTINGS -- ###
### Change these to the appropriate URLS.
### This is fine for testing on a local machine like Windows.
### But recommended to have this set as FQDN's such as:
### PUBLIC_URL=https://resume.example.com
### STORAGE_URL=https://storage.example.com/default

PUBLIC_URL=https://resume.yourdomain.com
STORAGE_URL=https://storage.yourdomain.com/default

## -- Development Settings -- ##
# NODE_ENV=development

## -- Printer (CHROME) SETTINGS -- ##
### Change this to the appropriate URL.
### This is fine for testing on a local machine like Windows.
### But recommended to have this set as a FQDN with web socket support such as:
### CHROME_URL=ws://printer.example.com
### OR
### CHROME_URL=wss://printer.example.com
### Leave this as is if using the traefik.yml file since that is how it was at default,
### but if you have issues, try using your domain name.

CHROME_URL=ws://printer.yourdomain.com

## -- AUTH -- ##
ACCESS_TOKEN_SECRET=access_token_secret ### You can change this to whatever you want
REFRESH_TOKEN_SECRET=refresh_token_secret ### You can change this to whatever you want

## -- EMAILS -- ##
MAIL_FROM=no-reply@example.com
SMTP_USER=example@example.com
SMTP_PASS=example-password-for-your-email-account
SMTP_ADDR=smtp.gmail.com
SMTP_PORT=587

## -- STORAGE (MINIO) -- ##
STORAGE_ENDPOINT=minio
STORAGE_PORT=9000
STORAGE_REGION=us-west-1
STORAGE_BUCKET=default
STORAGE_ACCESS_KEY=${MINIO_ROOT_PASSWORD} ### CHANGING THIS CAUSES ISSUES, Pending investigation
STORAGE_SECRET_KEY=${MINIO_ROOT_PASSWORD} ### CHANGING THIS CAUSES ISSUES, pending investigation
STORAGE_USE_SSL="false"
STORAGE_SKIP_BUCKET_CHECK="false"

## -- CROWDIN -- ##
## You can set your own Crowdin Project Information here if you want for translations, or wait for updates to get pushed to the new image. ##
CROWDIN_PROJECT_ID=
CROWDIN_PERSONAL_TOKEN=

## -- LOGIN PAGE -- ##
## Enable Signups = "false" | Disable Signups = "true" ##
# DISABLE_SIGNUPS="false"

## Enable Email Verification on Signup = "true" | Disable Email Verification on Signups = "false"
# DISABLE_EMAIL_AUTH="false"

## -- GITHUB OAUTH -- ##
GITHUB_CLIENT_ID=github_client_id
GITHUB_CLIENT_SECRET=github_client_secret
GITHUB_CALLBACK_URL=http://example.com/api/auth/github/callback

## -- GOOGLE OAUTH -- ##
GOOGLE_CLIENT_ID=google_client_id
GOOGLE_CLIENT_SECRET=google_client_secret
GOOGLE_CALLBACK_URL=http://example.com/api/auth/google/callback

## -- OPENID -- ##
VITE_OPENID_NAME=OpenID
OPENID_AUTHORIZATION_URL=
OPENID_ISSUER=
OPENID_TOKEN_URL=
OPENID_USER_INFO_URL=
OPENID_CALLBACK_URL=https://example.com/api/auth/openid/callback
OPENID_CLIENT_ID=
OPENID_CLIENT_SECRET=
OPENID_SCOPE=openid profile email
```

Make sure to update all the environment variables.

In this case, there's nothing much to change except for the following values:

```
POSTGRES_PASSWORD=postgres
MINIO_ROOT_PASSWORD=minioadmin
CHROME_TOKEN=chrome_token
PROXY_HOST=resume.example.com
PUBLIC_URL=http://localhost:3000
STORAGE_URL=https://storage.yourdomain.com/default
CHROME_URL=ws://printer.yourdomain.com
ACCESS_TOKEN_SECRET=access_token_secret
REFRESH_TOKEN_SECRET=refresh_token_secret
```

where you need to replace the values on the right side of the `=` sign. Make sure you generate or use strong random passwords or something similar for the Tokens, Postgres and Minio. Change the URL's to your respective domain.

Now, run the compose project by running the following command:

```
docker compose up -d
```

This should take about a few minutes depending on your server's network connection.

Once it is finished, run the logs command to check the logs to see if everything is running correctly:

```
docker compose logs -f
```

If you see something similar to the following output, it should mean everything is working as expected:

{% code lineNumbers="true" fullWidth="true" %}
```
app-1 | [Nest] 75  - 01/17/2025, 10:22:54 PM     LOG [Bootstrap] 🚀 Server is up and running on port xxxx
```
{% endcode %}

Now, just head over to `http://[your-server-ip]:81` and you should see Nginx Proxy Manager.&#x20;

Login to Nginx Proxy Manager with the default credentials of:\
Username=`admin@example.com`\
Password=`changeme`&#x20;

_Change your information if it asks you to. If it doesn't you can always do it from your Profile Icon in the Top Right Corner._

Navigate to `Hosts` > `Proxy Hosts`&#x20;

Setup your hosts similar to the picture below

<figure><img src="../.gitbook/assets/NGINX-Setup-Example.png" alt=""><figcaption></figcaption></figure>

**YOU ARE RESPONSIBLE FOR KNOWING HOW TO SETUP SSL, AND DNS RECORDS FOR YOUR DOMAIN!**

**If you have everything setup correctly, you should be able to access your Reactive Resume instance from your domain now.**

You can now create a new account, create a resume and print as PDF immediately.

You can also use the FREE and publicly available Reactive Resume at [https://rxresume.org](https://rxresume.org/)
