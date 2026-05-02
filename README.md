# GADROCRYPTO

Live crypto market treemap (BTC + top 60 coins by market cap), styled like coin360.com.

- Single static HTML file: `gadrocrypto.html`
- Pulls live USD prices and 24h/7d/30d % change from the public CoinGecko API
- No build step, no backend — just serve the file

## Deploy (Nginx)

Clone this repo to your server and point Nginx at it. See `deploy/gadrocrypto.conf` for the server block.

```bash
sudo git clone https://github.com/<your-user>/gadrocrypto.git /var/www/gadrocrypto
sudo cp /var/www/gadrocrypto/deploy/gadrocrypto.conf /etc/nginx/sites-available/gadrocrypto
sudo ln -s /etc/nginx/sites-available/gadrocrypto /etc/nginx/sites-enabled/gadrocrypto
sudo nginx -t && sudo systemctl reload nginx
```

## Update

```bash
cd /var/www/gadrocrypto && sudo git pull
```
