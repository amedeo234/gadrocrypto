# Deploying GADROCRYPTO to your Hetzner server

Stack assumed: Nginx already installed and running, served by IP (no domain), code pulled from a GitHub repo.

---

## Step 1 — Push the local files to GitHub (run on your laptop)

From the folder containing `gadrocrypto.html`, `README.md`, `.gitignore`, and `deploy/gadrocrypto.conf`:

```bash
git init
git add .
git commit -m "Initial GADROCRYPTO site"
git branch -M main
git remote add origin https://github.com/<your-user>/<your-repo>.git
git push -u origin main
```

(Create the empty repo on github.com first; no README/.gitignore on GitHub's side or the push will conflict.)

---

## Step 2 — On the Hetzner server (SSH in, then run)

### 2a. Clone the repo into the web root

```bash
sudo mkdir -p /var/www
sudo git clone https://github.com/<your-user>/<your-repo>.git /var/www/gadrocrypto
sudo chown -R www-data:www-data /var/www/gadrocrypto
```

### 2b. Install the Nginx server block

```bash
sudo cp /var/www/gadrocrypto/deploy/gadrocrypto.conf /etc/nginx/sites-available/gadrocrypto
sudo ln -sf /etc/nginx/sites-available/gadrocrypto /etc/nginx/sites-enabled/gadrocrypto

# Remove the stock default site so ours becomes the default
sudo rm -f /etc/nginx/sites-enabled/default

# Validate and reload
sudo nginx -t
sudo systemctl reload nginx
```

### 2c. Make sure port 80 is open

If you're using Hetzner Cloud Firewall, allow inbound TCP 80 in the Hetzner Cloud Console.
If you have ufw on the server:

```bash
sudo ufw allow 80/tcp
```

---

## Step 3 — Visit your site

Open `http://<your-server-ip>/` in a browser. You should see the GADROCRYPTO treemap loading live data.

---

## Updating the site later

After pushing changes to GitHub:

```bash
ssh <user>@<server-ip>
cd /var/www/gadrocrypto && sudo git pull
```

Nginx serves the updated file immediately — no restart needed for HTML changes.

---

## Optional: auto-pull every minute

```bash
sudo crontab -e
# add this line:
* * * * * cd /var/www/gadrocrypto && /usr/bin/git pull --quiet
```

---

## Troubleshooting

- **`nginx -t` fails** — copy/paste the error message; usually a syntax issue or the config wasn't copied correctly.
- **502 / blank page** — check `sudo tail -f /var/log/nginx/error.log`.
- **Site loads but no data** — open browser devtools → Network tab. CoinGecko's free API can rate-limit; the page retries every 60s.
- **Browser shows "Not secure"** — that's expected over HTTP. If you later add a domain, I can wire up free Let's Encrypt HTTPS.
