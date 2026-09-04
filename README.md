<div align="center">

<img src=".github/hero.png" alt="VLS — run any model on your own machine, and give it a memory far larger than its window" width="100%">

<br><br>

[![Release](https://img.shields.io/github/v/release/Veloresearch/VLS-32M-Context-Local-LLM?label=release&color=2B2B31&labelColor=17171A&style=for-the-badge)](https://github.com/Veloresearch/VLS-32M-Context-Local-LLM/releases/latest)
[![Downloads](https://img.shields.io/github/downloads/Veloresearch/VLS-32M-Context-Local-LLM/total?color=2B2B31&labelColor=17171A&style=for-the-badge)](https://github.com/Veloresearch/VLS-32M-Context-Local-LLM/releases)
[![Windows](https://img.shields.io/badge/Windows-x64-2B2B31?labelColor=17171A&style=for-the-badge&logo=windows&logoColor=f4f4f3)](#windows)
[![Linux](https://img.shields.io/badge/Linux-x64_%C2%B7_Vulkan-2B2B31?labelColor=17171A&style=for-the-badge&logo=linux&logoColor=f4f4f3)](#linux)
[![Licence](https://img.shields.io/badge/free_for_personal_use-2B2B31?labelColor=17171A&style=for-the-badge)](LICENSE)
[![Stars](https://img.shields.io/github/stars/Veloresearch/VLS-32M-Context-Local-LLM?style=for-the-badge&color=2B2B31&labelColor=17171A&logo=github&logoColor=f4f4f3)](https://github.com/Veloresearch/VLS-32M-Context-Local-LLM/stargazers)

**[Install](#install-it-in-one-line)** · **[What it does](#what-is-actually-in-it)** · **[Measured](#measured-not-claimed)** · **[Mesh](#mesh--the-machines-in-your-house-as-one)** · **[API](#connect-anything)**

</div>

<br>

## Install it in one line

<a name="windows"></a>**Windows**

```powershell
irm https://github.com/Veloresearch/VLS-32M-Context-Local-LLM/releases/latest/download/install.ps1 | iex
```

<a name="linux"></a>**Linux**

```bash
curl -fsSL https://github.com/Veloresearch/VLS-32M-Context-Local-LLM/releases/latest/download/install.sh | sh
```

No account, no sign-in, no telemetry. The installer picks the build that suits the machine —
CUDA where there is an NVIDIA card, Vulkan on Linux where there is any card at all, processor
otherwise — and opens the panel when it is done. Removing it is deleting one folder.

<br>

---

## The problem it solves

A model's context window is a hard limit paid for in memory. Eight thousand tokens, thirty-two
thousand, a hundred and twenty-eight — whatever it is, your work is bigger than it, and the usual
answers are to send less, to summarise until the detail is gone, or to build a vector database and
hope the chunks that come back are the right ones.

**VLS gives the model a memory instead of a bigger window.** You compile a folder once. Every
question after that reaches the whole of it: the service works out which passages the answer needs,
hands the model only those, and shows you a receipt of exactly what it read.

The model's window never changes. What changes is that it stops mattering.

<div align="center">
<br>
<img src=".github/home.png" alt="The VLS panel" width="940">
<br><br>
</div>

<br>

---

## What is actually in it

### A local server that speaks OpenAI

<img src=".github/api.png" alt="The API page" width="100%">

An OpenAI-compatible endpoint on a local address, so anything that already talks to OpenAI talks to
VLS — editors, agents, scripts, your own code. The **API** page shows the address of *this machine
on your network*, not `localhost`, together with the key and a request you can paste.

`context_length` is the model's window: the most one request may contain. How far the memory
reaches is reported beside it, with how much is used and what happens when it fills. Two different
numbers, never added together, because a client that confuses them builds prompts that get trimmed
without anybody noticing.

<br>

### Models, and whether they fit before you download

<img src=".github/models.png" alt="Models" width="100%">

Search Hugging Face from inside the panel. VLS reads each build's header, compares it with the card
and memory it found in this machine, and says which one will run well **before** the download
starts. Transfers keep running when you leave the page, show their progress on the Models page and
in Activity, and a half-finished file is never given a model's name — so it can never be offered to
you as something loadable.

Load and unload from the tile. Point VLS at a folder of GGUFs you already have and it reads their
headers without loading anything.

<br>

### Velocity Context — the memory

<img src=".github/contexts.png" alt="Contexts" width="100%">

Compile a folder — notes, a codebase, an export, a library of PDFs turned to text — and it becomes
addressable. Compiling 32 million tokens takes **17.2 seconds**, once. After that every question
selects what it needs out of the whole store in about half a second, and the answer arrives with a
receipt: which documents were read, which passages, how many tokens of the window they took.

It is not a vector database. Nothing is embedded, no index is rebuilt per query, and there is no
second service to run. The store is a file on your disk in a format you can read.

<br>

### Playground — with a receipt for every answer

<img src=".github/playground.png" alt="Playground" width="100%">

Talk to the loaded model with the memory attached. A reasoning model's working is shown as it
happens rather than behind a spinner, and **thinking is a switch beside the message box**, per
conversation: on for a hard question, off for a lookup. On a short factual question that is the
difference between 4.1 seconds and 0.2.

Every answer carries what it cost: tokens in, tokens out, prefill and decode rates, and what the
memory handed over.

<br>

### Mesh — the machines in your house as one

<img src=".github/mesh.png" alt="Mesh" width="100%">

The old laptop in the drawer, the box under the desk, the one with the card in it. Turn the mesh on
and they find each other on your network by themselves — no addresses to type, no configuration.
One machine makes a join code, you paste it into the others, and the panel shows what the whole
house has: every machine, its address, which one is in charge, how much memory it brings and how
fast it reads it.

The join code never travels over your network. Only a hash of it does, so a stranger on your wifi
learns that VLS exists and nothing else. A machine that is *found* is listed and asked nothing until
it is *joined*.

> **Honest about the state of it.** What ships today is discovery, the pooled view of what your
> machines have, and the placement plan — which device would hold which layers, fastest first, GPU
> before system memory, so a model too large for one machine has somewhere to go. Splitting a model
> across machines is measured and works; wiring it into the product is the next piece of work, and
> until it lands the mesh routes and reports rather than splits.

<br>

### Flows — work VLS does on its own

<img src=".github/flows.png" alt="Flows" width="100%">

A canvas of nodes joined by wires: read a folder, compile it into memory, ask the model, branch on
the answer, write the result to a file — on a schedule, without you. A wire carries text; the order
is worked out from the graph; a loop is refused rather than run.

Everything runs on this machine. No accounts, no OAuth, no webhooks out — which is a decision, not
a gap. An automation product's value is its four hundred integrations, each with its own breaking
change every quarter, and the moment VLS asks for somebody's mail password it becomes a thing worth
attacking. What is here instead is the work only VLS can do, because only VLS has the memory.

<br>

### Benchmarks you run yourself

<img src=".github/benchmarks.png" alt="Benchmarks" width="100%">

Decode speed, time to first token, context retrieval, BABILong-style long context, and your own
prompt — measured on **your** machine with **your** model, and kept as a record of runs that
actually happened. Nothing here is a number we shipped; it is a number your computer produced.

<br>

### Everything the machine is doing

<img src=".github/system.png" alt="System" width="100%">

Card, memory, temperature, what is resident and where it was placed, which backend took the model
and why. Usage and client history survive restarts and updates — a record that only lasts until the
next release is not a record.

<br>

---

## Measured, not claimed

Everything below ran on one laptop: an **RTX 3060 Laptop, 6 GB**, with **Qwen3.5-4B-Q4_K_M** and an
**8 192-token window**. Read the parts that do not flatter us; they are there on purpose.

### NVIDIA's RULER, their harness

Cloned, nothing underneath it modified: their generators, their scorer, their prompts. The 128k
column is twenty samples a task — 260 runs.

| RULER task | 4k | 16k | 32k | 64k | 128k |
|---|---:|---:|---:|---:|---:|
| niah_single_1 | 100 | 100 | 100 | 100 | 100 |
| niah_single_2 | 100 | 100 | 100 | 100 | 100 |
| niah_single_3 | 100 | 100 | 100 | 100 | 100 |
| niah_multikey_1 | 100 | 100 | 100 | 100 | 100 |
| niah_multikey_2 | 100 | 20 | 100 | 100 | 100 |
| niah_multikey_3 | 100 | 0 | 40 | 100 | 75.0 |
| niah_multivalue | 100 | 100 | 100 | 100 | 100 |
| niah_multiquery | 100 | 100 | 100 | 100 | 98.8 |
| vt | 100 | 32 | 72 | 60 | 78.0 |
| cwe | 100 | 100 | 100 | 98 | 98.5 |
| fwe | 100 | 100 | 100 | 100 | 100 |
| qa_1 | 100 | 80 | 60 | 60 | 50.0 |
| qa_2 | 100 | 40 | 40 | 40 | 30.0 |
| **13-task average** | **100.0** | **74.8** | **85.5** | **89.1** | **86.9** |

<sub>4k–64k are five samples a cell; 128k is twenty.</sub>

The score **rises** from 16k to 64k, which is backwards for a context benchmark and is the shape
this architecture predicts: the model's window is 8 192 tokens either way, so nothing is ever "too
long to read" — what changes is how much material selection has to choose from. **The 16k column is
the anomaly, not the 128k one**, and we do not yet know why. `qa_2` at 30% is the floor. At five
samples a cell the 128k average reads **89.6**; at twenty it reads **86.9**, and we publish the
second number.

### And beyond, to 32 million

Past 128k NVIDIA's harness stops, so the ladder below is **our own** — same task definitions, our
haystack, our scorer — on the same machine and the same 8 192-token window. The two are reported
separately and never averaged together.

| task | 1M | 5M | 10M | 15M | 20M | 25M | 32M |
|---|---:|---:|---:|---:|---:|---:|---:|
| niah_single_1 | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| niah_single_2 | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| niah_single_3 | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| niah_multikey_1 | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| niah_multivalue | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| niah_multiquery | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| cwe | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| fwe | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| vt | 80 | 60 | 100 | 80 | 80 | 100 | 100 |
| **nine-task average** | **97.8** | **95.6** | **100** | **97.8** | **97.8** | **100** | **100** |

<sub>Five samples a cell, 315 runs. <b>Nine of RULER's thirteen tasks</b> — niah_multikey_2,
niah_multikey_3, qa_1 and qa_2 are not in this sweep, so this average is <b>not</b> comparable to a
published 13-task RULER average.</sub>

### The time to answer does not grow with the corpus

| corpus | answer, median | answer, p90 | one-time build |
|---|---:|---:|---:|
| 1M | 703 ms | 890 ms | 0.5 s |
| 10M | 570 ms | 1 081 ms | 5.3 s |
| 20M | 708 ms | 1 117 ms | 10.6 s |
| 32M | 653 ms | 1 111 ms | 17.2 s |

Thirty-two times the material, the same half-second. That is the whole point of the design: a
question does not read the corpus, it reaches into it.

<br>

---

## Built on

VLS is a **router with a memory**, not an inference engine of its own. Work goes to whichever
backend suits the model, and we are explicit about which parts are ours and which are other
people's.

| layer | what it is | whose |
|---|---|---|
| **Velocity Context** | The 32M-token memory: compiles documents, selects the passages a question needs, hands only those to the model. This is what makes a small window stop mattering. | **ours** |
| **[llama.cpp](https://github.com/ggml-org/llama.cpp)** | The GGUF inference engine underneath every model VLS runs today. Georgi Gerganov and the ggml authors, MIT. We ship their `llama-server` unmodified. | *theirs* |
| **Velocity / MTA** | Our own execution runtime for `.mfy` artifacts — the research line behind Velocity. Selected automatically for models built for it. | **ours** |

Credit where it is owed: **without llama.cpp there would be no VLS to download.** We add the memory,
the routing, the hardware fit, the mesh and the panel. The engine is theirs, and their licence
travels inside every package we ship.

<br>

---

## Connect anything

```bash
curl http://127.0.0.1:11500/v1/chat/completions \
  -H "content-type: application/json" \
  -H "authorization: Bearer YOUR_KEY" \
  -d '{"model":"local","messages":[{"role":"user","content":"hello"}]}'
```

```python
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:11500/v1", api_key="YOUR_KEY")
print(client.chat.completions.create(
    model="local",
    messages=[{"role": "user", "content": "hello"}],
).choices[0].message.content)
```

Ask a question against a compiled memory by naming it:

```bash
curl http://127.0.0.1:11500/v1/chat/completions \
  -H "content-type: application/json" -H "authorization: Bearer YOUR_KEY" \
  -d '{"model":"local","velocity_store":"my-notes","velocity_excerpt":true,
       "messages":[{"role":"user","content":"what did we decide about the schema?"}]}'
```

The reply carries a `velocity_context` block: which store answered, how many windows were read,
what it cost, and — with `velocity_excerpt` — the exact text the model was given.

The panel's **API** page has all of this already filled in with this machine's address and key.

<br>

---

## Where things go

<table>
<tr><td width="50%">

**Windows**

```
%LOCALAPPDATA%\Programs\VLS
%LOCALAPPDATA%\VLS\models
%LOCALAPPDATA%\VLS\contexts
```

</td><td width="50%">

**Linux**

```
~/.local/lib/vls
~/.local/share/vls/models
~/.local/share/vls/contexts
```

</td></tr>
</table>

The first path is the program and an update replaces it wholesale. The other two are yours and an
update never touches them — updating VLS must never cost you a re-download of models. To remove it,
delete the program directory; your models and memories stay until you delete those too.

**Updating:** Settings → Service says whether a newer build exists and installs it in place, with a
progress bar and no commands.

<br>

---

## This is a preview

It is the first build anyone outside Velocity has been able to run, and it behaves like one. There
are rough edges. There are bugs. Some of them are ours to be embarrassed about, and you will
probably find one before we do.

**Please [open an issue](https://github.com/Veloresearch/VLS-32M-Context-Local-LLM/issues) when you
do** — that is what a preview is for, and it is by far the fastest way to get it fixed.

<br>

---

## Licence

**Free for personal use.** Use it on your own machines, for your own work, for as long as you like,
at no cost — including commercial work you do as an individual.

**Companies need a licence.** If VLS is used inside a company, by employees, on company hardware or
in a product, write to **contact@veloresearch.com** and we will sort it out quickly and sensibly.

See [LICENSE](LICENSE) for the exact terms.

<br>

---

<div align="center">

### If VLS is useful to you, a star is the whole ask

It is free, it has no telemetry, and there is nothing else we want from you. A star is how other
people find it.

[![Star this repository](https://img.shields.io/github/stars/Veloresearch/VLS-32M-Context-Local-LLM?style=for-the-badge&color=2B2B31&labelColor=17171A&logo=github&logoColor=f4f4f3&label=star%20this%20repository)](https://github.com/Veloresearch/VLS-32M-Context-Local-LLM)

<br><br>

<img src=".github/logo.png" alt="Velocity" width="52">

**Velocity**

Local runtimes and long-context execution.

[veloresearch.com](https://veloresearch.com) · [@velo_research](https://x.com/velo_research) · [contact@veloresearch.com](mailto:contact@veloresearch.com)

</div>
