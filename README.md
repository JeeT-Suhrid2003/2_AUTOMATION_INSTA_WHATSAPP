# WhatsApp and Instagram automation

This project contains a small browser page for sending WhatsApp messages through
WAHA and two n8n workflows for Instagram posts.

## Requirements

- Docker with the Docker Compose plugin
- A WhatsApp account/session configured in WAHA
- For Instagram posting: an eligible Instagram professional account and a valid
  Instagram Graph API access token with the required publishing permissions
- For `insta2.json`: an imgbb API key

## Start the services

Review `docker-compose.yml` before starting the services. It currently contains
WAHA API and dashboard credentials directly in the file. Replace them with
private values or move them to an untracked `.env` file; do not commit secrets.
Treat credentials that have already been shared or committed as exposed and
rotate them.

From the project directory, start the containers:

```sh
docker compose up -d
```

- n8n: <http://localhost:5678>
- WAHA API: <http://localhost:3000>

The Compose configuration sets the timezone to `Asia/Kolkata`.

## WhatsApp message page

Open `index.html` in a browser. Enter the destination phone number with country
code and digits only, the message, and the WAHA API key. The default WAHA URL is
`http://localhost:3000`; change the session field if your connected WAHA session
has a different name.

The page sends the key to your configured WAHA server in the `X-Api-Key` header.
Use it only with a trusted server and avoid sharing screenshots or browser
details that expose the key. If the browser cannot connect, check that WAHA is
running and that its CORS settings allow requests from the page's origin.

## Instagram workflows

Import either JSON file into n8n using the workflow import option:

- `insta2.json` generates an image, uploads it to imgbb, creates an Instagram
  media container, waits 15 seconds, and publishes it.
- `instagram-posting.json` is a simpler create-and-publish workflow. Review the
  image URL and caption in its `IG Create Container` node before running it.

Both workflows include a manual trigger and a daily 9 AM schedule. The daily
schedule uses the n8n instance timezone.

The Instagram request nodes read `IG_ACCESS_TOKEN` from the n8n environment.
The imgbb upload node in `insta2.json` also reads `IMGBB_API_KEY`. Set these
variables privately and make them available to the n8n container; values in a
local `.env` file are not automatically passed into a container unless Compose
is configured to pass them through. Ensure your n8n configuration permits
workflow expressions to access environment variables, or use n8n credentials
instead. Restart n8n after changing its environment.

Do not put real tokens or API keys in workflow exports, source files, or this
README. If a credential has been exposed, revoke or rotate it before use.
