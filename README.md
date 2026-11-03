# MQL5 Workshop

Routing-first agent skill for official **MQL5 Reference** (EN/ZH) + offline CHM corpus.

- EN: https://www.mql5.com/en/docs
- ZH: https://www.mql5.com/zh/docs

Works with **Exness** MT5. Intro: https://one.toexness.com/intl/zh/a/30852

## Install

```bash
npx skills add wwwgzpucom/mql5-workshop --skill mql5-workshop
```

Skill: `.claude/skills/mql5-workshop/SKILL.md`

## Design

1. **Task router** — jump by job (trade / indicator / panel / tester / …)
2. **Hard rules** — verified against official docs / `mql5.chm`
3. **Anti-patterns** — common failures with corpus page stems
4. **Playbooks** — minimal correct sequences, not encyclopedia dumps
5. **Atlas** — where to look up; StdLib/indicators stay as indexes
6. **Ship checklist** — compile + runtime self-check

Local corpus (optional): `mql5-ebook-corpus/pages/<topic>.htm.txt`

## License

MIT
