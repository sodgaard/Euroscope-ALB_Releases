# ALB Releases / Docs Briefing for Codex

This repository is the public-facing **release and documentation** repository
for ALB. Treat it differently from the source/development repository.

## Repository Role

This repo owns:

- public release metadata such as `alb-plugin-release-version.txt`
- release-facing documentation under `docs/`
- the published MkDocs site configuration and generated site content

This repo does **not** own the ALB implementation source code.

## Relationship To The Code Repository

The ALB implementation lives in the separate source repository:

- `C:\Users\sso99\source\repos\ES-ArrivalLoadBallance`

That source repo contains:

- the EuroScope loader/runtime code
- technical architecture docs
- current implementation behavior
- the authoritative packaging/update rules for `ALB.dll`, `ALBCore.dll`,
  `ALBLoader.ini`, and config handling

This release/docs repo lives at:

- `C:\Users\sso99\source\repos\Euroscope-ALB_Releases`

When updating documentation here:

- verify behavior against the source repo before changing user-facing text
- keep install/package/release wording aligned with the current loader/core
  architecture in the source repo
- treat this repo as the public explanation layer for what the source repo
  currently does

## Documentation Audience

The documentation site is primarily for:

- end users installing ALB
- controllers using ALB operationally
- release consumers looking for download/install guidance

Prefer operational, installation, and workflow guidance over implementation
detail.

## Documentation Boundaries

Do not mention or document **debugging** behavior on the public documentation
site unless the user explicitly asks for it and the page is clearly intended as
developer-only material.

Do not mention or document **experimental** behavior on the public
documentation site unless the user explicitly requests it for a dedicated
developer/internal page.

For the normal public docs in `docs/`, assume:

- debugging commands/features should stay out
- experimental flags/features should stay out
- user-visible stable workflows should be emphasized

If such behavior appears in source-repo docs or code comments, do not copy it
into the public docs by default.

## Packaging Facts To Preserve

Keep the public docs aligned with these current packaging rules unless the
source repo changes them:

- EuroScope should load `ALB.dll`
- `ALB.dll` loads `ALBCore.dll`
- the loader may update `ALBCore.dll`
- the loader may refresh `alb-config.default.json`
- the loader must not overwrite live `alb-config.json`

## Editing Guidance

When editing this repo:

- prefer updating `docs/` and `README.md`
- keep wording concise and user-facing
- avoid copying deep technical design notes from the source repo unless needed
  to explain install, release, or operational behavior
- do not change generated `site/` content unless the task explicitly requires a
  site rebuild

If a documentation claim conflicts with the source repo, trust the source repo
and update this repo accordingly.
