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

## Conceptual Model To Preserve

Keep these distinctions explicit and consistent in the public docs:

- **Feeder** versus **Runway** is a view and operational-role distinction
- it reflects the picture the controller wants, such as upstream or area
  feeding context versus TMA or runway-side context
- it is independent of `EAT:AR` versus `EAT:LT`
- `EAT:AR` versus `EAT:LT` is an engine or planning-mode distinction
- `EAT:LT` is the preferred modern method in normal operation
- `EAT:AR` is the older rough fallback method

Do not describe feeder view as "the AR view" or runway view as "the LT view".
If a page discusses both concepts, explain them separately.

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
- when documenting a feature, always also document how a user actually makes
  ALB act that way:
  - UI button or menu
  - mouse interaction
  - `.alb` command
  - config edit plus reload or restart
- do not leave a feature explained only in passive or descriptive terms if
  there is a real operator or config control surface that drives it
- do not change generated `site/` content unless the task explicitly requires a
  site rebuild

If a documentation claim conflicts with the source repo, trust the source repo
and update this repo accordingly.
