# bein-epg

Self-updating XMLTV electronic program guide for beIN Sports MENA channels, scraped from beinsports.com.

## EPG URL

Paste this into your IPTV player as the EPG / TV-guide source:

```
https://<YOUR_USERNAME>.github.io/bein-epg/guide.xml
```

The file regenerates every 12 hours via GitHub Actions. Players that cache the EPG will pick up new data on their next refresh.

## How it works

- `.github/workflows/update-epg.yml` runs on a cron (every 12h) and on manual dispatch.
- It checks out [iptv-org/epg](https://github.com/iptv-org/epg) into a working dir, installs deps, and calls the `beinsports.com` scraper against the MENA-EN channel set.
- Output is written to `docs/guide.xml` and committed back to `main`.
- GitHub Pages serves `docs/` as a static site, so the XML is publicly fetchable at a stable URL.

## Channels included

24 channels currently have program data. Use these `tvg-id` values in your M3U:

| Channel | tvg-id |
|---|---|
| beIN SPORTS | `beINSports.qa@MENA` |
| beIN SPORTS 1 | `beINSports1.qa@MENA` |
| beIN SPORTS 2 | `beINSports2.qa@MENA` |
| beIN SPORTS 3 | `beINSports3.qa@MENA` |
| beIN SPORTS 4 | `beINSports4.qa@MENA` |
| beIN SPORTS 5 | `beINSports5.qa@MENA` |
| beIN SPORTS 6 | `beINSports6.qa@MENA` |
| beIN SPORTS 7 | `beINSports7.qa@MENA` |
| beIN SPORTS 8 | `beINSports8.qa@MENA` |
| beIN SPORTS MAX 1 | `beINSportsMax1.qa@MENA` |
| beIN SPORTS MAX 2 | `beINSportsMax2.qa@MENA` |
| beIN SPORTS MAX 3 | `beINSportsMax3.qa@MENA` |
| beIN SPORTS MAX 4 | `beINSportsMax4.qa@MENA` |
| beIN SPORTS MAX 5 | `beINSportsMax5.qa@MENA` |
| beIN SPORTS MAX 6 | `beINSportsMax6.qa@MENA` |
| beIN SPORTS NEWS | `beINSportsNews.qa@SD` |
| beIN SPORTS NBA | `beINSportsNBA.qa@SD` |
| beIN 4K | `beIN4K.qa@SD` |
| beIN SPORTS XTRA 1 | `beINSportsXtra1.qa@SD` |
| beIN SPORTS XTRA 2 | `beINSportsXtra2.qa@SD` |
| beIN SPORTS AFC | `beINSportsAFC.qa@SD` |
| beIN SPORTS 1 AFC | `beINSportsAFC1.qa@SD` |
| beIN SPORTS 2 AFC | `beINSportsAFC2.qa@SD` |
| beIN SPORTS 3 AFC | `beINSportsAFC3.qa@SD` |

## What is `tvg-id`?

Your IPTV playlist is an `.m3u` file where each channel line has metadata. The `tvg-id` attribute is the binding between a stream and a guide entry — when it matches an EPG `<channel id="...">`, the player shows that channel's schedule.

Example M3U entry:

```
#EXTM3U x-tvg-url="https://<YOUR_USERNAME>.github.io/bein-epg/guide.xml"
#EXTINF:-1 tvg-id="beINSports1.qa@MENA" tvg-name="beIN SPORTS 1" group-title="beIN Sports",beIN SPORTS 1
http://your-iptv-provider/stream/beinsports1
```

The `x-tvg-url` header on the first line tells the player where to pull the EPG; the per-channel `tvg-id` matches each stream to its schedule.

## Manual refresh

```sh
gh workflow run update-epg.yml -R <YOUR_USERNAME>/bein-epg
```

Or click "Run workflow" in the Actions tab on github.com.

## Adding or changing channels

Edit `channels/beinsports.com_mena-en.channels.xml`:

- Add an `xmltv_id` to channels currently set to `""` to include them in the guide.
- Pick any string — it just needs to match the `tvg-id` in your M3U.
- Commit and push; the workflow runs automatically on changes to that file.

To switch regions (e.g. Australia, France, US), grab the corresponding file from [iptv-org/epg/sites/beinsports.com](https://github.com/iptv-org/epg/tree/master/sites/beinsports.com) and update the workflow path.

## Credit

EPG scraping logic from [iptv-org/epg](https://github.com/iptv-org/epg). Source data: beinsports.com public TV-guide API.
