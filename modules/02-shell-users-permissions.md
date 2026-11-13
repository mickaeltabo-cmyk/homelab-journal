- 2026-11-12 · S2 · Learned: PS1 and .bashrc make a setting permanent, and double vs single quotes · 
Stuck on: quoting inside the echo that writes PS1
- 2026-11-13 · S3 · Learned: usermod -aG (never -G alone), octal and symbolic permissions, setgid on a team folder, one-command sudo rule checked with visudo · Stuck on: closing /srv/deploy to other accounts without losing setgid (what r and x mean on a directory)
- sudo rule (ticket #209): `%oncall ALL=(root) /usr/bin/systemctl restart rsyslog`
- 2026-11-13 · S4 · Learned: proving a cause with a command or a log before fixing, PATH and .bashrc, setgid and group ownership, usermod -aG · Stuck on: finding the faulty line in .bashrc when grep itself was not found (use /usr/bin/grep)

## Incident A: "No command works" (PATH overwritten)
- Symptom: in Tamar's session, ls, vim and git returned "command not found".
- Key command: echo $PATH showed only /home/tamar/bin; /usr/bin/grep -n PATH ~/.bashrc found export PATH="$HOME/bin".
- Fix: edited ~/.bashrc with /usr/bin/vim to export PATH="$HOME/bin:$PATH"; checked in a new session with echo $PATH and ls.

## Incident B: "I cannot edit Dan's file" (setgid lost)
- Symptom: Tamar got "Permission denied" on a file created by Dan in /srv/deploy, although both are in deploy.
- Key command: ls -l /srv/deploy showed the wrong group on the file; ls -ld /srv/deploy showed no setgid (x instead of s).
- Fix: sudo chmod g+s /srv/deploy, then chgrp deploy and chmod g+w on the file; Tamar could then write to it.

## Incident C: "sudo refuses what worked yesterday" (group lost)
- Symptom: Dan got "not allowed to execute ... as root" for systemctl restart rsyslog.
- Key command: sudo -l -U dan and id dan showed he had lost the oncall group; grep usermod /var/log/auth.log showed the cause; auth.log also logged "user NOT in sudoers".
- Fix: sudo usermod -aG oncall dan (usermod -G without -a had replaced all his secondary groups); checked with sudo -l -U dan.
