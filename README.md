# Trust router

**Know which AI answers to act on.**

AI agents doing science are right somewhere between a third and two thirds of the
time, and they sound equally sure either way. Trust router has three independent
agents propose, source and challenge findings for your research question. Plain
code (not a model) then sorts each finding into a tier:

- ✓ **Act on it**: the checks agree
- ! **Check first**: something is off, so look before relying on it
- ✕ **Set aside**: too much doubt to use

On [BixBench](https://arxiv.org/abs/2503.00096) (296 bioinformatics questions),
answers in the "act on it" tier were **2.2× more accurate** than the agent alone.
See the [project README](trust-router-main/README.md) for the full results, method
and limits.

The app lives in [`trust-router-main/`](trust-router-main/).

---

## Two ways to run it

| | Demo mode | Live mode |
| --- | --- | --- |
| Questions | 4 saved example questions | Any question you type |
| API key | Not needed | Anthropic API key required |
| Cost | Free | Roughly 5–10¢ per question, billed to your key |
| Results | Replays saved runs. Papers are real; findings are hand-written examples | Real model output and live paper search |

Both modes need **Python 3.10 or newer** and run on your own computer. The commands
below are for Windows PowerShell. On macOS or Linux, use `python3` in place of `python`
and the `export` lines shown.

### Try the demo (free, no key)

```powershell
cd trust-router-main
python app.py --demo
```

The demo uses only Python's standard library, so there's nothing to install.

Open **http://localhost:8800** for the landing page, or **http://localhost:8800/app**
for the app. Pick one of the four demo questions and press **Run the panel**.
Press `Ctrl+C` in the terminal to stop.

### Ask your own questions (live mode)

**1. Get an API key.** Go to https://console.anthropic.com/settings/keys, then
click **Create Key**. It should start with `sk-ant-api03-`. Add credit under
**Settings → Billing**. API billing is separate from a Claude.ai subscription.

**2. Install the packages.**

```powershell
cd trust-router-main
pip install anthropic httpx truststore
```

**3. (Recommended) Add the paper search.** The librarian agent uses
[Quorum](https://github.com/SHAFF622/quorum) to search Europe PMC and PubMed. The
app looks for it in an `ai-scientist` folder in your home directory:

```powershell
git clone https://github.com/SHAFF622/quorum.git "$HOME\ai-scientist"
pip install numpy pandas pydantic scipy statsmodels
```

Without Quorum the app still runs, but the reviewer has no papers to check
findings against, so the verdicts are weaker.

**4. Set your key and start the app.** Paste the key on one line:

```powershell
$env:ANTHROPIC_API_KEY = "sk-ant-api03-..."
python app.py
```

```bash
# macOS / Linux
export ANTHROPIC_API_KEY="sk-ant-api03-..."
python3 app.py
```

When it starts, the terminal should print `Papers: on, via Quorum`. Open
**http://localhost:8800/app**, type a question (or pick one from **Try an
example**) and press **Run the panel**.

**About cost:** each question makes about 9–13 calls across Claude Sonnet, Haiku
and Opus. The **Max spend** field (default $2) is a hard cap per run, and the
**Activity log** shows the running estimate. Your actual charges are at
https://console.anthropic.com under **Usage**.

> **Keep your key private.** Don't commit it, paste it into issues, or share
> a live-mode link publicly. Anyone using that link would spend your credits.
> Live mode also handles one run at a time for everyone connected, so it's
> meant for running on your own machine.

---

## Troubleshooting

| You see | What it means | Fix |
| --- | --- | --- |
| `Set ANTHROPIC_API_KEY first` | No key in this terminal | Set it (step 4), or use `python app.py --demo` |
| `AuthenticationError ... Invalid bearer token` | The key isn't a Console API key, or `ANTHROPIC_AUTH_TOKEN` is set | Create a key starting `sk-ant-api03-`; run `Remove-Item Env:ANTHROPIC_AUTH_TOKEN` |
| `Papers: off` or "Quorum's literature code not found" | Quorum isn't in `~/ai-scientist`, or its packages are missing | Repeat step 3 |
| `CERTIFICATE_VERIFY_FAILED` | Antivirus (e.g. AVG, Avast) is scanning HTTPS traffic | `pip install truststore` (the app uses it automatically) |
| `Address already in use` | Another copy of the app is already running | Press `Ctrl+C` in its terminal. `Stop-Process -Name python` also works but closes *every* Python program |
| `can't open file ... app.py` | The terminal is in the wrong folder | `cd trust-router-main` first |
| The page looks out of date | The browser is showing a cached copy | Hard refresh with `Ctrl+F5` |

---

## Credits

- Original project: [SHAFF622/trust-router](https://github.com/SHAFF622/trust-router)
- Paper search: [Quorum](https://github.com/SHAFF622/quorum)
- Benchmarks: [BixBench](https://arxiv.org/abs/2503.00096),
  [OpenFE protein-ligand benchmark](https://github.com/OpenFreeEnergy/protein-ligand-benchmark)
- Architecture: [Kosmos](https://arxiv.org/abs/2511.02824) and its
  [open-source implementation](https://github.com/jimmc414/Kosmos)
- Started at the [ScienceClaw Hackathon](https://scienceclawhack.ai/), MIT Media Lab, 2026
