# Contributing a starter

There are three ways to help: add a starter, fix one, or ask for one. Each follows the format in [README.md](README.md#what-a-starter-is) and in [Edgible/card-kit](https://github.com/Edgible/card-kit). What is written there wins over anything here.

## Ask for a starter

Open an issue from the **Starter request** template. Name the app and link it, ideally on [awesome-selfhosted](https://awesome-selfhosted.net). Say what you would use it for and who calls it: a browser, the app's own phone or desktop apps, or another machine. That decides the auth mode. If you tried to build it, say where you got stuck.

## Add a starter

You need Docker, an Edgible account with a serving device, and the `edgible` CLI logged in. `test-card` runs the whole lifecycle on that device.

1. **Check the app and the name.** The app must not be on the README's [What is not a starter](README.md#what-is-not-a-starter) list. A 404 from this command means the name is free:

   ```bash
   gh api repos/Edgible/starters/contents/<name> --jq .name
   ```

2. **Fork and clone,** with card-kit beside it. Work on a branch, never on `main`:

   ```bash
   gh repo fork Edgible/starters --clone
   git clone https://github.com/Edgible/card-kit
   cd starters && git checkout -b <name>
   ```

3. **Write the starter.** Copy the starter closest to yours and change the names. The rules are in the README's [What a starter is](README.md#what-a-starter-is) and card-kit's [Conventions](https://github.com/Edgible/card-kit#conventions). Write the README with `card-readme`:

   ```bash
   ../card-kit/run card-readme <name> <name>-readme.yml
   ```

   The `<name>-readme.yml` it reads holds the parts only you know. Keep it out of the commit.

4. **Test it** on your serving device until it passes, and commit the `test.yml` it writes. Put the values only a person may fill in `test/inputs.env`, and the app's own end-to-end checks in `test/<check>.sh`:

   ```bash
   python3 ../card-kit/test-card.py <name> --device <your-device>
   ../card-kit/run check-cards --fresh
   ```

   `check-cards --fresh` fails a starter whose files changed after its `test.yml`, so test again after any change.

5. **Check nothing private is left:** no device name, hostname, organization id, password, or your own domain, in any file.

6. **Open the pull request** from your fork. The template lists the review checklist: tick each item you checked.

## Fix a starter

Use the same steps from 2: fork, branch, change, `test-card`, `check-cards --fresh`, pull request. Say what was wrong and how the test shows the fix. A failing starter has an issue already when the nightly test found it. Link to it.

## What happens next

A maintainer reviews the checklist, the parts a test cannot judge, and runs `test-card` on an Edgible device before merging. Lifecycle tests never run on a pull request. After the merge, the nightly `test-cards` run tests the starter whenever it or Edgible changes. When it fails, it gets an issue.

## Made with an AI agent

That is welcome. The [edgible-cards skill](https://github.com/Edgible/card-kit/tree/main/skills/edgible-cards) follows this file. Say in the pull request that an agent made it. You are still the one asking for the merge, so read what it wrote.

## Licence

This repository is [MIT](LICENSE). By contributing, you agree that your contribution is under the same licence. The apps a starter runs keep their own licences.
