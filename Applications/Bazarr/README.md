# Bazarr — October 15 subtitle provider recovery

Bazarr `v1.6.2`, container `bazarr`, LinuxServer image `lscr.io/linuxserver/bazarr:latest`.

## Findings

- Initial provider failures: TVSubtitles HTTP 404; OpenSubtitles.com AuthenticationError (12h cooldown); SubDL ConfigurationError; Anime Tosho missing AniDB integration warnings.
- TVSubtitles disabled. Website login and OpenSubtitles.com profile token validation passed. OpenSubtitles settings reconfigured; SubDL subsequently healthy.
- Final provider status screenshot: **Good** for `animetosho`, `animetosho_xyz`, `gestdown`, `opensubtitlescom`, `subdl`, `supersubtitles`, `yifysubtitles`.
- AniDB project **Servius Bazarr** was registered (Website Integration/Linux/Python). Registration of the named API client and saved metadata config not independently confirmed.
- Wanted count 365 → 355 but downloads not verified. A manual search on *The Boondocks* S04E10 returned subtitles with 93% / 86% scores; hash/release-group/service/audio-codec did not match. Scoring results are not proof of successfully saved subtitles.

## Verification

1. In **Wanted**, manually search representative TV, movie and anime items; select appropriately matched subtitle.
2. Confirm subtitle is written, correct language, and appears in **History**.
3. Review `Settings → Languages` profile assignment and subtitle minimum score before enabling mass searches.
4. Inspect `System → Providers` cooldowns; do not repeatedly reset provider throttling.

Read-only diagnostics:

```bash
sudo docker exec bazarr cat /app/bazarr/bin/VERSION
sudo docker logs --since 24h bazarr 2>&1 | grep -Ei 'error|warning|throttl|provider|opensubtitles|subdl' | tail -80
```

Basic HTTP authentication on LAN port 6767 is a browser popup; prefer HTTPS for credentials. Never commit passwords, provider cookies, API keys or session tokens.
