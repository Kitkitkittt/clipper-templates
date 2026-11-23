# Swarm-ready handoff

Repository: `Kitkitkittt/clipper-templates`. Contract: `swarm-ready/v1`. Source inspection: 2026-11-23.
Default branch: `main`. Inspected base: [dffb2d7bede9](https://github.com/Kitkitkittt/clipper-templates/commit/dffb2d7bede9049cd9ccb9089f6373cc34b21e23).

**Status: documented; runtime qualification is unverified.** This file defines an agent workflow. It does not install a shell command, enroll the repository in a scheduler, certify test success, or activate VMs. Follow the current user task and existing repository instructions; retain prior authorization within its actual scope.

## Invoke

Give the working agent this prompt:

```text
swarm ready: <one measurable repository objective>
Read SWARM_READY.md and the applicable AGENTS.md. Continue the current owned
work or ready issue, establish the baseline and acceptance check, then prepare
a bounded worker handoff. Use existing authorized tools and capacity. Report
what is documented, implemented, tested, and observed separately.
```

If a `swarm-ready` skill is installed, load it. Otherwise execute the documented workflow directly. Treat the phrase as an agent instruction unless the installed CLI explicitly advertises it. Inspect help/version before using optional command adapters.

## Repository starting point

Read: [README.md](README.md).

Continue an existing approved issue or a clearly scoped user objective after inspecting the source map below. If none is selected, return the smallest useful improvement and its verifier as the handoff; do not begin a portfolio-wide rewrite.

Start with one writer. Add workers only for disjoint changes or independent experiments whose results can be checked separately. Keep shared interfaces and final integration with one owner.

The base above is an inspection snapshot, not a permanent working branch. Refresh the actual target branch, current issues, incumbent worker and applicable nested instructions before editing. Preserve unrelated changes and upstream/fork boundaries.

## Execution logic

1. **Recover context.** Read the current issue, work order and last evidence-backed handoff. Keep useful work and the active worker. Record source commit, unresolved criteria and the next action; avoid repeating completed investigation.
2. **Define success.** State one outcome, baseline, target, changed paths, verification command and expected result. Keep acceptance criteria and reference data fixed unless the user changes the requirement.
3. **Bound the attempt.** Reuse the existing owner/task/attempt identities, action scope, resource limits, time/cost budget and retry allowance. Do not demand a new user-written permission receipt for already authorized work. If a real capability or grant is missing, name the exact blocked action and continue independent work.
4. **Partition only useful work.** Use one writer per branch/worktree and non-overlapping change scope. Reuse the existing task ledger and dependency graph. Reserve verification capacity before adding workers; avoid recursive worker growth and duplicate coordinators.
5. **Implement and measure.** Make the smallest coherent change. Use differential checks when a trusted reference exists, and deterministic failure simulation when it materially improves reproduction. Record the seed/configuration and counterexamples. Do not weaken tests, rewrite baselines or hide pre-existing failures to claim success.
6. **Verify and integrate.** Verify the exact returned commit independently in a clean environment. Recheck the final integrated revision when changes interact. Follow existing review, merge and deployment rules; a green worker report alone cannot override them. Use the narrowest checks that resolve a concrete risk.
7. **Checkpoint and finish.** Save candidate SHA, evidence, measured outcome, remaining blockers and a precise next action before context/lease expiry. Reconcile lost or duplicate completions before retrying. Release only the run's owned resources after preserving evidence. Stop when the requested outcome is met or a concrete blocker prevents the next required action.

## Verification starting point

From the repository root, run this Python 3 check of `templates/*-clipper.json` and the `Templates` list in `README.md`. It checks the shared `0.1.0` export fields, property names and catalog links. It accepts empty note content and property values, and allows exports that are not listed in the README.

```sh
python3 -IBS - <<'PY'
import json
import re
from pathlib import Path


def nonblank(value):
    return isinstance(value, str) and bool(value.strip())


def unique_keys(pairs):
    obj = {}
    for key, value in pairs:
        if key in obj:
            raise ValueError(f"duplicate JSON key {key!r}")
        obj[key] = value
    return obj


directory = Path("templates")
if directory.is_symlink() or not directory.is_dir():
    raise SystemExit("Expected a local templates directory")
exports = sorted(directory.glob("*-clipper.json"))
if not exports:
    raise SystemExit("No Clipper exports found in templates/")

for path in exports:
    if path.is_symlink() or not path.is_file() or path.stat().st_size > 64 * 1024:
        raise SystemExit(f"{path}: expected a regular JSON file of at most 64 KiB")
    try:
        data = json.loads(path.read_text(encoding="utf-8"), object_pairs_hook=unique_keys)
    except ValueError as exc:
        raise SystemExit(f"{path}: {exc}")
    if not isinstance(data, dict) or data.get("schemaVersion") != "0.1.0":
        raise SystemExit(f"{path}: expected a Clipper 0.1.0 export object")
    for field in ("name", "behavior", "noteNameFormat", "path", "noteContentFormat"):
        value = data.get(field)
        if not isinstance(value, str) or (field not in {"path", "noteContentFormat"} and not value.strip()):
            raise SystemExit(f"{path}: invalid or missing string field {field}")
    triggers = data.get("triggers")
    if not isinstance(triggers, list) or not all(map(nonblank, triggers)):
        raise SystemExit(f"{path}: expected triggers to be an array of nonblank strings")
    properties = data.get("properties")
    if not isinstance(properties, list):
        raise SystemExit(f"{path}: expected a properties array")
    names = set()
    for index, prop in enumerate(properties, 1):
        if not isinstance(prop, dict) or not all(nonblank(prop.get(key)) for key in ("name", "type")) or not isinstance(prop.get("value"), str):
            raise SystemExit(f"{path}: property {index} needs a name, type and string value")
        name = prop["name"].strip()
        if name in names:
            raise SystemExit(f"{path}: repeated property name {name!r}")
        names.add(name)

lines = Path("README.md").read_text(encoding="utf-8").splitlines()
try:
    start = lines.index("## Templates") + 1
except ValueError:
    raise SystemExit("README.md: missing Templates section")
filenames = {path.name for path in exports}
listed = set()
for number, row in enumerate(lines[start:], start + 1):
    if row.startswith("#"):
        break
    if not row.startswith("- "):
        continue
    link = re.match(r"- \[[^\]]+\]\(/?templates/([\w.-]+-clipper\.json)\)(?=\s|$)", row)
    if not link or link[1] not in filenames or link[1] in listed:
        raise SystemExit(f"README.md:{number}: expected a unique link to a local Clipper export")
    listed.add(link[1])
if not listed:
    raise SystemExit("README.md: no templates listed")
print(f"Checked {len(exports)} exports and {len(listed)} README template links.")
PY
```

Expect exit 0 and counts for exports and README links. This is a local format check, not proof that an export imports or renders correctly. Treat URL, schema and regex triggers and template expressions as inert strings: do not visit sites, install or launch Obsidian or a browser extension, evaluate expressions, or write to a vault.

## Required handoff result

Return a concise record with `repository`, `objective`, `issue_or_work_order`, `base_sha`, `candidate_sha`, `owner`, `task_id`, `attempt_id`, `worker/session`, `branch/worktree`, `changed_paths`, `environment`, `resource/budget limits`, `acceptance`, `commands_and_results`, `evidence_refs`, `known_failures`, `blocker_owner`, `next_action`, and `cleanup_status`. Use null/unknown when a field is unavailable; never manufacture evidence or authority.

Keep documentation readiness, command availability, source tests, live environment qualification and accepted outcome as separate states. Count verified outcomes, regressions, elapsed time, cost and human interventions. Commits, lines, tokens and worker count describe activity only.
