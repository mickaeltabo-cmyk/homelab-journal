# Interview bank

### Q1. How does SSH key-based authentication work, and why is it better than passwords?

SSH key authentication uses a key pair. The public key is stored on the server in authorized_keys, and the private key stays on my machine, protected by a passphrase. When I connect, my client proves it owns the private key by signing data from the session, so no secret ever travels over the network. It is better than a password because it can't be guessed by brute force, I can revoke one person's access by deleting one line, and tools like Ansible or CI pipelines can connect without anyone typing a password. Once keys work, I disable password login on the server.

### Q2. You get "WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED". What does it mean, and what do you do?

This warning means the server now shows a different host key from the one saved in my known_hosts file. Most of the time it's harmless: the server was rebuilt, or a new cloud instance got the same IP address. But it can also be a man-in-the-middle attack, so I never just delete the line. First I check the new fingerprint through another channel, for example the cloud console or the team that rebuilt the machine. Only when it matches do I remove the old entry with ssh-keygen -R and reconnect.

### Q3. What's the difference between a hard link and a symbolic link? Give a production use case.

A hard link is a second name for the same inode, so if I delete the original name, the data is still there. A hard link can't point to a directory or cross filesystems. A symbolic link is a small file that contains a path. It can point anywhere, but if I delete the target, it becomes a dangling link and breaks. In production, Nginx enables a site by linking sites-available to sites-enabled. Many deployments also keep a current symlink pointing to the active release, so a rollback is one command: ln -sfn pointing current to the previous version.

### Q4. Explain the difference between git add, git commit and git push.

Git has three local areas: the working tree with my files, the staging area, and the local repository. git add moves chosen changes to the staging area, so I build a clean commit. git commit saves a snapshot of what is staged in my local history, with a message and a unique hash, and it works offline. git push sends those commits to a remote like GitHub, so the team can see them. Before git add, I always check git status and git diff, because a secret pushed to a public repo must be considered leaked and rotated.
