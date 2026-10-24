# renovate-config

Gedeelde [Renovate](https://docs.renovatebot.com/)-preset voor alle theremcode-repos.
Doel: altijd de nieuwste packages, modules, images en runtimes — npm/TypeScript,
Go, Python, Terraform, Docker en GitHub Actions — zodat bekende kwetsbaarheden
niet blijven liggen. Breekt een update de app, dan fixen we dat.

## Gebruik in een repo

Zet in de root van de repo een `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>theremcode/renovate-config"]
}
```

De Renovate GitHub App moet op de repo geïnstalleerd zijn
(<https://github.com/apps/renovate>).

## Wat de preset doet

| Regel | Waarom |
|---|---|
| Twee keer per week, maandag en donderdag voor 7:00, alle niet-majors in één PR | Bijblijven zonder dagelijkse ruis |
| Patch, minor, digest: automerge als **alle** CI-checks groen zijn | Actueel blijven zonder klikken |
| Majors: eigen PR, nooit automerge | Daar fixen we wat breekt |
| Pas na 1 dag (`minimumReleaseAge`) | Een gekaapte release wordt vaak binnen uren ontdekt en ingetrokken; één dag wachten vangt de meeste, zonder ver achter te lopen |
| Security-fixes (OSV): direct, buiten schema, automerge | Een bekende CVE wacht niet op maandag |
| Lockfile-onderhoud wekelijks | Ververst ook transitieve afhankelijkheden |
| `rangeStrategy: bump` | `package.json`, `go.mod` en Terraform-constraints laten zien wat er echt draait |
| Docker-images en Actions op digest/SHA | OS-patches in basisimages komen als PR binnen; Actions zijn niet onder je handen te vervangen |
| PostgreSQL-major: label `database-migratie` en waarschuwing | Een nieuwe major start niet op de oude datamap |
| Eigen images (`acrtheremcode.azurecr.io`, `ghcr.io/theremcode`) genegeerd | Die tagt de CI van de repo zelf |

## Belangrijk: automerge zonder branch protection

De repos zijn privé op een gratis account, dus GitHub-branch-protection en
GitHub-auto-merge zijn niet beschikbaar (`platformAutomerge: false`). Renovate
merget dan zelf, en alleen als alle statuschecks op de PR groen zijn. Een repo
**zonder CI krijgt geen automerge** — Renovate merget niet blind. Zet in zo'n
repo eerst CI op.

Merge naar `main` betekent in de meeste repos ook uitrol naar productie. Hoe
beter de CI (unit- én e2e-tests), hoe veiliger automerge is.

## Een regel per repo afwijken

Voeg in de `renovate.json` van die repo `packageRules` of `ignoreDeps` toe; die
gaan boven de preset. Bijvoorbeeld automerge uit:

```json
{
  "extends": ["github>theremcode/renovate-config"],
  "automerge": false,
  "packageRules": [{ "matchUpdateTypes": ["minor", "patch", "digest"], "automerge": false }]
}
```
