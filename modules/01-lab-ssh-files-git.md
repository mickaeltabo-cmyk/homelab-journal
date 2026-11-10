# Module 01 — Lab, SSH, files and Git:

## What I did
- Session 1 (SSH): connected from Windows 11 to lab-01 with an Ed25519 key (`ssh lab-01`) and read the negotiation with `ssh -v`.
- Session 2 (files and logs): located configs (`/etc`), logs (`/var/log`) and kernel data (`/proc`); followed and filtered logs with `tail -f` and `grep`; found files with `find` by name, type, age and size.
- Session 3 (links and Git): compared hard links and symlinks, switched a release with `ln -sfn`; published this repo from lab-01 over SSH with my noreply email, and protected secrets with `.gitignore`.
- Session 4 (incidents): solved three incidents by proving the cause, and prepared four interview answers.

## Inventory
### lab-01 inventory

mickael@lab-01:~$ cat /etc/hostname
lab-01
mickael@lab-01:~$ cat /etc/
mickael@lab-01:~$ cat /etc/os-release
PRETTY_NAME="Ubuntu 24.04.5 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
VERSION="24.04.5 LTS (Noble Numbat)"
VERSION_CODENAME=noble
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=noble
LOGO=ubuntu-logo
mickael@lab-01:~$ cat /proc/meminfo
MemTotal:        3960884 kB
MemFree:         3168084 kB
MemAvailable:    3426328 kB
Buffers:          105788 kB
Cached:           312172 kB
SwapCached:            0 kB
Active:           313932 kB
Inactive:         150104 kB
Active(anon):      56344 kB
Inactive(anon):        0 kB
Active(file):     257588 kB
Inactive(file):   150104 kB
Unevictable:       27456 kB
Mlocked:           27456 kB
SwapTotal:       3960828 kB
SwapFree:        3960828 kB
Zswap:                 0 kB
Zswapped:              0 kB
Dirty:                 4 kB
Writeback:             0 kB
AnonPages:         73436 kB
Mapped:            88792 kB
Shmem:              1508 kB
KReclaimable:     109764 kB
Slab:             189544 kB
SReclaimable:     109764 kB
SUnreclaim:        79780 kB
KernelStack:        5888 kB
PageTables:         2720 kB
SecPageTables:         0 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:     5941268 kB
Committed_AS:     368968 kB
VmallocTotal:   34359738367 kB
VmallocUsed:       24168 kB
VmallocChunk:          0 kB
Percpu:            69120 kB
HardwareCorrupted:     0 kB
AnonHugePages:         0 kB
ShmemHugePages:        0 kB
ShmemPmdMapped:        0 kB
FileHugePages:         0 kB
FilePmdMapped:         0 kB
Unaccepted:            0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
Hugetlb:               0 kB
DirectMap4k:      134976 kB
DirectMap2M:     3010560 kB
DirectMap1G:     3145728 kB

### Security report
Pour la mission 2 voici ce que j'ai fais :

pour la question de combien de connexions SSH acceptées et combien d'échecs dans le auth.log actuel, depuis quelles adresses

j'ai fais un 'grep "Accepted" /var/log/auth.log ce qui m'a donne ceci :

mickael@lab-01:~$ grep "Accepted" /var/log/auth.log
2026-11-05T10:40:37.416932+00:00 lab-01 sshd[1506]: Accepted password for mickael from 192.168.83.1 port 51949 ssh2
2026-11-05T10:58:18.412075+00:00 lab-01 sshd[1996]: Accepted password for mickael from 192.168.83.1 port 61643 ssh2
2026-11-05T11:01:30.379927+00:00 lab-01 sshd[2096]: Accepted publickey for mickael from 192.168.83.1 port 49852 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg
2026-11-05T11:01:54.351892+00:00 lab-01 sshd[2163]: Accepted publickey for mickael from 192.168.83.1 port 63401 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg
2026-11-05T11:03:10.832483+00:00 lab-01 sshd[2221]: Accepted publickey for mickael from 192.168.83.1 port 54902 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg
2026-11-05T11:04:28.071924+00:00 lab-01 sshd[2289]: Accepted publickey for mickael from 192.168.83.1 port 59093 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg
2026-11-05T11:08:46.449135+00:00 lab-01 sshd[2409]: Accepted publickey for mickael from 192.168.83.1 port 56214 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg
2026-11-05T11:10:26.761025+00:00 lab-01 sshd[2482]: Accepted publickey for mickael from 192.168.83.1 port 58731 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg
2026-11-05T18:13:36.739930+00:00 lab-01 sshd[31911]: Accepted publickey for mickael from 192.168.83.1 port 59220 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg
2026-11-05T18:29:36.159682+00:00 lab-01 sshd[32056]: Accepted publickey for mickael from 192.168.83.1 port 51270 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg
2026-11-05T18:31:53.422801+00:00 lab-01 sshd[32122]: Accepted publickey for mickael from 192.168.83.1 port 54034 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg
2026-11-06T08:13:03.799012+00:00 lab-01 sshd[1356]: Accepted publickey for mickael from 192.168.83.1 port 52668 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg
2026-11-06T08:22:38.530921+00:00 lab-01 sshd[1545]: Accepted publickey for mickael from 192.168.83.1 port 54641 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg
2026-11-06T08:23:06.883710+00:00 lab-01 sshd[1617]: Accepted publickey for mickael from 192.168.83.1 port 54645 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg
2026-11-07T19:22:28.682489+00:00 lab-01 sshd[1294]: Accepted publickey for mickael from 192.168.83.1 port 65272 ssh2: ED25519 SHA256:cuaTKPAdC7mA6+zlaSQCQRw0ilS4BKvF45V6cp8fSFg

donc on compte 15 connexions ssh acceptées et ensuite pour les echecs j'ai fais un 'grep "Failed" /var/log/auth.log
ce qui m'a donne ceci :

mickael@lab-01:~$ grep "Failed" /var/log/auth.log
2026-11-07T19:31:14.422765+00:00 lab-01 sudo:  mickael : TTY=pts/0 ; PWD=/home/mickael/homelab-journal/modules ; USER=root ; COMMAND=/usr/bin/grep Failed /var/log/auth.log
2026-11-07T19:31:29.323037+00:00 lab-01 sshd[1541]: Failed password for invalid user toto from 192.168.83.1 port 53955 ssh2

c'est moi qui ai fais expres de faire un echec avec toto sinon il ny en avait pas

ensuite pour le plus gros fichier de /var/log jai fais ceci :

ls -lSh /var/log

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
