# GitHub Packages SNAPSHOTs (JRebirth org)

## Bootstrap order

1. Merge this repo (`JRebirth-Organization-POM`) — workflow `Snapshot to GitHub Packages` on `master`.
2. Confirm package `org.jrebirth:organization:6.0.0-SNAPSHOT` under Packages.
3. Merge `JRebirth-RIA-POM` and `JRebirth-Skin` (same workflow pattern).
4. Merge `JRebirth` branch `12.x-dev` — deploys `org.jrebirth.af:*:12.0.0-SNAPSHOT`.
5. Merge app/demo repos; switch consumers (e.g. ezDojo) to resolve from Packages.

## Reusable workflow

- [`.github/workflows/reusable-maven-snapshot.yml`](.github/workflows/reusable-maven-snapshot.yml)
- [`.github/maven-settings.xml`](.github/maven-settings.xml) — server ids `github`, `github-jrebirth-*`

Caller example (other repos):

```yaml
jobs:
  snapshot:
    uses: JRebirth/JRebirth-Organization-POM/.github/workflows/reusable-maven-snapshot.yml@master
    with:
      java-version: "25"
      pom-path: path/to/pom.xml
      maven-args: "-B -Pgithub-packages deploy -DskipTests"
    secrets: inherit
```

RIA-based apps must use `-Pgithub-packages,-OJO` so the default OJO profile does not override deploy URLs.

## Consumer `settings.xml`

```xml
<server>
  <id>github-jrebirth-af</id>
  <username>x-access-token</username>
  <password>YOUR_PAT_WITH_read:packages</password>
</server>
```

Repeat for `github-jrebirth-org` and `github-jrebirth-ria` as needed.

## Maven profile

`-Pgithub-packages` sets `distributionManagement` to
`https://maven.pkg.github.com/JRebirth/<this-repo>`.
