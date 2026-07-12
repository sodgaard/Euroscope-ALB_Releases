# Retired: Scenarios

The `Scenarios` menu is retained for compatibility and history, but it is not part of the recommended modern operating method.

Recent ALB builds also hide the visible `Scenarios` menu from the normal
control bar, even though the legacy scenario config and apply logic still
exist internally.

## What scenarios were

Scenarios are predefined sets of via-fix arrival-rate values attached to a timeline.

Historically, they gave the FMR a quick way to apply a coarse flow regime across several streams at once.

## Why they are retired

Current ALB use is centered on `EAT:LT` and landing-timeline monitoring.

- the preferred method is to watch the landing plan and correct aircraft that do not follow it
- scenario switching is no longer the normal control method
- if AR values are being used at all, they are usually part of a rougher fallback workflow rather than the main planning model

## If you still see the menu

- treat it as legacy UI
- use it only if your local operation deliberately wants that older workflow
- do not read it as the recommended normal way to operate ALB today

## Related pages

- [Quick Intro](../quick-intro.md)
- [Buttons & Menus](../buttons-and-menus.md)
- [Workflows](../workflows.md)
