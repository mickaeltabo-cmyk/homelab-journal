# Module 01 — Lab, SSH, files and Git

## What I did
- Session 1 (SSH): connected from Windows 11 to lab-01 with an Ed25519 key (`ssh lab-01`) and read the negotiation with `ssh -v`.
- Session 2 (files and logs): located configs (`/etc`), logs (`/var/log`) and kernel data (`/proc`); followed and filtered logs with `tail -f` and `grep`; found files with `find` by name, type, age and size.
- Session 3 (links and Git): compared hard links and symlinks, switched a release with `ln -sfn`; published this repo from lab-01 over SSH with my noreply email, and protected secrets with `.gitignore`.
- Session 4 (incidents): solved three incidents by proving the cause, and prepared four interview answers.

## Inventory
- Host: lab-01, Ubuntu 24.04.5 LTS (noble), see `/etc/hostname` and `/etc/os-release`
- Config: `/etc`
- Logs: `/var/log` (`auth.log`, `syslog`)
- Programs: `/usr/bin`
- Home: `/home/mickael`
- Memory: `/proc/meminfo` (about 3.8 GiB total, watch `MemAvailable`)

## Security report
Data from `/var/log/auth.log`, checked on 2026-11-07.

- **Accepted SSH logins: 15** (`grep "Accepted" /var/log/auth.log`). 2 used a password (the first two, on 5 Nov) and 13 used the Ed25519 key.
- **Failed SSH logins: 1** (`grep "Failed" /var/log/auth.log`): `Failed password for invalid user toto`. I triggered it myself to test. The second line returned by this grep is only my own `sudo grep` command logged by sudo, not a failed login.
- **Source addresses:** all connections come from `192.168.83.1` (my Windows PC, through the VMware network). No unknown address.
- **Biggest file in `/var/log`:** `<FILE>`, `<SIZE>` (`ls -lSh /var/log`, sorted by size).

## Incidents

### A — SSH password fallback
- Symptom: `ssh lab-01` prompted for the Linux password instead of the key passphrase.
- Key command: `sudo tail -f /var/log/auth.log` showed `Authentication refused: bad ownership or modes for file /home/mickael/.ssh/authorized_keys`.
- Fix: `authorized_keys` was 666 and sshd (StrictModes) ignores a writable key file; `chmod 600 ~/.ssh/authorized_keys`, then `ssh -v lab-01` shows `using "publickey"`.

### B — Broken symlink after deployment
- Symptom: app failed with `cat: .../current/config.ini: No such file or directory`.
- Key command: `ls -l ~/lab/pannes/app/` showed `current` pointing to the non-existent `releases/v2` (dangling symlink); `find ~/lab/pannes -xtype l` listed it.
- Fix: `ln -sfn ~/lab/pannes/app/releases/v1 ~/lab/pannes/app/current`; `cat current/config.ini` returns `port=8080` and `find` now lists nothing.

### C — GitHub rejects my password
- Symptom: `git push` asks for a username and password for https://github.com and the password is refused, although I can log in on the website.
- Key command: `git remote -v` showed an HTTPS remote URL; `ssh -T git@github.com` replied `Hi mickaeltabo-cmyk! You've successfully authenticated`, so the SSH key was fine.
- Fix: GitHub no longer accepts the account password for Git over HTTPS; `git remote set-url origin git@github.com:mickaeltabo-cmyk/homelab-journal.git`, `git remote -v` now shows `git@github.com`, then `git push` and the commit "Test incident C" appears on GitHub.
