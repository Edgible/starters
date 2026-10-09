# starters

A starter is one self-hosted app, written as an Edgible card and tested end to end: Gitea, Umami, Vaultwarden, and so on. Starters are the pieces people build cards from. A [card](https://github.com/Edgible/cards) is a pattern, several apps wired together for a purpose; a starter is one of the apps, ready to drop into one.

Starters are made by an agent and reviewed by a person before they are merged. Every starter carries `test.yml`, the result of its last full run on a real Edgible serving device.

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

The format and the tools are in [Edgible/card-kit](https://github.com/Edgible/card-kit). Every pull request runs `check-cards`. The full lifecycle tests run on a self-hosted runner labelled `edgible-test`, on an Edgible serving device, and only from workflows on `main`: never from a pull request.

## License

[MIT](LICENSE). The apps a starter runs keep their own licenses.
