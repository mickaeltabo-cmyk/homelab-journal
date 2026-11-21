# Module 03 — Processes, services, logs and packages
- 2026-11-16 · S1 · Learned: tracing a CPU hog from uptime and ps to its parent (pstree) and its origin in auth.log, then stopping it with TERM via sudo · Stuck on: remembering the ps options, so I keep one template and adapt it
- 2026-11-16 · S2 · Learned: writing and debugging a systemd unit (dedicated user, absolute ExecStart path, Restart=on-failure, enable vs start, daemon-reload) and hardening it with a drop-in, from 9.2 UNSAFE to 6.2 MEDIUM with systemd-analyze security · Stuck on: how units and drop-ins fit together, and what each hardening option really forbids
### Incident A - wrong page, directory listing
- Symptom: the status page showed a directory listing, and the service kept restarting (Restart=on-failure).
- Key command: sudo ss -tlnp | grep 8080 found another python3 http.server holding the port; the service log said Address already in use.
- Fix: stopped the intruder with sudo kill (TERM); statuspage came back on its own and curl returned the page.
### Incident B - 404 while the service is green
- Symptom: curl -i returned 404 No permission to list directory, although statuspage was active and index.html was intact.
- Key command: ls -ld /srv/statuspage showed drwxrws--- root deploy, and sudo -u statuspage ls confirmed Permission denied.
- Fix: chmod 2775 so the service can read and the deploy group can still write; curl returned 200 and tamar could still create files.
### Incident C - service stopped every night, no error
- Symptom: statuspage was inactive (dead) with no error and systemd did not restart it.
- Key command: journalctl around the exact stop time, without -u, showed logrotate running and statuspage killed by signal HUP.
- Fix: python does not handle HUP, so I removed the postrotate HUP and used copytruncate; a forced logrotate -f left the service active and logging.
- 2026-11-21 · S4 · Learned: going from a symptom to a proven cause with status, journal, ss, ls -ld and sudo -u · Stuck on: ...
