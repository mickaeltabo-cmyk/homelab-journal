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
- Key command: `git remote -v` showed an HTTPS remote URL; `ssh -T git@github.com` replied `Hi mickael-cmyk! You've successfully authenticated`, so the SSH key was fine.
- Fix: GitHub no longer accepts the account password for Git over HTTPS; `git remote set-url origin git@github.com:mickael-cmyk/homelab-journal.git`, `git remote -v` now shows `git@github.com`, then `git push` and the commit "Test incident C" appears on GitHub.
