# Publish `org.jrebirth:organization` to Maven Central

Uses the [Central Publisher Portal](https://central.sonatype.org/publish/publish-portal-maven/) via profile **`deploy-central`** (`central-publishing-maven-plugin` + GPG).

Local `~/.m2/settings.xml` is **not** used by GitHub Actions. Copy the same Central token into repository secrets.

## One-time GitHub secrets

Repo → **Settings → Secrets and variables → Actions**:

| Secret | Value |
|---|---|
| `CENTRAL_USERNAME` | Central user-token username (`<server><id>central</id>` in settings.xml) |
| `CENTRAL_TOKEN` | Central user-token password |
| `GPG_PRIVATE_KEY` | ASCII-armored secret key (`gpg --export-secret-keys --armor KEYID`) |
| `GPG_PASSPHRASE` | Passphrase for that key |

Generate a Central token at [central.sonatype.com](https://central.sonatype.com/) → account → **View User Token**.

## Local publish (manual)

With `central` credentials and GPG already in `~/.m2/settings.xml`:

```bash
mvn -f org.jrebirth.organization/pom.xml -B -Pdeploy-central clean deploy
```

Default: upload + validate only (`autoPublish=false`). Then publish in the [Central deployments UI](https://central.sonatype.com/publishing/deployments).

Auto-publish (CI-like):

```bash
mvn -f org.jrebirth.organization/pom.xml -B -Pdeploy-central clean deploy \
  -Dcentral.publishing.autoPublish=true \
  -Dcentral.publishing.waitUntil=published
```

Eclipse launch **JRebirth Organization Deploy Central** activates `deploy-central`.

## GitHub Actions

Workflow: [`.github/workflows/release-maven-central.yml`](.github/workflows/release-maven-central.yml)

1. **Tag** (POM may still be SNAPSHOT; the job rewrites the version from the tag):

   ```bash
   git tag organization-6.0.0
   git push origin organization-6.0.0
   ```

2. **Manual**: Actions → **Release to Maven Central** → **Run workflow** → enter version (e.g. `6.0.0`).

The job signs artifacts, uploads the portal bundle, and waits until **published** (unless you uncheck auto-publish on a manual run).

## Notes

- Version must not contain `SNAPSHOT`.
- Namespace `org.jrebirth` must already be verified on the Central Portal.
- Snapshot / GitHub Packages deploy stays on workflow **Snapshot to GitHub Packages** (`-Pgithub-packages`).
