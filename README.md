# Website

## Prerequisites

* ghc == 9.14.1
* cabal-install == 3.18.2.0
* [gcloud](https://cloud.google.com/sdk/docs/install#de )

## Build

```bash
cabal build
```

## Run

```bash
cabal run
```

## Docker

### Build

```bash
# Docker Hub
docker image build -t asattelmaier/website:latest -f docker/Dockerfile .
# Google Cloud
docker image build -t eu.gcr.io/PROJECT_ID/website:latest -f docker/Dockerfile .
```

### Run

```bash
docker run -p 8000:8000 --name website asattelmaier/website:latest
```

### Push

```bash
# Docker Hub
docker push asattelmaier/website:latest
# Google Cloud
docker push eu.gcr.io/PROJECT_ID/website:latest
```

## Update GHC and Dependencies

1. Set `GHC_VERSION` and `CABAL_VERSION` in `.github/workflows/build-and-push.yml` and update
   the Prerequisites above.
2. Check for newer versions of the dependencies with `cabal outdated` and adjust the
   `^>=` bounds in `website.cabal` if needed. When changing GHC, set the `base` bound to the
   `base` version that comes with it (GHC 9.14.1: `base ^>=4.22.0.0`).
3. Regenerate the freeze file and build:

    ```bash
    cabal update
    rm cabal.project.freeze
    cabal freeze
    cabal build
    ```

## Security Updates

Only security fixes are automated; there are no routine version-update pull requests.

* **GitHub Actions** are referenced by major version tag (e.g. `actions/checkout@v7`), so fixes
  within a major version arrive without a pull request; `.github/dependabot.yml` turns off
  version updates. Under *Settings → Advanced Security* enable **Dependabot alerts**, leave
  **Dependabot security updates** off (it opens pull requests for alerts of any severity) and add
  the Dependabot rules severity `critical`, `high` → *Open a pull request to resolve this alert*
  and, to avoid e-mails for the others, severity `low`, `moderate` → *Dismiss alerts*.
* **Haskell packages** are not covered by Dependabot. When working on the site, check
  `cabal.project.freeze` against the
  [Haskell Security Advisory Database](https://github.com/haskell/security-advisories) and, if
  needed, do the update above:

    ```bash
    docker run --rm -v "$PWD:/src:ro" ghcr.io/google/osv-scanner:v2.7.0 scan source -L /src/cabal.project.freeze
    ```

  `HSEC-2023-0007` (`base`, no fixed version) is a known finding.
* The Docker base image `ubuntu:24.04` is intentionally not pinned to a digest, so every image
  build gets the current security patches; they reach the site only when the image is rebuilt,
  i.e. on a push to `master`.
