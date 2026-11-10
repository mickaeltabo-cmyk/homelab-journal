### How does SSH key-based authentication work, and why is it better than passwords?

SSH uses a key pair: the public key goes on the server, and the private key stays on my machine. 
The client signs the session so no secrets cross the wire. 
It enables easy CI/CD automation and lets us disable password logins to stop brute-force attacks.

### You get "WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED". What does it mean, and what do you do?

known_hosts tracks server fingerprints. 
This alert means the host key changed—usually because a VM was rebuilt, but potentially a man-in-the-middle attack. 
I verify the fingerprint via the cloud console first, then clear the old entry with ssh-keygen -R and reconnect.

### What's the difference between a hard link and a symbolic link? Give a production use case.

A hard link is another name for the same data on disk. A symlink is just a pointer to a path. 
In production, symlinks are standard for enabling Nginx sites or pointing a current link to the active build for instant rollbacks.

### Explain the difference between git add, git commit and git push.

Git works in three local areas: working tree, staging, and local repo. git add stages specific changes, git commit snapshots them offline, and git push syncs them to GitHub. 
I always run git status first so I never push leaked secrets.
