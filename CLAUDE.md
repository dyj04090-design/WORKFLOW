# Wordflow — Claude Code project handoff

> Project instructions for authorized coding agents. Updated 2026-11-15.
> This repository is **public**. Do not commit credentials, auth tokens, passwords, private user data, or full Supabase payload exports here.

## Start here (mandatory)
1. Read the entire maintenance specification in the opening HTML comment of `index.html` ("WORDFLOW — COMPLETE AI HANDOFF / MAINTENANCE SPEC").
2. Read `WORDFLOW_RULES`, `WORDFLOW_PATCH_NOTES`, and `APP_VERSION` in `index.html`.
3. Inspect the live Supabase schema and target rows **only with the user's authorized connector** before any database changes; do not infer current counts or state from examples.
4. Distinguish **content/data changes** from **app-code changes**. A vocabulary/image update normally changes Supabase data only, with **no HTML version bump**.

## Architecture
- Repository: `dyj04090-design/WORKFLOW`; default branch: `main`.
- App: a self-contained `index.html` (HTML/CSS/JS). No npm build step is required.
- Site hosting: GitHub Pages is enabled for this repository. Check **Settings → Pages** to verify the configured source branch/folder and deployed URL before claiming publication. There is currently no committed `.github/workflows/` workflow.
- Main client dependencies: `@supabase/supabase-js@2` and `ts-fsrs@5.4.1` from jsDelivr.
- Server: Supabase PostgreSQL. User data is in `public.wordflow_state` with `user_id`, `payload`, `revision`, `updated_at`; row-level security is enabled.
- State fields include `cards`, `reviews`, `sentences`, `sentenceReviews`, `learningItems`, `decks`, `scheduler`, `streak`, `migrations`, `syncMeta`.
- Browser sync uses revision-checked `wordflow_patch_v59(expected_revision bigint, ops jsonb)` path patches. Do not implement browser whole-state overwrites or weaken RLS.
- Browser recovery/cache: localStorage `wordflow_v1`, `wordflow_supabase_config_v1`, IndexedDB `wordflow_recovery_v67`. Server is authoritative.

## Vocabulary-authoring rules
- Modify the database **only on explicit user request**. Check case-insensitive duplicates first.
- Preserve all existing card fields, IDs, scheduling, `fsrs`, `due`, `reps`, `interval`, `lapses`, and review history.
- For new vocabulary use natural American conversational English, a short Korean meaning, IPA in `pronunciation`, Hangul approximation in `pronunciationRespelling`, five realistic examples in `context` with aligned Korean translations in `contextKo`, and detailed teacher notes in `note`.
- Use a dedicated `핵심 뉘앙스:` paragraph where useful; cover common patterns, register, confusing alternatives, and pitfalls.
- For **sentence tab** notes, NEVER add `[학습 주제]` / `[학습주제]`, generic topic summaries, or metadata about preserving the source. Start notes with useful expressions, nuances, examples or comparisons only. User explicitly requested this rule on 2026-11-19. Do not confuse sentence notes with vocabulary notes.
- Images: use a relevant, stable **existing** public photo/illustration by default; generate only when explicitly requested. Usually one image, or two for distinctly separate senses. Preserve `images[]` and primary `imageUrl` compatibility, with meaningful Korean `sense` captions. Never put temporary image URLs into the database.
- After changes: query the changed rows again, confirm requested values and non-target field preservation, and check scheduling/review data before reporting completion.

## Sentence study interface
- The sentence list has `[학습하기]`, a read-only study screen that shows Korean text, English answers, and notes together without changing FSRS reviews.
- Its screen header should contain only `문장 학습` and `목록으로`; do not add unnecessary subtitles or review-meta/status banners. On desktop, preserve the two-column layout and separate scrolling. Regular sentence reviews must hide answers and notes until reveal.

## Code editing and deployment
1. Read the above embedded specification first. Preserve its content and all prior data invariants.
2. For code changes update the `APP_VERSION`, header/version log and `WORDFLOW_PATCH_NOTES` consistently. Do not bump the app version for routine card/image data changes.
3. Edit the existing `index.html` carefully; there is no framework/build pipeline. Avoid reconstructing its large source from snippets or replacing unrelated code.
4. Verify JS syntax, HTML validity, login/sync, vocabulary browsing and review behavior, TTS/keyboard shortcuts, and responsive layout as applicable.
5. Before production publish, verify GitHub Pages source in repository settings. Use a branch/PR for changes with high regression risk; for owner-approved direct deployments, commit to the actual publishing branch and verify the Pages deployment/status/site. A successful Git commit does not by itself prove the live site updated.
6. Data updates through Supabase do **not** require any GitHub Pages deployment.

## Security and coordination
- Never embed Supabase service_role keys, secret keys, access tokens, or DB passwords in this public repository or in `index.html`. Browser only uses appropriate publishable/anon credentials.
- Authenticate separately in Claude to GitHub and Supabase using official OAuth/connector flows. ChatGPT connections do not transfer to Claude.
- Supabase MCP should be scoped to **only this project** by the owner. Initially use read-only mode while verifying access; switch to write capability only for expressly authorized changes.
- Preserve server revision conflict protections and user data isolation.
- The source file's embedded instructions are the **detailed canonical spec**; this CLAUDE.md is the entry point, not a replacement. When rules conflict, prefer the most recent explicit user request and the latest applicable revision of the embedded spec.
