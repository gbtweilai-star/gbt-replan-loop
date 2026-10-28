# replan_loop

**One capability bit from GBT V8, packaged standalone.** No. 142 of 341,
`llm` domain (LLM).

<p>
<img alt="python" src="https://img.shields.io/badge/python-3.9%2B-blue">
<img alt="license" src="https://img.shields.io/badge/license-MIT-green">
<img alt="deps" src="https://img.shields.io/badge/runtime%20dependencies-0-brightgreen">
</p>

> 大脑执行层·**回流环**（主人令 2026-10-13「遇到问题不能直接停下把问题返回大脑执行层，分析推理规划从新生成任务图在继续推进以此循环」）：卡点 → 交回大脑执行层 → **重生成图**（增「排因」步、rev+1、重出 markmap）→ 继续。**轮次上限 3**，到顶如实报到顶，不假装还能推。op=tick/history/status

That description is copied verbatim from the registry. `tests/test_spec.py` pins it,
so documentation drift fails the build instead of shipping quietly.

## What this package actually is

It is the **contract** for one capability: its name, its parameters, the values it
accepts, and whether it can change state that cannot be restored. It is not the
implementation — that lives in the runtime this bit was extracted from.

So `run()` goes through a narrow `Backend` protocol, and the default backend is a
stub that **says so**:

```
status="validated"   backend="stub"   data={"executed": false}
```

Never `status="ok"`. A stub that reports success is worse than no stub, because the
caller stops looking for the real thing.

## Install

```bash
git clone https://github.com/gbtweilai-star/gbt-replan-loop.git
cd gbt-replan-loop
python -m pip install -e ".[dev]"
```

## Use

```bash
python -m gbt_replan_loop info                  # capability card
python -m gbt_replan_loop spec --json           # machine-readable contract
python -m gbt_replan_loop check --payload '{}'
python -m gbt_replan_loop run   --payload '{}'
```

Real `info` output from this machine:

```text
replan_loop  (llm)  v1.0.0
----------------------------------------
大脑执行层·**回流环**（主人令 2026-10-13「遇到问题不能直接停下把问题返回大脑执行层，分析推理规划从新生成任务图在继续推进以此循环」）：卡点 → 交回大脑执行层 → **重生成图**（增「排因」步、rev+1、重出 markmap）→ 继续。**轮次上限 3**，到顶如实报到顶，不假装还能推。op=tick/history/status

gated      : False
external   : False
multi-op   : True
params     : (none)
values     : tick, history, status
```

Wire a real implementation:

```python
from gbt_replan_loop import run

class MyBackend:
    name = "my-backend"
    def invoke(self, cap, payload):
        return {"cap": cap, "handled": sorted(payload)}

result = run({}, backend=MyBackend())
assert result.status == "ok"
```

## Parameters

| name | required | notes |
|---|---|---|
| — | no | the registry declares no required parameters |

Registry hint (`params_hint`): (empty)

**注册表登记的取值 / 子动作（3 个）**：`tick`、`history`、`status`

Types are deliberately absent: the registry does not declare them, and an invented
type is worse than no type, because callers believe it.

## Boundaries

- Not gated: the registry marks this bit `safe: true`.
- No external service dependency declared.
- Multi-op (`multi_op: true`): several sub-actions share one name — read `op` before calling.
- No implementation ships here. Bring a backend; the protocol is one method.

## What this is not

Not a wrapper around a hosted API. Not a model. Not a promise that calling it does
anything on its own. It is the typed seam plus the validation, which is the part that
stays correct even when the implementation changes.

## Tests

```bash
python -m pytest
```

```text
20 passed, 3 skipped in 0.11s
```

## Provenance

| field | value |
|---|---|
| capability | `replan_loop` |
| domain | `llm` |
| handler | `replan_loop` |
| safe | `True` (gated = `False`) |
| external | `False` |
| atlas page | [llm/replan_loop](https://github.com/gbtweilai-star/gbt-caps/blob/main/caps/llm/replan_loop.md) |
| full index | [https://github.com/gbtweilai-star/gbt-caps](https://github.com/gbtweilai-star/gbt-caps) |

The parent README says 348 capabilities; an AST pass over the registry enumerates
`341 + 0 unimplemented + 0 disabled = 341`. The extra 7 are historical duplicates.
Numbering here follows 341 and matches the `gbt-caps` index one-to-one.

## License

MIT.
