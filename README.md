# starters

A starter is one self-hosted app, written as an Edgible card and tested end to end: Gitea, Umami, Vaultwarden, and so on. Starters are the pieces people build cards from. A [card](https://github.com/Edgible/cards) is a pattern, several apps wired together for a purpose; a starter is one of the apps, ready to drop into one.

Starters are made by an agent and reviewed by a person before they are merged. Every starter carries `test.yml`, the result of a full run of that version on a real Edgible serving device, and has a status badge below that says whether it still passes.

## The starters

| Starter | What | Status |
| --- | --- | --- |
| [gitea](gitea) | Gitea, your own Git forge, with Git over HTTPS and SSH | [![gitea status](https://img.shields.io/endpoint?url=https%3A%2F%2Fraw.githubusercontent.com%2FEdgible%2Fstarters%2Ftest-status%2Fgitea.json)](https://github.com/Edgible/starters/blob/test-status/status.json) |
| [immich](immich) | Immich, your photo and video library, for its phone apps and the web | [![immich status](https://img.shields.io/endpoint?url=https%3A%2F%2Fraw.githubusercontent.com%2FEdgible%2Fstarters%2Ftest-status%2Fimmich.json)](https://github.com/Edgible/starters/blob/test-status/status.json) |
| [jellyfin](jellyfin) | Jellyfin, your films, shows and music, streamed to its apps and the web | [![jellyfin status](https://img.shields.io/endpoint?url=https%3A%2F%2Fraw.githubusercontent.com%2FEdgible%2Fstarters%2Ftest-status%2Fjellyfin.json)](https://github.com/Edgible/starters/blob/test-status/status.json) |
| [linkding](linkding) | linkding, a minimal bookmark manager, for your browsers and its extension | [![linkding status](https://img.shields.io/endpoint?url=https%3A%2F%2Fraw.githubusercontent.com%2FEdgible%2Fstarters%2Ftest-status%2Flinkding.json)](https://github.com/Edgible/starters/blob/test-status/status.json) |
| [minecraft](minecraft) | A Minecraft server for friends on PC over TCP and on phones over UDP, one world | [![minecraft status](https://img.shields.io/endpoint?url=https%3A%2F%2Fraw.githubusercontent.com%2FEdgible%2Fstarters%2Ftest-status%2Fminecraft.json)](https://github.com/Edgible/starters/blob/test-status/status.json) |
| [miniflux](miniflux) | Miniflux, the minimalist feed reader, behind your org login | [![miniflux status](https://img.shields.io/endpoint?url=https%3A%2F%2Fraw.githubusercontent.com%2FEdgible%2Fstarters%2Ftest-status%2Fminiflux.json)](https://github.com/Edgible/starters/blob/test-status/status.json) |
| [stirling-pdf](stirling-pdf) | Stirling-PDF, the PDF toolbox, behind your org login | [![stirling-pdf status](https://img.shields.io/endpoint?url=https%3A%2F%2Fraw.githubusercontent.com%2FEdgible%2Fstarters%2Ftest-status%2Fstirling-pdf.json)](https://github.com/Edgible/starters/blob/test-status/status.json) |
| [umami](umami) | Umami analytics, the dashboard behind org and the tracking script open | [![umami status](https://img.shields.io/endpoint?url=https%3A%2F%2Fraw.githubusercontent.com%2FEdgible%2Fstarters%2Ftest-status%2Fumami.json)](https://github.com/Edgible/starters/blob/test-status/status.json) |
| [uptime-kuma](uptime-kuma) | Uptime Kuma, the uptime monitor, behind your org login | [![uptime-kuma status](https://img.shields.io/endpoint?url=https%3A%2F%2Fraw.githubusercontent.com%2FEdgible%2Fstarters%2Ftest-status%2Fuptime-kuma.json)](https://github.com/Edgible/starters/blob/test-status/status.json) |
| [vaultwarden](vaultwarden) | Vaultwarden, a Bitwarden-compatible password server, for your own apps and browsers | [![vaultwarden status](https://img.shields.io/endpoint?url=https%3A%2F%2Fraw.githubusercontent.com%2FEdgible%2Fstarters%2Ftest-status%2Fvaultwarden.json)](https://github.com/Edgible/starters/blob/test-status/status.json) |
| [wordpress](wordpress) | WordPress with MariaDB, installed before it goes public | [![wordpress status](https://img.shields.io/endpoint?url=https%3A%2F%2Fraw.githubusercontent.com%2FEdgible%2Fstarters%2Ftest-status%2Fwordpress.json)](https://github.com/Edgible/starters/blob/test-status/status.json) |

The status is the latest lifecycle test of each starter, on a real Edgible serving device, and the Edgible version it ran on. A starter is tested again when it changes and when Edgible releases a new version, so a green badge with the current version means it works today.

## What a starter is

A starter follows [the card format](https://github.com/Edgible/card-kit#what-a-card-is) exactly, with these additions:

- **One app.** The app may sit on more than one hostname, such as Umami's dashboard and its tracking script, and it brings its own database or cache. It does not bring a second app.
- **Names that say whose they are.** Every variable, service, and volume is prefixed with the app: `GITEA_DB_PASSWORD`, a service `gitea-db`, a volume `gitea-db-data`. No `container_name`. Two starters then drop into one Compose file without a clash.
- **Pinned images.** Each image is the exact version that passed `test.yml`. A newer version replaces it only once it passes too.
- **`test.yml`, always, with `result: pass`.** It is committed only when the result, a step, or the images change ([the rule](https://github.com/Edgible/card-kit#test-results)).
- **A short README.** Why is what the app is, with a link to its docs. What is its services, its hostnames, and what it needs to be combined into a card. How, Verify, and Tear down are as in any card.

## Choosing the auth mode

The auth mode belongs to each hostname, and the right one depends on who calls it.

| Caller | Auth mode |
| --- | --- |
| A person in a browser, for an admin or personal interface | `org` |
| An app's own phone app, desktop client, or browser extension, which cannot get past an Edgible sign-in | `none`, with the app's own login as the protection, and the starter says so |
| Another machine calling an API | `api-key` |
| The public, such as a site, a tracking script, or a webhook | `none` |
| A game, SSH, or another `tcp` or `udp` service | `none`, the only mode those take; the app's own checks decide who gets in |

`org` is not automatically the safe choice. A starter on `org` that its own phone app cannot reach does not work, and the README is where the trade-off is written down.

## What is not a starter

Some apps should not be put on a public hostname at all, however popular they are. The agent skips them and says why:

- Services that would serve anyone and can be abused: an open DNS resolver such as Pi-hole, an open mail relay, an open proxy.
- Apps with no maintained container image.
- Apps whose project is archived.

## Reviewing a starter

A person reviews every starter before it is merged. The agent cannot judge these, so the pull request lists them:

- [ ] Default passwords are changed or generated, and the README says how to sign in the first time.
- [ ] Every secret is empty in git, with a `# Generate with:` comment.
- [ ] The auth mode of each hostname suits its callers, by the table above.
- [ ] The README says where the data lives, and Tear down backs it up before deleting it.
- [ ] `test.yml` passes, and its images are the ones in the Compose file.
- [ ] Nothing names a device, a hostname, or an organization.

## Using starters in a card

Copy the services of each starter into your card's Compose file, and their lines into its `card.env`. The prefixes keep them apart. Then add what only you know: how the apps reach each other, which place each runs in, and the story in Why. Say in the card's README which starters it was built from.

## Tools and tests

The format and the tools are in [Edgible/card-kit](https://github.com/Edgible/card-kit). Every pull request runs `check-cards --fresh`, which fails a starter whose files changed since its `test.yml`: run `test-card` and commit the new one. The lifecycle tests run with `test-cards` on an Edgible serving device, from `main` only, never from a pull request. They write the status to the [`test-status` branch](https://github.com/Edgible/starters/tree/test-status) and never commit to `main`; a failing starter gets an issue.

## Contributing

How to add a starter, fix one, or ask for one is in [CONTRIBUTING.md](CONTRIBUTING.md).

## License

[MIT](LICENSE). The apps a starter runs keep their own licenses.
