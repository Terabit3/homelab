# Pi-hole — Reverse Proxy Opens the Wrong Page

> **Historical troubleshooting note — unverified for current Pi-hole.** The original solution used Lighttpd, commonly associated with older Pi-hole deployments. Modern Pi-hole versions use a different web-server setup. Do **not** apply the Lighttpd steps to the current installation without checking its version and server.

## Symptom

Opening a Pi-hole custom domain through a reverse proxy displayed a placeholder page rather than the Pi-hole administration interface.

## What the old notes changed

- A Pi-hole Local DNS CNAME pointed the custom hostname toward the reverse-proxy host.
- Nginx Proxy Manager forwarded the hostname to the Pi-hole server on port 80.
- On the Pi-hole machine, a Lighttpd rule redirected requests outside `/admin` to `/admin`.

This approach only applies when the Pi-hole install actually uses Lighttpd, and it depends on the chosen DNS and reverse-proxy topology. A CNAME must target a **DNS hostname**, not a bare IP address.

## Safer troubleshooting checklist

1. Check whether the Pi-hole admin UI works directly at the current host and port.
2. Check which web server the installed Pi-hole version uses.
3. In Nginx Proxy Manager, confirm the proxy host's target, scheme, port, and HTTPS settings.
4. Verify local DNS points the custom hostname to the **reverse proxy**, not unintentionally to Pi-hole itself.
5. Confirm the app's expected `/admin` route, redirects, and any reverse-proxy-related settings for its installed version.
6. Re-test and record the exact fix that works today.

## Legacy-only Lighttpd rule

For historical context, an earlier deployment added `/etc/lighttpd/conf-enabled/15-pihole-custom-admin-redirect.conf`:

```lighttpd
$HTTP["url"] !~ "^/admin" {
    url.redirect = (".*" => "/admin")
}
```

The old procedure then restarted Lighttpd. **This is not a recommended fix for a modern Pi-hole container.** Preserve it only as a record of what the earlier deployment used.

## Source from old notes

- https://fullmetalbrackets.com/blog/reverse-proxy-using-nginx-pihole-cloudflare/#https
