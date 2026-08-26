# Context — slash-builder/slash-builder.github.io
# SlashBuilder OSS-org site

**What it is:** Public website for SlashBuilder, Lockamy Studios' open-source
organization, live at slashbuilder.com. Stewards Hearth (the protocol),
BenixOS (the OS), and bitchain (the content-addressed storage kit) as peer
projects — see the site content and
`dlockamy/context/cross-repo-context.md`'s Corporate structure section for
the full, locked studio-wide record. bitchain joined the day after this site
shipped, when `clusterzer0` was dissolved (2026-08-23) and the storage stack
transferred into `slash-builder/`; see `dlockamy/context/projects/slash-builder.md`.

**Stack:** Jekyll, `minima` theme (bare — matches the current state of
sibling project sites `benixos.github.io` and `thunderhead-systems.github.io`;
Digital Zen theming is a deliberate next step, not yet applied).

**CI:** Jenkinsfile → Jekyll build → archive `_site/`. GitHub Pages can also
build natively from `main` if Jenkins isn't wired up yet for this org.

**Naming note:** the GitHub org is `slash-builder` (hyphenated) — the
unhyphenated `slashbuilder` GitHub username is a dormant, unrelated
third-party personal account, not available. The brand stays "SlashBuilder"
everywhere in copy; only the org/repo slug carries the hyphen.

**Domain:** `slashbuilder.com`, via `CNAME`.

**Next steps:**
- Apply Digital Zen theme (see `lockamy-studios/digital-zen` for tokens)
  once the studio decides this site should carry the full design system
  rather than a bare-minima stub.
- Wire real Jenkins CI once a `slash-builder` org folder exists on the
  controller (mirrors the pattern already used by `benixos`,
  `thunderhead-systems`).
- Cross-check `benixos.github.io` and `thunderhead-systems.github.io` for
  staleness against the 2026-08-22 SlashBuilder/BenixOS/Thunderhead
  corporate-structure correction — both currently predate it and describe
  an earlier framing (Thunderhead as a hardware product, BenixOS branded
  independently as "Personal Cloud Operating System"). Not fixed here;
  flagged for whoever next touches either repo.
