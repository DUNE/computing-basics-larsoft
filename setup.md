---
title: "Mission Setup for LArSoft Basics"
teaching: 60
exercises: 20
questions:
- How do I prepare for the LArSoft Basics tutorial?
- Which environment do I use, AL9 or the SL7 container?
objectives:
- Get an environment ready for DUNE, AL9 and Spack by default
- Understand the authentication procedure
- Know when to use the SL7 container instead
- Run a short exercise to check the setup
keypoints:
- DUNE is moving from SL7 (Apptainer, UPS) to native AL9 (Spack)
- Running dunesw tools on existing files works under the dune-prototype Spack environment on AL9
- Building or modifying code with mrb still needs the SL7 container
- Do not source both environments in one shell
- Kerberos authenticates you to FNAL machines; tokens authenticate data access
---

## Objectives

- Get an environment ready for DUNE, AL9 and Spack by default
- Understand the authentication procedure
- Know when, and why, to use the SL7 container instead
- Run a short exercise to check that the setup works

## Two environments

DUNE is part way through moving from SL7 (run through Apptainer, using UPS) to native
AL9 (using Spack). At the time of writing:

- Running LArSoft and `dunesw` tools (`lar`, `config_dumper`, the event display, and so
  on) on existing files works under the `dune-prototype` Spack environment on AL9.
- Building or modifying LArSoft or DUNE code with `mrb` does not yet work under Spack on
  AL9. Episodes 05.5 and 06 check out and edit source code, so they still use the SL7
  container.

This part of the migration moves quickly. If a command below does not behave as
described, check `#computing-training-basics` on DUNE Slack; the Spack environment may
have moved on since this page was last checked.

> ## Do not mix environments
> Source either the AL9/Spack setup or the SL7 container in a shell, not both, and start
> a fresh login shell when you switch.
>
> This matters most in one direction: Apptainer passes the parent shell's environment
> into the container, so if the Spack environment is active when you start the SL7
> container, its `LD_LIBRARY_PATH` leaks in and the container's `root` and `lar` try to
> load the AL9 ROOT libraries. You get
> `Fatal in <TROOT::InitInterpreter>: cannot load library /lib64/libc.so.6: version GLIBC_2.33 not found`.
> `setup dunesw` fixes `PATH` but not `LD_LIBRARY_PATH`, so the fix is to enter the
> container from a shell where you have not run any `spack` setup.
{: .callout}

Before the tutorial, follow the setup for the
[Computing Basics tutorial](https://dune.github.io/computing-basics/setup.html) and read
its storage and data management material.

## Step 1: Accounts

You need to be a DUNE collaborator with a valid FNAL or CERN computing account. See
[DUNE Basic Setup](https://dune.github.io/computing-basics/setup.html) if you do not have
one yet.

## Step 2: AL9 / Spack setup (default)

Log in to a `dunegpvmXX.fnal.gov` machine, which runs Alma Linux 9 with no container
needed, or to `lxplus.cern.ch`. Start from a clean shell with no other experiment's
setup sourced.

~~~
source /cvmfs/dune.opensciencegrid.org/spack/setup-env.sh
spack env activate dune-prototype
~~~
{: .language-bash}

Activating the environment is enough to put `lar`, `root`, and the rest of `dunesw` on
your path. You do not need a separate `spack load`.

For Spack and MPD documentation, configuration reference, and issue tracking, see the
[DUNE Spack project](https://dune.github.io/dune-spack-project/).

> ## Instructor check: environment
> Verified 2026-09-07 on `dunegpvm13`: `$SPACK_ROOT` is **v1.2.2**, environment
> **`dune-prototype`**. `setup-env.sh` selects the current instance automatically; do
> not source v1.0 or v1.1 directly. Spack and MPD docs, config reference, and issues:
> [https://dune.github.io/dune-spack-project/](https://dune.github.io/dune-spack-project/).
>
> `spack find --paths` shows `dunesw`, `root`, and `gcc` living under a
> `.../spack/v1.1.1/opt/spack/...` tree, each marked `[^]`. That is expected: v1.2.2
> uses v1.1.1 as an upstream and reuses its builds rather than rebuilding. The
> environment is still served through v1.2.2.
>
> In a live session, confirm:
>
> - `spack env list` includes `dune-prototype`
> - `which lar` resolves under `.../environments/dune-prototype/.spack-env/view/bin/`
> - `spack find dunesw` shows one installed `dunesw@...`
>
> > ## What you should see
> > ~~~
> > $ which lar && root --version
> > /cvmfs/dune.opensciencegrid.org/spack/environments/dune-prototype/.spack-env/view/bin/lar
> > ROOT Version: 6.28/12
> >
> > $ spack find dunesw
> > -- linux-almalinux9-x86_64_v3 / %c,cxx=gcc@12.5.0 ---------------
> > dunesw@10.22.00d01
> > ==> 1 installed package
> > ~~~
> > {: .output}
> {: .solution}
{: .callout}

Set up disk-area variables:

~~~
export DUNEDATA=/exp/dune/data/users/$USER
export DUNEAPP=/exp/dune/app/users/$USER
export PERSISTENT=/pnfs/dune/persistent/users/$USER
export SCRATCH=/pnfs/dune/scratch/users/$USER

mkdir -p $DUNEAPP $DUNEDATA $SCRATCH $PERSISTENT
~~~
{: .language-bash}

Check that the tools are on your path:

~~~
which root
root --version
which lar
lar --help
~~~
{: .language-bash}

> ## Exercise: AL9 sanity check
> 1. Confirm `root --version` prints a version rather than "command not found".
> 2. Confirm `which lar` resolves to a path under the Spack tree.
> 3. Run `date >& $DUNEAPP/my_first_login.txt` and check the file.
> 4. If `lar --help` fails, `dune-prototype` does not have `dunesw` set up in your
>    session. Stop here and flag it rather than continuing into Episode 04.
{: .challenge}

## Step 3: SL7 container (only to build or modify code)

Skip this step if you are only running existing tools on existing files.

Episodes 05.5 (mrb) and 06 (modify a module) need the SL7 container, because `mrb` and
UPS-based development do not yet work under Spack on AL9.

> ## Start an SL7 Apptainer
> > ## gpvm
> > ~~~
> > {% include apptainer_gpvm.md %}
> > ~~~
> > {: .language-bash}
> {: .solution}
> > ## build machine
> > `/pnfs` is not mounted on the build machines.
> > ~~~
> > {% include apptainer_build.md %}
> > ~~~
> > {: .language-bash}
> {: .solution}
> > ## CERN
> > ~~~
> > {% include apptainer_cern.md %}
> > ~~~
> > {: .language-bash}
> {: .solution}
{: .challenge}

These are long commands. It helps to define aliases in a login script:

~~~
alias dunesl7="/cvmfs/oasis.opensciencegrid.org/mis/apptainer/current/bin/apptainer shell --shell=/bin/bash -B /cvmfs,/exp,/nashome,/pnfs/dune,/opt,/run/user,/etc/hostname,/etc/hosts,/etc/krb5.conf --ipc --pid /cvmfs/singularity.opensciencegrid.org/fermilab/fnal-dev-sl7:latest"

alias dunesl7build="/cvmfs/oasis.opensciencegrid.org/mis/apptainer/current/bin/apptainer shell --shell=/bin/bash -B /cvmfs,/exp,/build,/nashome,/opt,/run/user,/etc/hostname,/etc/hosts,/etc/krb5.conf --ipc --pid /cvmfs/singularity.opensciencegrid.org/fermilab/fnal-dev-sl7:latest"

alias dunesl7CERN="/cvmfs/oasis.opensciencegrid.org/mis/apptainer/current/bin/apptainer shell --shell=/bin/bash -B /cvmfs,/afs,/opt,/run/user,/etc/hostname --ipc --pid /cvmfs/singularity.opensciencegrid.org/fermilab/fnal-dev-sl7:latest"

alias dunesetups="source /cvmfs/dune.opensciencegrid.org/products/dune/setup_dune.sh"
~~~
{: .language-bash}

A container starts with a bare environment and does not source your `.profile`, so do
that yourself. Then set up `dunesw`:

~~~
dunesetups

export DUNELAR_VERSION=v10_22_00d01
export DUNELAR_QUALIFIER=e26:prof
setup dunesw $DUNELAR_VERSION -q $DUNELAR_QUALIFIER
~~~
{: .language-bash}

> ## Instructor check: dunesw version
> `dune-prototype` on AL9 carries `dunesw@10.22.00d01` (verified 2026-09-07), so
> `v10_22_00d01` is correct here. Inside the SL7 container, confirm the UPS tag and
> qualifier with `ups list -aK+ dunesw` near the session date, and update every
> `DUNELAR_VERSION` in this lesson (including Episode 06) if it has moved.
{: .callout}

## Step 4: Authentication for streaming and grid access

DUNE has moved from grid proxies to tokens for data-access authentication. Get a token
once per session, before any command that streams a file over XRootD or submits to the
grid:

~~~
htgettoken -a htvaultprod.fnal.gov -i dune
~~~
{: .language-bash}

This opens a browser page (or prints a URL to open) for a one-time authentication, then
caches a token for the rest of the session. Check it with:

~~~
httokendecode
~~~
{: .language-bash}

The same command works in the AL9/Spack environment and inside the SL7 container.

> ## Instructor check: tokens
> Verified 2026-09-07 on AL9: `htgettoken -a htvaultprod.fnal.gov -i dune` is the
> current command, matching
> [https://dune.github.io/computing-basics/Tokens/index.html](https://dune.github.io/computing-basics/Tokens/index.html).
> The older `setup_fnal_security` grid-proxy path is not needed for this lesson; leave it
> as an SL7-only fallback if a site still requires an X.509 proxy.
{: .callout}

## Useful links

- [DUNE FAQ][dunefaq]
- [DUNE Interactive Computing Resources wiki][dune-wiki-interactive-resources]
- [Tokens setup](https://dune.github.io/computing-basics/Tokens/index.html)

{%include links.md%}

[dunefaq]: https://github.com/orgs/DUNE/projects/19/views/1
[dune-wiki-interactive-resources]: https://wiki.dunescience.org/wiki/DUNE_Computing/DUNE_Interactive_Computing_Resources
