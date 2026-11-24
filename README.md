# Open-Source Secrets Management Landscape

> **Decision:** Use this landscape to choose a secret-delivery pattern per consumer and document its root of trust; skip it if one vault product is expected to solve human, repository, CI, Kubernetes, and runtime needs identically.
>
> **For:** developers, platform engineers, and operators designing secret custody and delivery  ·  **Not for:** teams without rotation and recovery ownership or seeking a popularity ranking

This is a non-commercial, source-traceable decision guide. It normalizes upstream declarations; it does not claim that repository reachability proves functionality, security, maintenance, or fitness.

**Start here:** [Download the secret delivery boundary ledger](assets/secret-plaintext-boundary-map.csv) — twelve projects, located upstream evidence, and explicit unknowns.

## Choose by workload

- Human credentials: Vaultwarden, Bitwarden Server, Passbolt, or Psono, after checking edition and client compatibility.
- Reviewable encrypted configuration: SOPS or age; gopass for Git-backed team CLI workflows.
- Kubernetes GitOps: Sealed Secrets encrypts for a cluster; External Secrets Operator retrieves from an external provider.
- Runtime leases and dynamic credentials: OpenBao. Machine identity, policy and secret retrieval: Conjur; lease parity is not assumed.
- Developer project/environment workflow: Infisical, with its ee license boundary explicitly checked.

## Locate plaintext and trust roots

Human vaults, encrypted files and runtime servers have different roots. OpenBao supplies leases/dynamic engines; Conjur is shortlisted for machine identity, policy and retrieval, not presumed lease parity.

## Plaintext-hop map

- **Human vaults** — Vaultwarden, Bitwarden Server, Passbolt, and Psono serve people/devices; account recovery and client compatibility dominate.
- **Files and Git** — SOPS encrypts selected structured values, age is a primitive, and gopass combines encryption with Git. None supplies dynamic leases by itself.
- **Kubernetes delivery** — Sealed Secrets stores ciphertext decryptable by a cluster controller; External Secrets Operator synchronizes from an external provider. Their disaster-recovery roots differ.
- **Runtime secret servers** — OpenBao and Conjur authenticate workloads and centralize policy; OpenBao also supplies documented dynamic engines; bootstrap and HA are core design work.
- **Developer platform** — Infisical organizes projects/environments and delivery; distinguish MIT code from the separately licensed ee directories.

## Secret delivery boundary ledger

| Project | Role | Consumer and plaintext location | Identity or delivery mechanism | Recovery and root material |
|---|---|---|---|---|
| Vaultwarden | human password vault | Unknown in the frozen upstream README | An alternative server implementation of the Bitwarden Client API, written in Rust and compatible with official Bitwarden clients [disclaimer], perfect for self-… | Please note: We cannot be held liable for any data loss that may occur while using Vaultwarden. This includes passwords, attachments, and other information hand… |
| Bitwarden Server | official human password vault | Unknown in the frozen upstream README | Please refer to the Server Setup Guide in the Contributing Documentation for build instructions, recommended tooling, code style tips, and lots of other great i… | The Bitwarden Server project contains the APIs, database, and other core infrastructure items needed for the "backend" of all bitwarden client applications. |
| Passbolt | team password vault | The open source password manager for teams. | - Security: Passbolt security model features user-owned secret keys and end-to-end encryption. It is audited multiple times annually, and findings are made publ… | - Security: Passbolt security model features user-owned secret keys and end-to-end encryption. It is audited multiple times annually, and findings are made publ… |
| SOPS | encrypted configuration files | SOPS is an editor of encrypted files that supports YAML, JSON, ENV, INI and BINARY | Unknown in the frozen upstream README | Unknown in the frozen upstream README |
| age | file-encryption primitive | File-encryption primitive with caller-managed ciphertext and keys | It features small explicit keys, post-quantum support, no config options, and UNIX-style composability. | It features small explicit keys, post-quantum support, no config options, and UNIX-style composability. |
| OpenBao | runtime secret server | OpenBao is a software solution to manage, store, and distribute sensitive | OpenBao is a software solution to manage, store, and distribute sensitive | data including secrets, certificates, and keys. The OpenBao community intends to provide this software under an OSI-approved open-source license, led by a commu… |
| Infisical | developer secret platform | Infisical is the open source security infrastructure platform that teams use for secrets, certificates, privileged access management, and AI agent access. | - Dynamic Secrets: Generate ephemeral secrets on-demand for services like PostgreSQL, MySQL, RabbitMQ, and more. | Keep cryptographic keys in Infisical and use them through the API, so your applications never hold the keys. |
| Psono | team password vault | settings.yaml file as it contains some secrets necessary to decrypt the data in your database. We have created two little scripts to backup and restore your fil… | Unknown in the frozen upstream README | For a full backup, you have to backup two things. First thing obviously is the database, and the second thing is your settings.yaml file as it contains some sec… |
| Sealed Secrets | Kubernetes encrypted manifest controller | Problem: "I can manage all my K8s config in git, except Secrets." | decrypted only by the controller running in the target cluster and nobody else (not even the original author) is able to obtain the original Secret from the Sea… | - SealedSecrets as templates for secrets - Public key / Certificate |
| External Secrets Operator | Kubernetes secret synchronizer | External Secrets Operator is a Kubernetes operator that integrates external secret management systems like AWS Secrets Manager, HashiCorp Vault, [Google Secrets | Unknown in the frozen upstream README | Unknown in the frozen upstream README |
| gopass | Git-backed team CLI vault | > The slightly more awesome standard UNIX password manager for teams. | Gopass is a drop-in replacement for pass, the standard UNIX password manager. By default your credentials are encrypted with GPG and versioned in git. This can … | Unknown in the frozen upstream README |
| Conjur | runtime secret and identity server | Unknown in the frozen upstream README | Unknown in the frozen upstream README | Do NOT lose the data key, or all the encrypted data will be unrecoverable. |

Values are short excerpts from the frozen upstream README, not feature-test scores. The CSV retains the located evidence for each cell. “Unknown” means the retrieved document did not establish that axis; it is not a claim that the feature is absent.

## Keep the authority path explicit

Draw the secret path from generation to consumer and mark every plaintext hop, cache, log, backup, and human recovery step. Rotate one canary secret, revoke one identity, lose one node, and restore one encrypted backup. Never store the only decryptor beside the ciphertext it protects.

## Adoption notes by project

### Vaultwarden

- **Choose it for:** Small self-hosters wanting Bitwarden client compatibility.
- **License at the frozen source:** [AGPL-3.0](https://raw.githubusercontent.com/dani-garcia/vaultwarden/baff1cabae39f55fd5111d0724d1389d08d00a63/LICENSE.txt).
- **Adoption test:** It is an independent implementation; test client compatibility, admin-token handling, backups, attachments, and emergency export.
- **Source:** [dani-garcia/vaultwarden](https://github.com/dani-garcia/vaultwarden).

### Bitwarden Server

- **Choose it for:** Organizations wanting the upstream server stack.
- **License at the frozen source:** [mixed: AGPL-3.0 main server plus Bitwarden source-available licensed directories](https://raw.githubusercontent.com/bitwarden/server/f0bcb2c70914f1e11a9b335e5c0bae59da599d48/LICENSE.txt).
- **Adoption test:** Main server is AGPL, while bitwarden_license directories/features are source-available and production use may require a subscription; review LICENSE_FAQ.
- **Source:** [bitwarden/server](https://github.com/bitwarden/server).

### Passbolt

- **Choose it for:** Teams centered on controlled password sharing.
- **License at the frozen source:** [AGPL-3.0 with trademark reservation](https://raw.githubusercontent.com/passbolt/passbolt_api/87281294c234653b9872b58e3bbcbab42493a71d/LICENSE.txt).
- **Adoption test:** Back up database, server keys, and user recovery material; trademark terms and edition capabilities require review.
- **Source:** [passbolt/passbolt_api](https://github.com/passbolt/passbolt_api).

### SOPS

- **Choose it for:** Reviewable configuration in Git and CI.
- **License at the frozen source:** [MPL-2.0](https://raw.githubusercontent.com/getsops/sops/9aa10f12bcd82400b7eb73331dd7a87a93bb17f7/LICENSE).
- **Adoption test:** Availability depends on recipient/KMS recovery; test rotation, removed recipients, CI identity, and emergency decrypt.
- **Source:** [getsops/sops](https://github.com/getsops/sops).

### age

- **Choose it for:** Building simple offline encryption workflows.
- **License at the frozen source:** [BSD-3-Clause](https://raw.githubusercontent.com/FiloSottile/age/b74dce4cdbe35b5e5f66c06d9612b72f89028758/LICENSE).
- **Adoption test:** It supplies encryption, not policy, audit, rotation orchestration, or recovery; design those explicitly.
- **Source:** [FiloSottile/age](https://github.com/FiloSottile/age).

### OpenBao

- **Choose it for:** Services and CI needing runtime identity and leases.
- **License at the frozen source:** [MPL-2.0](https://raw.githubusercontent.com/openbao/openbao/a943844dae65dcdaa031cd739b7545250578693d/LICENSE).
- **Adoption test:** The unseal/recovery root, storage snapshots, plugins, HA, audit devices, and upgrade compatibility require rehearsed operations.
- **Source:** [openbao/openbao](https://github.com/openbao/openbao).

### Infisical

- **Choose it for:** Teams organizing application configuration across environments.
- **License at the frozen source:** [MIT except separately licensed ee directories](https://raw.githubusercontent.com/Infisical/infisical/ee6d61366304fa752d83216002f8bcde83e9955b/LICENSE).
- **Adoption test:** Repository is MIT except ee directories, which require an Infisical license; confirm every needed feature and migration/export path.
- **Source:** [Infisical/infisical](https://github.com/Infisical/infisical).

### Psono

- **Choose it for:** Teams wanting a self-hosted collaborative vault.
- **License at the frozen source:** [Apache-2.0 core; verify enterprise modules separately](https://raw.githubusercontent.com/psono/psono-server/b0b458bfd83d7bd0f2b10be4b5c766df1c2847e5/LICENSE.md).
- **Adoption test:** Verify community/enterprise feature boundary, client compatibility, server keys, database/filestore backup, and account recovery.
- **Source:** [psono/psono-server](https://github.com/psono/psono-server).

### Sealed Secrets

- **Choose it for:** GitOps teams storing cluster-bound ciphertext.
- **License at the frozen source:** [Apache-2.0](https://raw.githubusercontent.com/bitnami/sealed-secrets/53625dfe02d8e807122f08f53fd6d9ee774b131b/LICENSE).
- **Adoption test:** Back up controller keys or ciphertext becomes unrecoverable; use current canonical bitnami repository and avoid experimental raw mode as a baseline.
- **Source:** [bitnami/sealed-secrets](https://github.com/bitnami/sealed-secrets).

### External Secrets Operator

- **Choose it for:** Clusters already using a cloud or central secret store.
- **License at the frozen source:** [Apache-2.0](https://raw.githubusercontent.com/external-secrets/external-secrets/96634cd3c5949acb12dcd7cea7302b28527c5676/LICENSE).
- **Adoption test:** Kubernetes Secrets still contain materialized plaintext; test provider outage, refresh/deletion policy, RBAC, and recovery.
- **Source:** [external-secrets/external-secrets](https://github.com/external-secrets/external-secrets).

### gopass

- **Choose it for:** CLI-oriented teams and offline/air-gapped workflows.
- **License at the frozen source:** [MIT](https://raw.githubusercontent.com/gopasspw/gopass/5f4d4032405055d6b842d3148b805950a97b2da6/LICENSE).
- **Adoption test:** The similarly named distro gopass package can be unrelated; test key onboarding/offboarding, Git history, and recovery.
- **Source:** [gopasspw/gopass](https://github.com/gopasspw/gopass).

### Conjur

- **Choose it for:** Infrastructure needing application identity and policy.
- **License at the frozen source:** [LGPL-3.0 server; Apache-2.0 clients/extensions; commercial licenses also offered](https://raw.githubusercontent.com/cyberark/conjur/bfc444df74b7588d23b0ab8d4070e0b12e47ce80/LICENSE.md).
- **Adoption test:** Use tested suite releases and scope database initialization, HA, backup, and migration to CyberArk offerings; server and clients use different licenses.
- **Source:** [cyberark/conjur](https://github.com/cyberark/conjur).

## Rotate and recover a canary

1. Mark every plaintext hop, identity bootstrap and root key holder.
2. Rotate one canary secret and revoke one consumer identity.
3. Disable the external provider/controller and observe cached-secret behavior.
4. Restore keys and encrypted state independently; verify consumer permissions.

## What this guide covers

Operational secret delivery and trust roots, not an AI-agent policy evaluator or a universal IAM solution.

## Adjacent guides

- [self-hosted-git-forge-matrix](https://github.com/Rubio199321/self-hosted-git-forge-matrix) — Use it when the consumers are a self-hosted forge, CI workers, package registry, or deployment system.
- [self-hosted-container-management-map](https://github.com/Hervieux39AC/self-hosted-container-management-map) — Use it when secrets must reach containerized workloads through a recoverable control plane.
- [encrypted-backup-tool-selector](https://github.com/Anguianodd/encrypted-backup-tool-selector) — Use it when secret-store data and recovery keys require a separate encrypted backup plan.

## Located sources and update policy

[`sources.json`](sources.json) binds each cited declaration to a commit, document, exact line range and local snapshot hash. [`assets/projects.json`](assets/projects.json) holds the normalized roles and decision axes. Current HEAD reachability is recorded separately; it is not a maintenance or security assessment. Licensing is version- and directory-specific. Re-read the selected release before adoption.

Run the local evidence and package checks, then opt into public-source reachability:

```bash
python3 scripts/verify_sources.py
python3 scripts/verify_sources.py --online
```

These checks validate source binding, excerpts, hashes, language, the decision asset and nonempty public content. They do not certify feature behavior, migration success or restore success. No popularity score, paid placement or affiliate ranking is used. Restricted-license and historical examples are explicit contrasts, not silently recommended open-source defaults.

If this saved you a decision, star the repo — it is how the next reader finds it.
