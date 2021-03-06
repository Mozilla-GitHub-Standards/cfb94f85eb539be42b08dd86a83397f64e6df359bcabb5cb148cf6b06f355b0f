# Android l10n tooling

This repository hosts code to create and maintain the android-l10n repository.
It provides both cross-product and cross-branch strings, joining the
strings in several `strings.xml` files from different repositories and
branches.

The best way to run this is to create the docker image, and run the process
inside a docker container.

```sh
docker build -t android-l10n-tooling .
```

## Exporting

This is the process to transfer strings from the Android project repositories
onto the `android-l10n` strings-only repository.

Outside of the container, you want to have the `android-l10n` repository.
There you should make manual git changes, like changes to the `config.toml`
file. That way, you don't need to set up your git identity inside the container.

We're using `android-components` as an example on how to update the quarantine
branch of an Android project. Adjust the organization and repository name to
match the project you're actually working on. Here are the steps, first, fire
up the container.

```sh
docker run --rm -it -v /src/experiments/android-l10n:/workdir/android-l10n android-l10n-tooling bash
```

Make sure to adjust the path to your `android-l10n` repository you have on
your machine.

Then, inside the container, run:

```sh
create-l10n-branch --pull --repo mozilla-mobile/android-components --branch android-components-quarantine android-l10n/
```

This should pull a new clone of `android-components` in your container, and
update the `android-l10n` repository on your machine. It will create or
maintain the `android-components-quarantine` branch.

If you'd like to check the result of the conversion for a local repository
state of yours, you want to modify the `branch` in `config.toml`, and mount
your local clone to `/workdir/mozilla-mobile/android-components` when starting
the docker container.

## Importing

This is the process to get strings from the `android-l10n` strings-only
repository back into the Android project repositories. These commands
are run in the same docker container as for exporting.

Outside of the container, you want to have your project repository, and
mount that while running the command. Same goes for the `android-l10n`
repository.

Then, inside the container, you run

```sh
import-android-l10n android-l10n/mozilla-mobile/android-components/l10n.toml mozilla-mobile/android-components
```

The first argument is the `l10n.toml` for the project as it lives inside
`android-l10n`. The second argument is the path to the local checkout of the
project repository.

This command doesn't do anything related to git, so you want to check out a 
git branch before running it, and add/commit afterwards.
