# Operator guide: the H100

Every command, why it is there, and what to expect. Run them in order. Nothing here needs editing a file.

## 1. Get the code

The repository is private, so the machine needs your GitHub login once.

If `gh` is installed (`gh --version` prints a version):

    gh auth login
    cd ~
    gh repo clone Dxv-404/g-one-h100
    cd g-one-h100

If it is not, clone over HTTPS. Git asks for a username and a password; give your GitHub username and, as the
password, a personal access token (github.com, Settings, Developer settings, Personal access tokens), not your
account password:

    cd ~
    git clone https://github.com/Dxv-404/g-one-h100.git
    cd g-one-h100

Keep the folder in the Linux home folder. On WSL never put it under `/mnt/c`, which is hundreds of times slower for
the compilation cache.

## 2. First contact: the probe

    bash scripts/a6000/probe.sh

Read-only, about two minutes. It prints and saves `results/probe.txt`: the GPUs, the driver and the CUDA version it
supports, whether this is plain Linux, WSL, a container or a cluster, the CPU, memory, disk, network and tools.

**Send `results/probe.txt` and wait for the reply before going on.** If the machine is a cluster with a job
scheduler, or the driver is too old for the project's CUDA 12.9, the next steps change, and it is cheaper to find out
now than two hours into a check.

## 3. Set up

**uv**, which builds the Python environment, if `uv --version` says it is missing:

    curl -LsSf https://astral.sh/uv/install.sh | sh

then close and reopen the terminal.

**tmux**, so that work survives a closed window or a dropped connection:

    tmux new -s h100

Everything from here runs inside it. To leave it running, press Ctrl+B, then D. To return: `tmux attach -t h100`.

## 4. Session checks

    bash scripts/a6000/session2.sh

About 2 to 3 hours, four parts in order, each reported as it finishes:

| Part | What it checks | Why |
|---|---|---|
| check | the full machine check: installs the environment, runs the test gates | the run refuses to start on an unchecked machine |
| determinism | one seed of the final program in 6 fresh processes must agree bit for bit | different compilations of one program once disagreed (DEV-038) |
| concurrency | how many long-run processes at once per card (up to 32), then every card at once, results compared bit for bit; the costliest conditions timed; a projected duration with the in-run S4 included | sets how the run is split and how long it takes |
| recheck | the S4 learnability check on the frozen world, on this card | the calibration ran on a laptop |

It refuses to start if a long run or another check is already running. At the end it says which file to send:
`results/session2.tar`. **Send it and wait**: the launch command depends on what it measured.

## 5. The long run

The exact command comes back after step 4, in this form:

    bash scripts/a6000/run.sh longrun --shards=N --gpus=K

`--gpus=K` only on a machine with K GPUs; each process is pinned to one card. The run is resumable: after a crash, a
reboot or a power cut, the same command continues where it stopped, and finished work is never repeated. It prints
its progress and its own time estimate. When it finishes it says which folders to send.

## If something stops

Every stop prints a line beginning `STOPPED:` that names the fix. Send that line if the fix is not obvious.
