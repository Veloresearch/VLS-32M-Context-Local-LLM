<div align="center">

<img src=".github/logo.png" alt="VLS" width="112">

<h1>VLS</h1>

### Velocity Local Services

**Run any GGUF model on your own machine — with up to 32 million tokens of memory beside it.**

No cloud. No retraining. No data leaving the machine.

<br>

[![Release](https://img.shields.io/github/v/release/Veloresearch/VLS-Local-LLM-Server?label=Preview-1&color=4B5BF5&style=for-the-badge)](https://github.com/Veloresearch/VLS-Local-LLM-Server/releases/latest)
[![Downloads](https://img.shields.io/github/downloads/Veloresearch/VLS-Local-LLM-Server/total?color=4B5BF5&style=for-the-badge)](https://github.com/Veloresearch/VLS-Local-LLM-Server/releases)
[![Windows](https://img.shields.io/badge/Windows-x64-4B5BF5?style=for-the-badge&logo=windows&logoColor=white)](#install)
[![Licence](https://img.shields.io/badge/free_for_personal_use-4B5BF5?style=for-the-badge)](LICENSE)

**[veloresearch.com](https://veloresearch.com)** &nbsp;·&nbsp; **[@velo_research](https://x.com/velo_research)** &nbsp;·&nbsp; **[Download](https://github.com/Veloresearch/VLS-Local-LLM-Server/releases/latest)**

</div>

<br>

```powershell
irm https://github.com/Veloresearch/VLS-Local-LLM-Server/releases/latest/download/install.ps1 | iex
```

<br>

---

## What it is

A local model service with a panel. You give it a GGUF model and, if you want, a folder of your own
documents. It runs the model on your card or your processor, serves an OpenAI-compatible API on
`127.0.0.1`, and answers questions out of documents far larger than the model could ever read.

**The part that is not ordinary is the memory.** A 4B model reads 8 192 tokens at a time. VLS keeps
up to 32 million tokens beside it and, for each question, selects the handful of passages that
answer it. The corpus never enters the model's window — so the context length that matters is not
the model's, and adding documents costs disk rather than VRAM.

It works on a model nobody retrained. Qwen, Llama, Mistral, whatever you already have.

<br>

|  |  |
|---|---|
| 🖥️ **Runs GGUF models** | On the card or the processor, with the placement worked out for *your* hardware before anything loads. |
| 🔎 **Finds models** | Search Hugging Face inside the panel: download size, memory needed **on this machine**, and whether it fits — before you spend twenty minutes on the wrong file. |
| 🧠 **Remembers** | Compile a folder into a context and address millions of tokens. Ask questions across all of it. |
| 🧾 **Shows its working** | Every answer carries a receipt: which passages were selected, how much of the memory reached the model, how long each stage took. |
| 🔌 **Speaks OpenAI** | Point an editor, an agent or your own script at `http://127.0.0.1:11500/v1`. It needs to know nothing about VLS. |
| 📊 **Measures itself** | The benchmarks below, runnable from the panel against your own model and your own memory. |

<br>

---

## Install

### Windows

```powershell
irm https://github.com/Veloresearch/VLS-Local-LLM-Server/releases/latest/download/install.ps1 | iex
```

No administrator, no installer wizard, no system changes. It looks for an NVIDIA card and takes the
CUDA package if it finds one, the **25 MB** processor-only package if it does not, verifies the
download against the published SHA-256, and unpacks it into your own profile.

Then the panel is at **http://127.0.0.1:11500/**

### Linux — Docker

> Preview-2. The image is being published; this section will name the exact command when
> `docker pull` actually works. We would rather leave it blank than print a command that 404s.

<br>

---

## Velocity Context

Measured on **NVIDIA's RULER** — their harness, cloned, nothing underneath it modified: their
generators, their essays, their scorer. Driven through this service's own API with
**Qwen3.5-4B** on a laptop **RTX 3060** and an **8 192-token model window**. The 128k column is
twenty samples a task: 260 generations, zero nulls, 24.5 minutes.

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

**How to read this, including the parts that do not flatter us.**

The score *rises* from 16k to 64k, which is backwards for a context benchmark and is the shape this
architecture predicts: the model's window is 8 192 tokens either way, so nothing is ever "too long
to read" — what changes is how much material selection has to choose from. The **16k column is the
anomaly, not the 128k one**, and we do not yet know why.

`qa_2` at 30% is the floor and nothing we have tried has moved it. On `qa_1` and `qa_2` selection
delivers the answer and the 4B model fails to convert it — a smaller model reading well is still a
smaller model.

At five samples a cell the 128k average reads **89.6**. At twenty it reads **86.9**. We publish the
second number.

<br>

---

## Where things go

```
%LOCALAPPDATA%\Programs\VLS      the program.  An update replaces it wholesale.
%LOCALAPPDATA%\VLS\models        your models.  An update never touches them.
%LOCALAPPDATA%\VLS\contexts      your memories, likewise.
```

Updating must never cost you a re-download of models, so the two are kept apart. To remove VLS,
delete the program folder; your models and memories stay until you delete those too.

**Updating:** Settings → Service says whether a newer build exists. VLS reads one small file to find
out and never downloads or replaces anything by itself. The command that updates it is the one that
installed it.

<br>

---

## This is Preview-1

It is the first build anyone outside Velo Research has been able to run, and it behaves like one.
There are rough edges. There are bugs. Some of them are ours to be embarrassed about, and you will
probably find one before we do.

**Please [open an issue](https://github.com/Veloresearch/VLS-Local-LLM-Server/issues) when you do.**
That is what a preview is for, and it is the fastest way to get it fixed.

Nothing here sends your data anywhere, so the worst case is a service that annoys you — not one that
costs you something.

<br>

---

## Licence

**Free for personal use. Commercial use requires a licence.**

Use VLS on your own machine, for your own work, study or curiosity, and pay nothing. Use it inside a
business, or to provide a service to somebody else, and you need a commercial licence —
[licensing@veloresearch.com](mailto:licensing@veloresearch.com).

Full terms: **[LICENSE](LICENSE)** · Third-party components:
**[THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt)**

<br>

---

<div align="center">

**[veloresearch.com](https://veloresearch.com)** &nbsp;·&nbsp; **[@velo_research](https://x.com/velo_research)**

<sub>This repository publishes releases. The source lives elsewhere.</sub>

</div>
