# Expose Local Ollama via Cloudflare Tunnel (Step-by-Step)

Use this guide to run Ollama on your local machine and expose it securely over the internet using a Cloudflare tunnel, so services like Railway can call your local Ollama instance.

---

## 1. Install Ollama (if not already)

```bash
# Install Ollama
curl -fsSL https://ollama.com/install.sh | sh

# Start the Ollama service
sudo systemctl start ollama
```

---

## 2. Pull a Model

```bash
ollama pull llama3
```

You can replace `llama3` with any model you prefer (e.g., `llama3.2`, `phi3`, `mistral`).

---

## 3. Allow CORS / Any Origin

Ollama restricts origins by default. To allow browser-based or cross-origin clients:

```bash
# Create systemd override directory
sudo mkdir -p /etc/systemd/system/ollama.service.d

# Add OLLAMA_ORIGINS=* override
printf '[Service]\nEnvironment="OLLAMA_ORIGINS=*\n' | sudo tee /etc/systemd/system/ollama.service.d/override.conf

# Reload systemd and restart Ollama
sudo systemctl daemon-reload
sudo systemctl restart ollama

# Verify the environment variable is set
systemctl show ollama -p Environment | grep OLLAMA_ORIGINS
```

---

## 4. Install `cloudflared`

```bash
# Download latest cloudflared (Linux amd64)
curl -L https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64 -o cloudflared

# Make it executable and move to PATH
chmod +x cloudflared
sudo mv cloudflared /usr/local/bin/

# Verify installation
cloudflared --version
```

---

## 5. Start the Tunnel (Critical: Host Header)

```bash
cloudflared tunnel --url http://localhost:11434 --http-host-header=localhost:11434
```

**Why `--http-host-header=localhost:11434`?**  
Ollama validates the `Host` header and rejects requests that aren’t `localhost` or `127.0.0.1`. Without this flag, tunnel requests return `403 Forbidden`.

The command will print a public URL like:

```text
https://leader-rendering-thou-wednesday.trycloudflare.com
```

Keep this terminal running while you want the tunnel active.

---

## 6. Use the Tunnel URL in Railway (or Other Services)

In your Railway (or other platform) environment variables:

```env
OLLAMA_URL=https://leader-rendering-thou-wednesday.trycloudflare.com
```

Example Node.js client:

```js
const res = await fetch(`${process.env.OLLAMA_URL}/api/generate`, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    model: "llama3",
    prompt: "Hello from Railway!"
  })
});

const data = await res.json();
console.log(data);
```

---

## ⚠️ Important Notes

| Issue                     | Description / Mitigation                                                                 |
|---------------------------|-------------------------------------------------------------------------------------------|
| Tunnel URL changes        | The `trycloudflare.com` URL changes every time you restart the tunnel.                   |
| Public / unsecured        | Anyone with the URL can call your Ollama instance while the tunnel is running.           |
| PC must stay on           | Your local machine must remain on and connected for the tunnel to work.                  |
| Rate limits / abuse risk  | Expose only for trusted clients or short testing windows unless you add auth / firewall. |

---

## Optional: Permanent URL with a Named Tunnel

For a stable URL (and optionally your own domain):

```bash
# Login to Cloudflare
cloudflared tunnel login

# Create a named tunnel
cloudflared tunnel create ollama-tunnel

# (Optional) Route a custom domain
cloudflared tunnel route dns ollama-tunnel ollama.yourdomain.com

# Run the named tunnel
cloudflared tunnel run ollama-tunnel
```

You can then configure a Cloudflare config file (`~/.cloudflared/config.yml`) to run the tunnel as a systemd service for persistence.

---

## Troubleshooting Tips

- **403 from Ollama:** Ensure you used `--http-host-header=localhost:11434`.
- **CORS errors in browser:** Confirm `OLLAMA_ORIGINS=*` is active (`systemctl show ollama -p Environment`).
- **Tunnel won’t start:** Check firewall/SELinux and that port 11434 is listening (`ss -tlnp | grep 11434`).