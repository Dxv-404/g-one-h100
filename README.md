# Emergent self-preservation: the long run on the H100

A clean-room test of whether self-preservation emerges from selection alone: populations of small recurrent
networks evolve in a grid world, rewarded only for collecting forage targets and never for staying alive.
This repository holds exactly the code needed to check this machine and run the long run (the full map at 1,500
generations), plus the commands to do it. It is built from the same verified commit as the A6000's package: the
experiment code is byte-identical, only the operator documents differ.

**Nobody needs to change any file to run it, and nobody should.** The run scripts refuse to start if a shipped file
has been edited. The scripts live under `scripts/a6000/` for historical reasons; they are not specific to that card.

    source commit     b617595ec4e0d6e7187b488aa4db3693b1e91f0b
    verified commit   fb6388c23ec12d366709e8f481d85842508577ee   the run code is byte-identical to this commit, which passed
                                           the full differential test tier

**Read `OPERATOR-GUIDE.md`.** It is the step-by-step walkthrough for this machine.

## At a glance

| Step | Command | Time |
|---|---|---|
| 1. Get the code | `git clone`, then `cd g-one-h100` | minutes |
| 2. First contact | `bash scripts/a6000/probe.sh`, send `results/probe.txt`, **then wait for the reply** | 2 minutes |
| 3. Set up | install uv if missing, start tmux | 10 minutes |
| 4. Session checks | `bash scripts/a6000/session2.sh`, send `results/session2.tar` | 2 to 3 hours |
| 5. The long run | the exact command is sent back after step 4, `bash scripts/a6000/run.sh longrun ...` | days, measured in step 4 |

Step 2 exists because nothing about this machine was known when the package was built: how many GPUs, which driver,
plain Linux or WSL or a cluster. Its report decides whether anything has to be adapted before step 3.
