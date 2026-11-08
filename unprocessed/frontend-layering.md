---
type: topic
updated: 2026-10-25
---

# Frontend layering: kit → patterns → views

Research done for AgentOS V2's web rebuild (2026-10-25). The idea is three layers: a `kit` of
primitives, the only place with raw elements and CSS; `patterns`, recurring composites built from
kit parts; and `views`, the pages. This page shows how real apps and the known methods do it. All
links were verified on 2026-10-25.

## Real apps to study (ranked by how closely they match)

1. **Supabase**: names all three layers.
   - Layers: `packages/ui` (shadcn on Radix) → `packages/ui-patterns` ("fragments": PageHeader,
     FilterBar, MetricCard) → `apps/studio`.
   - Read:
     [ui-patterns README](https://github.com/supabase/supabase/blob/master/packages/ui-patterns/README.md),
     [studio components README](https://github.com/supabase/supabase/blob/master/apps/studio/components/README.md),
     [design system: fragments](https://supabase.com/design-system/docs/fragments/introduction).
   - Copy: no barrel file; each composite is imported by path.
   - Avoid: two kit generations side by side.
2. **Twenty** (CRM, NestJS backend): Base UI underneath.
   - Layers: `twenty-ui/src/primitives` → `twenty-ui/src/components` (FormFieldRow, MenuItem) →
     `twenty-front/src/modules/ui` → feature modules.
   - Lint rule
     [`no-twenty-ui-override`](https://github.com/twentyhq/twenty/blob/main/packages/twenty-oxlint-rules/rules/no-twenty-ui-override.ts):
     no restyling the kit from outside (no className/style overrides).
   - Avoid: four tiers, one more than needed.
3. **Sentry** (Django + React): the strongest "use the kit" enforcement.
   - Lint
     [`prefer-primitives`](https://github.com/getsentry/sentry/blob/master/static/oxlint/eslintPluginScraps/src/rules/preferPrimitives.ts)
     flags styled divs and spans and points to Container or Text. Also `use-semantic-token` and
     `no-manual-table`.
   - Has layout primitives in
     [`core/layout`](https://github.com/getsentry/sentry/tree/master/static/app/components/core/layout).
   - Avoid: no named patterns layer, so composites sprawl.
4. **PostHog Quill**: names the model exactly ("tokens > primitives > components > blocks") in
   [`packages/quill/AGENTS.md`](https://github.com/PostHog/posthog/blob/master/packages/quill/AGENTS.md).
   The recipe is Base UI + cva + `data-slot`. It's new and its blocks layer is empty. The live app
   uses `lemon-ui` → `lib/components` → `scenes`, with `react/forbid-elements` in oxlint.
5. **Plane**: `@makeplane/propel` (Base UI) →
   [`packages/blocks`](https://github.com/makeplane/plane/tree/preview/packages/blocks/src)
   ("composite components built on propel primitives") → `apps/web`.
6. **Documenso**: the easiest to read end to end:
   [`packages/ui/primitives`](https://github.com/documenso/documenso/tree/main/packages/ui/primitives)
   → `packages/ui/components` → `apps/remix`. Convention only, and some app code has leaked into
   primitives.
7. **Backstage**: `packages/ui` (React Aria, plain CSS) → `core-components` (Page, Header, Content,
   InfoCard) → plugins. Has its own eslint plugin.
8. **Grafana**: `grafana-ui` → `public/app/core/components` → features. Its lint rules are worth
   copying (`theme-token-usage`, `no-border-radius-literal`). Its flat 100+ component kit is a
   warning.
9. Others checked: Cal.com (a written guide to swapping the headless layer, Radix → Base UI), GitLab
   gitlab-ui and Pajamas (Vue), n8n (Vue; a flat kit, cautionary).

**Trends:**

- Lint, not folder names, is what holds the boundary.
- Base UI is the trend for new kits (Twenty, Quill, Plane, Cal.com).
- A layout-primitive set (Container, Flex, Stack, Text) is what makes raw divs unnecessary above the
  kit.

## Methods

- **Atomic Design** (Brad Frost, [ch. 2](https://atomicdesign.bradfrost.com/chapter-2/)): atoms →
  molecules → organisms → templates → pages. The well-known problem is arguing over molecule versus
  organism. Kit/patterns/views folds those two together, which removes the argument.
- **Feature-Sliced Design** ([layers](https://feature-sliced.design/docs/reference/layers)):
  `shared/ui` → entities → features → widgets → pages, strict downward imports. The
  [Steiger](https://github.com/feature-sliced/steiger) linter's `insignificant-slice` rule flags
  slices with only 0–1 users. Heavier than needed.
- **Bulletproof React**
  ([structure](https://github.com/alan2207/bulletproof-react/blob/master/docs/project-structure.md)):
  `components` → `features/*` → `app/routes`, enforced with `import/no-restricted-paths`. The
  simplest config to copy.
- **shadcn/ui**: primitives copied into your repo, then Blocks. Base UI has been its default since
  07/2026. This is "own the look, rent the behaviour".
- **Design systems:**
  - Atlassian primitives (Box, Stack, Inline, Text, token-only styling plus a lint rule) are the
    cleanest kit model.
  - Primer publishes UI patterns.
  - Chakra's recipes and slot recipes declare variants in one place, not as stacks of props.

## Enforcement tools

- **Layer imports:**
  [eslint-plugin-boundaries](https://github.com/javierbrea/eslint-plugin-boundaries)
  (`boundaries/dependencies`, default-deny); dependency-cruiser in CI.
- **No raw elements outside the kit:**
  [`react/forbid-elements`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/forbid-elements.md)
  scoped to patterns and views; `forbid-dom-props` and `forbid-component-props` (no style or
  className outside the kit).
- **Tokens:**
  [stylelint-declaration-strict-value](https://github.com/AndyOGo/stylelint-declaration-strict-value).
- **Reuse:** [react-scanner](https://github.com/moroshko/react-scanner) counts each component and
  prop. Fail CI on exports with zero uses, and demote anything with one use.

## Keeping the kit small

- Rule of three ([Dan Mall](https://v5.danmall.com/posts/three-times-is-a-pattern/)); "duplication
  is cheaper than the wrong abstraction"
  ([Sandi Metz](https://sandimetz.com/blog/2016/1/20/the-wrong-abstraction)).
- Prop caps (about 8–10) and a two-screens rule are opinion, not established practice. Use slots,
  compound parts or variant recipes instead of adding props.
