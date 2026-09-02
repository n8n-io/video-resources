# Tunnelling to n8n

📺 **[Watch the video](https://youtu.be/O9dpO81dEQ0)**


Companion resources for the "Tunnelling to n8n" video. Use these to give your local, Docker-based n8n a public URL for testing webhooks, with either Cloudflare Tunnels or ngrok.

> Tunnels are for testing external endpoints. Don't run production this way. For production, use n8n Cloud or self-host on a VPS with a real domain.

## Resources

- `.env.example` — environment variables for n8n and ngrok. Copy to `.env` and fill in.
- `docker-compose.yaml` — runs n8n and the ngrok agent together.

## Prerequisites

- Docker Desktop
- For Cloudflare: a domain added to your Cloudflare account
- For ngrok: a free ngrok account (authtoken and one reserved domain)

## Option A: Cloudflare Tunnel (named tunnel)

1. In Cloudflare Zero Trust: Networks, then Tunnels, create a tunnel, pick Cloudflared, name it, select docker from the connectors and copy the connector command.
2. Run the connector command (it should look like this):

   ```bash
   docker run -d --name cloudflared cloudflare/cloudflared:latest \
     tunnel --no-autoupdate run --token <YOUR_TUNNEL_TOKEN>
   ```

3. In the dashboard, add a public hostname (for example `n8n.yourdomain.com`) for the service type select HTTP as the protocol pointing to `http://host.docker.internal:5678`. Not http://localhost:5678. Cloudflare creates the DNS record for you.

4. Set `N8N_WEBHOOK_URL` to your Cloudflare subdomain and start n8n:

   ```bash
   docker run -d --name n8n -p 5678:5678 \
     -e N8N_WEBHOOK_URL=https://n8n.yourdomain.com/ \
     -e GENERIC_TIMEZONE=Europe/Berlin \
     -e TZ=Europe/Berlin \
     -e N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true \
     -v n8n_data:/home/node/.n8n \
     docker.n8n.io/n8nio/n8n
   ```

### Troubleshooting cloudflared 

If you get a 503 bad gateway when loading the public hostname. 
- Check that your service is using the correct hostname, not localhost. 
- Make sure both the cloudflared and n8n docker containers are running


## Option B: ngrok (Docker Compose)

1. In the ngrok dashboard, copy your authtoken and reserve a free dev domain, for example `your-name.ngrok-free.dev`.
2. Copy `.env.example` to `.env` and set `NGROK_AUTHTOKEN`, `NGROK_DOMAIN`, and `N8N_WEBHOOK_URL` & time zone to your reserved domain.
3. Start it:

   ```bash
   docker compose up
   ```

4. Open your domain in the browser to reach n8n. The request inspector is at http://localhost:4040.

## The key setting

n8n builds its webhook URLs from where it thinks it lives, which is localhost. Set `N8N_WEBHOOK_URL` to your public address and restart n8n, so the editor shows and registers the public webhook URLs.

`N8N_WEBHOOK_URL` replaces the older `WEBHOOK_URL`, which is deprecated from n8n 2.35.0 (it still works but logs a warning on startup).

## Links

- Install with Docker : https://docs.n8n.io/deploy/host-n8n/install-options/install-with-docker
- n8n endpoints (webhook URL) docs: https://docs.n8n.io/deploy/host-n8n/configure-n8n/basic-configuration/use-environment-variables/endpoints
- ngrok + n8n: https://ngrok.com/docs/universal-gateway/examples/n8n
- Cloudflare Tunnel: https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/