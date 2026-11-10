### 1.How does SSH key-based authentication work, and why is it better than passwords?

SSH key-based authentication relies on public-key cryptography: the public key is appended to the remote server's ~/.ssh/authorized_keys, while the private key remains secured on the local machine under a passphrase. 
During the handshake, the client generates a digital signature tied to the active session, which the server validates using the corresponding public key—ensuring sensitive credentials never cross the wire. 
Operationally, revoking access requires deleting just a single line, and automation workflows like CI jobs or Ansible playbooks can run non-interactively without passwords. 
Once key authentication is confirmed working, disabling password logins entirely eliminates brute-force exposure on port 22.

### 2.You get "WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED". What does it mean, and what do you do?

The SSH client records the host key fingerprint of every encountered system in known_hosts; this alert signifies that the remote target is presenting a different identity than recorded. 
Often, this is harmless—typically caused by a reprovisioned server or a cloud instance inheriting a recycled IP address. 
However, it can also point to an active man-in-the-middle attack, which is why the entry should never be wiped reflexively. 
The new fingerprint must first be verified out-of-band—such as through the provider's cloud console output or directly with the infrastructure team. 
Only after confirming its authenticity do I purge the stale record via ssh-keygen -R and reconnect

### 3.What's the difference between a hard link and a symbolic link? Give a production use case.

A hard link acts as an additional directory entry referencing an existing inode: file data remains intact on disk as long as at least one link persists, though hard links cannot span across filesystems or target directories. 
In contrast, a symbolic link is a distinct pointer file storing a target path; it can reference any arbitrary location, but becomes broken if the destination file is removed. 
In production environments, symlinks are standard practice—such as Nginx activating configurations by linking sites-available into sites-enabled.
Many continuous deployment strategies similarly maintain a current symlink pointed to the live release directory, enabling atomic releases and instant rollbacks simply by repointing that single link.

### 4.Explain the difference between git add, git commit and git push.


Locally, Git structures work across three distinct layers: the working tree containing live project files, the staging area (index), and the local repository. 
Using git add allows granular staging of changes to craft coherent, intentional commits. 
Running git commit then logs an offline snapshot of the staged state into local history, stamped with a unique SHA hash, author metadata, and a descriptive message. 
Finally, git push synchronizes these local commits to an upstream remote like GitHub, making them available to team members and CI/CD pipelines. 
Reviewing git status and git diff prior to pushing is an essential safeguard: once committed and pushed to a remote, any exposed credential must be treated as compromised and immediately rotated.
