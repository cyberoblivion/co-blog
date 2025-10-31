---
layout: post
title:  "How I Configure My Development Environment"
date:   2025-05-02 20:34:44 -0400
categories: development howto
bootstrap-enabled: false
author: "Ben Erridge"
permalink: /howto/development-env-setup/
description: "A deep dive into my personal development environment setup, including bash aliases, git configuration, and directory structure. Learn how to optimize your workflow with powerful git aliases and smart organization patterns."
---

Every developer has their own way of organizing their workspace. Some like chaos, some crave order. Me? I'm somewhere in between; but I've landed on a setup that keeps me productive without getting in my way. Let's walk through how I configure my development environment, from directory structure to git aliases that save me countless keystrokes.

**Back to the HACKS!**

## Directory Structure: Keep It Simple

My development workspace lives under `~/dev/` with two main subdirectories:

```bash
~/dev/
├── projects/    # Active development projects
└── utils/       # Utility scripts, tools, and binaries
```

This separation keeps things clean. Projects are where the real work happens; active repositories, experiments, client work. Utils is for everything else; helper scripts, third-party tools, standalone utilities that don't belong in a specific project.

<div class="info-panel">
  <div class="info-icon">&#8505;</div>
    <div class="info-content">
    <strong>Quick Navigation Tip:</strong>
    I have a bash alias <code>gopro</code> that jumps straight to <code>~/dev/projects</code>. Just type <code>gopro</code> and you're there. Simple, but it saves time every single day.
    </div>
</div>

Here's what typically lives in each:

**`~/dev/projects/`** contains:
- Git repositories for active projects
- Experimental codebases
- Client work
- Personal projects like this blog (co-blog)
- Third-party repos I'm contributing to or learning from

**`~/dev/utils/`** contains:
- Standalone utility binaries (like winbox64.exe for RouterOS management)
- SDK installations (Google Cloud SDK, etc.)
- Build tools and CLI utilities
- Scripts that work across multiple projects

## Bash Configuration: Power User Settings

Let's dive into my `.bashrc` configuration. This file is where the magic happens; it's loaded every time you open a terminal and sets up your environment.

### History Settings: Never Lose a Command Again

```bash
export HISTSIZE=100000
export HISTFILESIZE=100000
export PROMT_COMMAND='history -n;history -a'
```

These settings give me a massive command history (100,000 commands) and ensure commands are saved immediately. I can't count how many times I've needed to find that obscure command I ran three weeks ago. With this setup, it's always there.

### Essential Aliases

```bash
alias gopro='cd ~/dev/projects'
alias myip='dig +short txt ch whoami.cloudflare @1.0.0.1'
alias winbox='sudo wine /home/ben/dev/utils/winbox64.exe'
```

**`gopro`**: My most-used alias. Jump to projects directory instantly.

**`myip`**: Query Cloudflare's DNS to get your public IP. No need to visit whatismyip.com.

**`winbox`**: Launch MikroTik's WinBox for RouterOS management via Wine. Network admin life.

### Git Prompt Integration

```bash
GIT_PROMPT_THEME=Solarized_UserHost
source ~/dev/projects/bash-git-prompt/gitprompt.sh
```

I use [bash-git-prompt](https://github.com/magicmonty/bash-git-prompt) to show git status directly in my terminal prompt. It displays the current branch, whether you have uncommitted changes, and how many commits you're ahead or behind. Game changer for git workflows.

![Git Bash Prompt in Action](/assets/command-line.png)

### Development Tool Settings

```bash
export MAVEN_OPTS="-Xmx1G -XX:MaxPermSize=512M"
export USER_MEM_ARGS="-Xmx4G"
umask 0002
```

These configure memory limits for Maven builds and set a sensible umask for file permissions (group-writable by default, which is handy for team environments).

### SDKMAN Integration

```bash
export SDKMAN_DIR="$HOME/.sdkman"
[[ -s "$HOME/.sdkman/bin/sdkman-init.sh" ]] && source "$HOME/.sdkman/bin/sdkman-init.sh"
```

[SDKMAN](https://sdkman.io/) is a lifesaver for managing Java, Gradle, Maven, and other JVM-related tools. This snippet initializes it at shell startup.

## Git Configuration: Aliases That Save Hours

Now for the real power user stuff. My `.gitconfig` file contains aliases that make git operations lightning-fast. Let's break down each one:

### Basic Shortcuts

```ini
[alias]
    s = status
    co = checkout
    br = branch
    com = commit
```

These are straightforward time-savers. Instead of typing `git status`, just `git s`. Same for checkout, branch, and commit. Muscle memory loves these.

### Remote Operations

```ini
    f = fetch --all
    r = remote -v
```

**`git f`**: Fetch from all remotes at once. Essential when working with multiple remote repositories or forks.

**`git r`**: Show all configured remotes with their URLs. Quick way to verify where you're pushing and pulling from.

### Advanced: Incoming and Outgoing Commits

These are my favorites:

```ini
    out = log --pretty=oneline --abbrev-commit --graph @{u}..
    in = !git fetch && git log --pretty=oneline --abbrev-commit --graph ..@{u}
```

**`git out`**: Shows commits you have locally that haven't been pushed yet. Perfect before pushing to verify what you're about to send upstream.

**`git in`**: Fetches from remote and shows commits that exist upstream but not in your local branch. See what you're about to pull before you pull it.

Both use `--graph` for a visual representation and `--abbrev-commit` for shorter, readable commit hashes.

Example output:
```bash
$ git out
* a3f4b21 Add feature X
* 2c8d9f3 Fix typo in docs

$ git in
* 7e9a1c4 Merge pull request #42
* 5b3f8d2 Update dependencies
```

### Cherry-Pick Shortcut

```ini
    cher = cherry-pick
```

**`git cher`**: Cherry-picking is common enough to deserve a shorter alias. Copy specific commits from one branch to another without the full merge.

### Patch Application

```ini
    ap = apply --ignore-space-change --ignore-whitespace
```

**`git ap`**: Apply patches while ignoring whitespace differences. Crucial when working with patches from different editors or coding standards.

### Branch Creation and Push

```ini
    branchup = "!f() { git checkout -b \"$1\" && git push -u origin \"$1\"; }; f"
```

**`git branchup feature-name`**: This one's a real workflow enhancer. It creates a new branch AND pushes it to origin with upstream tracking in one command.

Usage:
```bash
git branchup feature-authentication
# Creates local branch 'feature-authentication'
# Pushes it to origin
# Sets up tracking so future pushes/pulls just work
```

No more "fatal: The current branch has no upstream branch" messages.

## Editor and Merge Tool Configuration

```ini
[core]
    editor = emacs

[merge]
    tool = kdiff3

[diff]
    tool = kdiff3
```

I use Emacs as my default git editor (for commit messages and interactive rebases) and KDiff3 for resolving merge conflicts. KDiff3 provides a three-way merge view that makes conflict resolution much more manageable than trying to decipher git's conflict markers in a text editor.

## Practical Workflows

Here's how these configurations come together in daily use:

### Starting a New Feature
```bash
gopro                                    # Jump to projects directory
cd my-project                           # Enter project
git branchup feature-cool-thing         # Create and push new branch
# ... make changes ...
git s                                   # Check status
git com -m "Implement cool thing"       # Commit
git out                                 # Verify what will be pushed
git push                                # Push to remote
```

### Syncing with Upstream
```bash
git f                                   # Fetch from all remotes
git in                                  # See what's new upstream
git pull                                # Pull the changes
```

### Applying Patches from Email/PR
```bash
git co -b review-patch                  # Create review branch
git ap patch-file.patch                 # Apply patch ignoring whitespace
git s                                   # Verify changes
```

## Why This Setup Works

This configuration evolved over years of development work. The key principles:

1. **Reduce Friction**: Every alias saves seconds, but those seconds add up. When you're running git commands hundreds of times a day, saving a few keystrokes matters.

2. **Visual Feedback**: Git prompt integration and aliases like `git in`/`git out` give you situational awareness without running multiple commands.

3. **Safety Rails**: Seeing what you're about to push (`git out`) or pull (`git in`) before doing it prevents mistakes.

4. **Consistency**: Having a standard directory structure and predictable aliases means less cognitive load. You can focus on solving problems, not remembering where things are.

## Getting Started

Want to try this setup? Here's how:

### 1. Directory Structure
```bash
mkdir -p ~/dev/projects ~/dev/utils
```

### 2. Add Bash Aliases
Add these to your `~/.bashrc`:
```bash
export HISTSIZE=100000
export HISTFILESIZE=100000
alias gopro='cd ~/dev/projects'
alias myip='dig +short txt ch whoami.cloudflare @1.0.0.1'
```

### 3. Configure Git Aliases
Run these commands or add them to `~/.gitconfig`:
```bash
git config --global alias.s status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.com commit
git config --global alias.f "fetch --all"
git config --global alias.r "remote -v"
git config --global alias.cher cherry-pick
git config --global alias.out "log --pretty=oneline --abbrev-commit --graph @{u}.."
git config --global alias.in "!git fetch && git log --pretty=oneline --abbrev-commit --graph ..@{u}"
git config --global alias.ap "apply --ignore-space-change --ignore-whitespace"
git config --global alias.branchup '!f() { git checkout -b "$1" && git push -u origin "$1"; }; f'
```

### 4. Optional: Install bash-git-prompt
```bash
cd ~/dev/projects
git clone https://github.com/magicmonty/bash-git-prompt.git
```

Add to `~/.bashrc`:
```bash
GIT_PROMPT_THEME=Solarized_UserHost
source ~/dev/projects/bash-git-prompt/gitprompt.sh
```

Reload your shell:
```bash
source ~/.bashrc
```

---

## Conclusion

Your development environment should work for you, not against you. This setup gives me fast navigation, powerful git shortcuts, and the visibility I need to work confidently.

The best part? It's portable. These configurations live in dotfiles that I can deploy to any new machine in minutes. Whether I'm on my workstation, a cloud VM, or a fresh Linux install, my environment feels like home.

Try these configurations, adapt them to your workflow, and you'll wonder how you ever lived without them.

**Back to the HACKS!**

All the code for this blog is available on GitHub [here](https://github.com/cyberoblivion/co-blog)

{% include comments.html %}
