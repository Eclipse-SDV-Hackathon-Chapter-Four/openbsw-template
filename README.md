# OpenBSW Hackathon Starter

Starter repository for Eclipse SDV Hackathon teams building with
[Eclipse OpenBSW](https://github.com/eclipse-openbsw/openbsw), an open source SDK for automotive
embedded software.

OpenBSW is vendored at [`vendor/openbsw`](vendor/openbsw). Its complete upstream history is retained
under that prefix, so teams can work from one repository without a submodule or a separate clone.
The current snapshot is based on upstream commit
[`b423bf0`](https://github.com/eclipse-openbsw/openbsw/commit/b423bf051c594647f1305deec9a839f208c38545).

## Prerequisites

- Git
- Docker with Docker Compose (Docker Desktop is sufficient on macOS and Windows)

The provided development container includes the compilers and build tools. The POSIX/FreeRTOS
reference application runs without embedded hardware.

## Get started

### Preserve the vendored history (recommended)

First create an empty repository for your team. Clone this repository, then point the clone at your
team repository:

```sh
git clone git@github.com:Eclipse-SDV-Hackathon-Chapter-Four/openbsw-template.git <project-name>
cd <project-name>
git remote rename origin template
git remote add origin <your-empty-team-repository-url>
git push --set-upstream origin main
cd vendor/openbsw
```

GitHub repositories created with **Use this template** start with one new commit, so that route
copies the current files but not the vendored OpenBSW history. Use it only when your team prefers a
clean history over history-preserving vendor updates.

Start the development container from that directory:

```sh
DOCKER_UID="$(id -u)" DOCKER_GID="$(id -g)" docker compose run --build development
```

If your local UID and GID are both `1000`, the two environment variables can be omitted. Commands
below run inside the container.

### Build the host reference application with CMake

```sh
cmake --preset posix-freertos
cmake --build --preset posix-freertos --parallel
```

Run the resulting application; press `Ctrl-C` to stop it:

```sh
build/posix-freertos/executables/referenceApp/application/Release/app.referenceApp.elf
```

### Build and test with Bazel

```sh
bazel build //...
bazel test //...
```

For an embedded S32K148 build, add `--config=s32k148` to a Bazel command. See the
[OpenBSW documentation](https://eclipse-openbsw.github.io/openbsw/) for architecture, setup,
platform, and debugging guides.

## Where to start hacking

- [`vendor/openbsw/executables/referenceApp`](vendor/openbsw/executables/referenceApp) contains the
  reference application and its feature configuration.
- [`vendor/openbsw/libs`](vendor/openbsw/libs) contains reusable base-software libraries.
- [`vendor/openbsw/platforms`](vendor/openbsw/platforms) contains platform-specific integration.
- Keep team documentation, diagrams, and supporting tooling outside `vendor/openbsw` when they do
  not need to participate in the OpenBSW build.

Changes under `vendor/openbsw` are normal for hackathon projects. Keeping team commits separate from
vendor-update merge commits makes later upstream comparisons and updates easier.

## Updating the vendored OpenBSW history

Maintainers need [`josh-filter`](https://github.com/josh-project/josh) installed. The prefix filter
rewrites upstream paths beneath `vendor/openbsw`; merging the filtered ref retains that rewritten
history as a merge parent.

```sh
git remote add openbsw https://github.com/eclipse-openbsw/openbsw.git  # first update only
git fetch openbsw main
josh-filter ':prefix=vendor/openbsw' openbsw/main \
  --update refs/heads/vendor-openbsw
git merge --no-ff vendor-openbsw -m 'Merge Eclipse OpenBSW into vendor/openbsw'
```

After an update, change the pinned commit link near the top of this README and run the relevant
OpenBSW build and tests before pushing.

## Licensing and upstream contributions

The vendored OpenBSW project is licensed under the
[Apache License 2.0](vendor/openbsw/LICENSE); its attribution and legal notices remain in
[`vendor/openbsw/NOTICE.md`](vendor/openbsw/NOTICE.md). Review OpenBSW's
[contribution guide](vendor/openbsw/CONTRIBUTING.md) before proposing changes upstream.
