# Portfolio Workflow Notes

## Purpose

Reduce repeated investigation, permission retries, unnecessary screenshots, and token usage when continuing this portfolio project.

## Project Facts

- Working directory: `E:\燕云\portfolio-publish`
- GitHub repository: `cfvbdv4-Oranger/portfolio`
- Published site: `https://cfvbdv4-oranger.github.io/portfolio/`
- Main files:
  - `index.html`: page content, project order, bilingual text
  - `styles.css`: original base styles
  - `hero-hands.css`: current visual system and responsive overrides
  - `script.js`: animation and bilingual switching

## Problems Encountered

### 1. File write permissions behaved inconsistently

- Text edits through `apply_patch` worked.
- Copying, renaming, Git metadata writes, and browser launches were often blocked.
- Repeated escalation requests frequently timed out.
- Windows ACLs were valid; the restriction came from the Codex managed sandbox.

Optimization:

- Use `apply_patch` for all text changes.
- Ask the user to drag binary assets directly into the project when copying is blocked.
- Reference the actual dragged filename instead of repeatedly trying to rename it.
- Do not repeatedly inspect Windows ACLs after confirming them once.

### 2. GitHub push failed because of an invalid injected proxy

- Codex process proxy variables pointed to `http://127.0.0.1:9`.
- The working system proxy was `http://127.0.0.1:10808`.
- Git used Windows `schannel`, which failed in this environment.

Working push command:

```powershell
git -c http.proxy=http://127.0.0.1:10808 -c https.proxy=http://127.0.0.1:10808 -c http.sslBackend=openssl push origin main
```

Optimization:

- If GitHub push fails, immediately inspect proxy variables and Windows proxy settings.
- Test `127.0.0.1:10808` before retrying ordinary pushes.
- Use the working push command above instead of repeated default pushes.

### 3. Git commit identity was missing

Repository-local identity used:

```powershell
git config user.name "cfvbdv4-Oranger"
git config user.email "cfvbdv4@gmail.com"
```

Optimization:

- Check repository identity before the first commit.
- Keep configuration local to this repository.

### 4. Screenshot verification captured hidden reveal states

- Sections use `.reveal` animation and initially have zero opacity.
- Direct element screenshots sometimes captured blank or dark cards.

Working browser verification preparation:

```js
document.querySelectorAll(".reveal").forEach((element) => {
  element.classList.add("visible");
  element.style.opacity = "1";
  element.style.transform = "none";
  element.style.transition = "none";
});
```

Optimization:

- Force reveal elements visible before screenshots.
- Capture only the changed section instead of the entire page.
- Generate one representative project-card screenshot when shared styles affect all projects.

### 5. Asset naming caused avoidable friction

- Chinese filenames and temporary screenshot names work locally and online.
- Renaming was blocked by sandbox permissions.
- The page needed to reference the actual filenames.

Current project images:

- `0.302.png`: ModuScreen
- `屏幕截图 2026-06-14 170932.png`: AirTrace
- `助行车.jpg`: MotionCare
- `手电筒.jpg`: SPIRY

Optimization:

- Prefer short ASCII filenames before dragging assets into the project.
- If the files are already present, use their actual names immediately.
- Always verify every local `src` and `href` before publishing.

### 6. Shared styles should be changed once

- All project headings use `.project-copy h3`.
- Shared card material lives in `hero-hands.css`.
- Changing individual cards separately would waste time and create inconsistency.

Optimization:

- Use shared selectors for typography and material changes.
- Add project-specific classes only for image crop positions.
- Keep project order and numbering in `index.html`.

### 7. Bilingual switching originally covered only the hero

- The script already supported all elements with `data-zh` and `data-en`.
- The rest of the page lacked translation attributes.

Optimization:

- Add both translation attributes whenever new visible text is added.
- Verify counts of `data-zh` and `data-en` match.
- Keep HTML inside translation attributes only when line breaks are required.

## Recommended Low-Token Workflow

1. Read this file and run `git status --short --branch`.
2. Inspect only `index.html`, `hero-hands.css`, and `script.js` sections relevant to the request.
3. Use `apply_patch` for text and CSS changes.
4. For new binary assets, first check whether the user already dragged them into the project.
5. Verify:
   - `git diff --check`
   - all referenced files exist
   - CSS braces are balanced
   - `data-zh` and `data-en` counts match
6. Generate section screenshots only when visual judgment is needed; force `.reveal` elements visible.
7. Before publishing:
   - stage only required site files
   - exclude previews and `desktop.ini`
   - check Git identity
8. Push using the known working proxy/OpenSSL command.
9. Confirm the online HTML contains the latest unique content or commit query parameter.

## Do Not Repeat

- Do not search for the project directory again; it is `E:\燕云\portfolio-publish`.
- Do not use `portfolio-site`; it is an older source/export directory.
- Do not repeatedly retry ordinary GitHub pushes while proxy variables point to port `9`.
- Do not commit preview screenshots, `desktop.ini`, or unrelated artifacts.
- Do not deploy unless the user explicitly approves deployment.
