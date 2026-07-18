# skills

A personal collection of [agent skills](https://docs.claude.com/en/docs/claude-code/skills) for Claude Code — the ones I actually reach for. A skill is a folder with a `SKILL.md` file that packages a repeatable workflow (a review process, an interview discipline, a teaching loop) so the agent runs it the same way every time.


## Skills

| Skill | What it does |
|-------|--------------|
| [`code-review`](./code-review) | Two-axis review of a diff — **Standards** (does it follow the repo's documented conventions + a Fowler code-smell baseline?) and **Spec** (does it match the originating issue/PRD?) — run as parallel sub-agents and reported side by side. |
| [`grilling`](./grilling) | Interviews you relentlessly, one question at a time with a recommended answer each, walking every branch of the decision tree until you reach shared understanding. The engine the `grill-*` skills build on. |
| [`grill-me`](./grill-me) | Shortcut that runs a `/grilling` session to stress-test a plan or design. |
| [`grill-with-docs`](./grill-with-docs) | A `/grilling` session that also produces docs (ADRs and a glossary) as decisions crystallise. |
| [`improve-codebase-architecture`](./improve-codebase-architecture) | Scans a codebase for deepening opportunities, presents them as a visual HTML report, then grills through whichever one you pick. |
| [`loop-me`](./loop-me) | A stateful grilling session whose only output is workflow specs. |
| [`teach`](./teach) | Teaches a topic across multiple sessions, tracking learning records and the zone of proximal development. |
| [`handoff`](./handoff) | Compacts the current conversation into a handoff document so a fresh agent can pick up the work. |
| [`writing-great-skills`](./writing-great-skills) | Reference for writing and editing skills well — the vocabulary and principles that keep a skill predictable. |

## Layout

Each skill is a top-level folder containing a `SKILL.md` (name + description frontmatter, then the instructions). Some also carry supporting files:

```
<skill>/
├── SKILL.md              # required: the skill itself
├── agents/openai.yaml    # optional: agent config
└── *.md                  # optional: format/reference docs the skill points to
```

## Installing

Clone into your Claude Code skills directory (or symlink individual folders):

```bash
git clone https://github.com/<your-username>/skills.git ~/.claude/skills
```

Then invoke a skill with `/<skill-name>` in Claude Code, e.g. `/code-review` or `/grill-me`.

## Notes on cross-skill references

A couple of skills reference sibling skills that are **not** included in this repo (they live in the upstream collection):

- `grill-with-docs` calls `/domain-modeling`
- `improve-codebase-architecture` calls `/codebase-design`

If you use those skills, either add the referenced skills or adjust the references.

## License

[MIT](./LICENSE)
