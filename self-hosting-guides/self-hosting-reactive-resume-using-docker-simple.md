---
description: >-
  This should be an in-depth guide on how to set up Reactive Resume on your
  Virtual Private Server running a run-of-the-mill Linux distribution.
icon: docker
---

# Self-Hosting Reactive Resume using Docker - SIMPLE

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

## Docker Installation Methods

{% include "../.gitbook/includes/docker-installation-methods.md" %}

### Docker Compose & ENV Files

Copy the following code to the home folder (or any specific project folder) on your machine, to a file named `compose.yml`or `docker-compose.yml`. If you choose to name the file anything else, you would need to run the docker compose command along with the `-f [file path]` flag.

{% tabs %}
{% tab title="Docker Compose - Simple" %}
{% @github-files/github-code-block url="https://github.com/lazy-media/Reactive-Resume/blob/main/tools/compose/simple.yml" %}
{% endtab %}

{% tab title="ENV File - Simple Example" %}
{% @github-files/github-code-block %}
{% endtab %}
{% endtabs %}

### ENV File

Use the above .env example file. Replacing the values with the correct ones.

Make sure to update all the environment variables.

In this case, there's not much to change except for the following values:

```yaml
POSTGRES_PASSWORD=postgres
MINIO_ROOT_PASSWORD=minioadmin
CHROME_TOKEN=chrome_token
PUBLIC_URL=http://localhost:3000
ACCESS_TOKEN_SECRET=access_token_secret
REFRESH_TOKEN_SECRET=refresh_token_secret
```

where you need to replace the values on the right side of the `=` sign. Make sure you generate or use strong random passwords or something similar for the Tokens, Postgres and Minio.

### Starting & Running Reactive Resume

Now, run the compose project by running the following command:

```bash
docker compose up -d
```

This should take about a few minutes depending on your server's network connection.

Once it is finished, run the logs command to check the logs to see if everything is running correctly:

```bash
docker compose logs -f
```

If you see the following output, it means everything is working as expected

{% code lineNumbers="true" fullWidth="true" %}
```sh
app-1       | [Nest] 75  - 01/17/2025, 10:22:54 PM     LOG [Bootstrap] 🚀 Server is up and running on port 3000
```
{% endcode %}

Now, just head over to `http://[your-server-ip]:3000` and you should see Reactive Resume working as expected. You can now create a new account, create a resume and print as PDF immediately.

You can also use the FREE and publicly available Reactive Resume at [https://rxresume.org](https://rxresume.org/)
