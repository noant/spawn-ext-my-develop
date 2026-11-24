# UI Design

Guidance for distinctive, intentional visual design when building new UI or reshaping an existing one - components, pages, whole interfaces: aesthetic direction, typography, accessibility, design tokens, avoiding templated AI defaults.

## Ground your designs in the subject matter

Understand the context before making aesthetic choices. What is this product for? Who uses it? What emotional register fits - serious, playful, luxurious, technical? A fintech dashboard, a children's game, and a portfolio site should never look interchangeable. Pull specific vocabulary, motifs, and constraints from the domain.

## Design principles

- Commit to a direction. One clear idea executed fully beats several half-applied ones. Pick an aesthetic concept (e.g., "editorial minimalism", "brutalist utilitarian", "soft tactile") and let every choice serve it.
- Typography is the design. Choose distinctive, purposeful fonts - not just Inter/Roboto. Establish a clear scale and hierarchy; tune line-height, letter-spacing, and measure deliberately. Default to line lengths under 80 characters.
- Color with intent. Avoid default Tailwind/every-library palettes. Build a small, disciplined palette with one dominant color, restrained accents, and strong contrast discipline.
- Space and motion are material. Use whitespace structurally; make motion purposeful, quick, and interruptible - never decorative for its own sake. Non-user-triggered motion: a single orchestrated moment lands better than scattered effects.
- Visual structure is information. Outlines, borders, numbering, dividers, labels must encode meaning, not decorate. Numbered markers (01/02/03) only when content is actually a sequence.
- Details separate good from great: consistent borders, states, empty/loading/error designs, thoughtful microcopy.

## Process

Work in two passes:

1. Plan - briefly state the aesthetic direction: typography, palette (4-6 named hex values), layout concept (ASCII wireframes help), and the "signature" element. Review the plan against the brief: does it serve the actual user and purpose, or is it generic? Revise anything that reads as a default.
2. Build - implement fully with real copy, real data shapes, and all states designed. Mind CSS specificity so classes don't cancel each other out (especially padding/margin between sections).

Critique your own work: actively hunt for the AI-visual-defaults:

1. Generic Inter/Roboto on a white/near-white background with a single purple/blue accent.
2. Warm cream background (near #F4F1EA) with high-contrast serif display and terracotta accent (near #D97757).
3. Near-black background with a single acid-green or vermilion accent.
4. The SaaS-card kit: identical rounded cards, one border-radius everywhere, same soft grey shadow under each, gradient washes as decoration.
5. Template chrome regardless of subject: tracked-out ALL-CAPS eyebrow labels, meta strings joined with middle dots, tinted near-black standing in for black, monospace data labels, arrow appended to links.
6. Centered hero + 3-column feature cards + rounded gradient buttons.
7. Placeholder copy and lorem ipsum standing in for designed content.

The brief's own words always win - if the user explicitly requests one of these looks, follow them. Where the brief leaves an axis free, don't spend that freedom on a default.

## Restraint and self-critique

- Fewer, bolder choices. Spend boldness in one place: one memorable element beats ten subtle ones.
- Remove anything that does not serve hierarchy, comprehension, or the core concept.
- Build to a quality floor without announcing it: responsive down to mobile, visible keyboard focus, reduced motion respected, visually accessible.
- Ask: "Would a senior designer ship this, or does it read as generated?" Take screenshots to review if your environment supports it. Revise until it reads as authored.

## More on writing in design

Copy is part of the design. Write real, specific microcopy - button labels, empty states, errors, onboarding - in the product's voice. Never ship lorem ipsum or vague placeholders.

- Write from the end user's perspective: a user manages notifications, not webhook config.
- Active voice; CTAs say what happens: "Save changes", not "Submit". Keep the same action name through the whole flow ("Publish" produces "Published").
- Errors explain what went wrong and how to fix it; they don't apologize and are never vague. An empty screen is an invitation to act.
- Plain verbs, sentence case, no filler; each written element does exactly one job.

## Accessibility and UX quality bar

- Contrast: text meets WCAG AA - 4.5:1 normal text, 3:1 large text (18px+ / 14px bold); 3:1 for UI icons and borders.
- Touch targets: minimum 44x44px (24px absolute floor for dense desktop UI); spacing between targets >= 8px.
- Focus states: every interactive element has a visible focus indicator (outline >= 2px, offset 2px); never `outline: none` without a replacement.
- Keyboard navigation: full tab flow in logical order; no keyboard traps; custom widgets implement ARIA patterns.
- Semantic HTML first: button for actions, anchor for navigation, label tied to every input, landmarks for page regions.
- Forms: errors adjacent to the field, tied via `aria-describedby`, with `aria-invalid`; never color-only error signaling.
- Reduced motion: respect `prefers-reduced-motion` - disable parallax, large transitions, autoplaying animation.
- Hover-only affordances are banned: every state reachable and discoverable on touch and keyboard.
- Do not disable zoom; base font size >= 16px; line length 45-75 characters for reading text.
- Test the design as: keyboard-only user, screen-reader user, low-vision user.

## Design tokens and systems

- Spacing scale: 4px base (4 / 8 / 12 / 16 / 24 / 32 / 48 / 64). No arbitrary values; pick from the scale.
- Radii hierarchy: one small (4-6px inputs), one medium (8-12px cards), one large (16px+ modals); full-round only for pills/avatars. Keep it consistent.
- Elevation system: 2-3 defined shadow levels (rest, raised, overlay) with matching z-index tokens; do not invent shadows ad hoc.
- Color tokens, not raw hex in components: `surface` (backgrounds), `base` (text/borders), `interactive` (links, buttons, focus), `accent` (highlights, CTA). Dark mode derives from the same tokens.
- Interactive states are part of the component: define hover, active/pressed, focus-visible, disabled, and loading for every interactive element. Disabled is not just opacity 0.5 - keep contrast legible and communicate why.
