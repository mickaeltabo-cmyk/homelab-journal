# Interview bank

### Q1. How does SSH key-based authentication work, and why is it better than passwords?

SSH key authentication uses a key pair. The public key is stored on the server in authorized_keys, and the private key stays on my machine, protected by a passphrase. When I connect, my client proves it owns the private key by signing data from the session, so no secret ever travels over the network. It is better than a password because it can't be guessed by brute force, I can revoke one person's access by deleting one line, and tools like Ansible or CI pipelines can connect without anyone typing a password. Once keys work, I disable password login on the server.

### Q2. You get "WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED". What does it mean, and what do you do?

This warning means the server now shows a different host key from the one saved in my known_hosts file. Most of the time it's harmless: the server was rebuilt, or a new cloud instance got the same IP address. But it can also be a man-in-the-middle attack, so I never just delete the line. First I check the new fingerprint through another channel, for example the cloud console or the team that rebuilt the machine. Only when it matches do I remove the old entry with ssh-keygen -R and reconnect.

### Q3. What's the difference between a hard link and a symbolic link? Give a production use case.

A hard link is a second name for the same inode, so if I delete the original name, the data is still there. A hard link can't point to a directory or cross filesystems. A symbolic link is a small file that contains a path. It can point anywhere, but if I delete the target, it becomes a dangling link and breaks. In production, Nginx enables a site by linking sites-available to sites-enabled. Many deployments also keep a current symlink pointing to the active release, so a rollback is one command: ln -sfn pointing current to the previous version.

### Q4. Explain the difference between git add, git commit and git push.

Git has three local areas: the working tree with my files, the staging area, and the local repository. git add moves chosen changes to the staging area, so I build a clean commit. git commit saves a snapshot of what is staged in my local history, with a message and a unique hash, and it works offline. git push sends those commits to a remote like GitHub, so the team can see them. Before git add, I always check git status and git diff, because a secret pushed to a public repo must be considered leaked and rotated.

## Q5. What are stdin, stdout and stderr? What does 2>&1 do, and why does the order matter?
Every process has three streams: stdin (0) for input, stdout (1) for normal output and stderr (2) for errors. I can redirect them separately, for example results to a file and errors to /dev/null. 2>&1 sends stderr wherever stdout points at that moment, so > file 2>&1 captures both, while 2>&1 > file still prints the errors on screen. In my lab I wrote a one-line health check for ssh.socket that appends OK or ALERTE to a log and prints nothing, using >/dev/null 2>&1.

## Q6. Explain Linux file permissions. What do 755, 644 and 2775 mean?
Each file has an owner, a group and others, with read (4), write (2) and execute (1). 755 is full access for the owner and read-execute for everyone else; 644 is read-write for the owner and read-only for the others; 2775 is a team directory, where the leading 2 is setgid, so new files inherit the directory's group. On a directory, execute means you can enter it. In my lab, a teammate could not edit a file in a shared folder because setgid had been removed and the file got the wrong group; I restored setgid and fixed the file's group.

## Q7. How would you give a user limited admin rights on a Linux server?
I use sudo with least privilege: a group for the role, for example oncall, and a rule in /etc/sudoers.d, written with visudo, that allows only the exact command, such as restarting one service. I check it with visudo -c and sudo -l -U, and every attempt is logged in auth.log. Since access comes from group membership, revoking it is a single command. In my lab, a user lost this access because usermod -G without -a replaced all his groups; I found it with sudo -l -U and auth.log.

## Q8. A command works in your terminal but fails with "command not found" in a script, a cron job or with sudo. Why?
The shell looks for commands in the directories listed in PATH. A script, a cron job or sudo does not use my interactive PATH: .bashrc is only read by interactive shells, and sudo uses its own secure_path. To diagnose it, I compare echo $PATH and type -a in the failing context. In automation I use absolute paths or set PATH explicitly. In my lab, a line in .bashrc replaced a user's whole PATH, so even ls failed; I found it with /usr/bin/grep and fixed it with PATH="$HOME/bin:$PATH".
