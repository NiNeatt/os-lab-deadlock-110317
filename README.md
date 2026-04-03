# os-lab-deadlock-110317

## Level 1: Virtual Vault Provisioning (Formatting & Mounting) 
<img width="1293" height="98" alt="Screenshot 2026-04-03 140454" src="https://github.com/user-attachments/assets/d5487494-582d-4c67-87cb-fc7950a5abb4" />
The output shows that /dev/loop38 and /dev/loop30 are mounted as loop devices with available space. This proves that both vault_alpha and vault_beta are successfully set up and ready to use.

---

## Level 2:  The Naive Sync (Introducing the Flaw)
<img width="1217" height="225" alt="Screenshot 2026-04-03 130107" src="https://github.com/user-attachments/assets/59e59801-02f4-4d35-ab74-dab54c22cc6c" />

---

## Level 3: The Local Circular Wait (Triggering Deadlock)
<img width="1863" height="195" alt="Screenshot 2026-04-03 141508" src="https://github.com/user-attachments/assets/c07de069-73b0-443d-adb6-76c1b6ff595a" />

<img width="1353" height="129" alt="Screenshot 2026-04-03 141636" src="https://github.com/user-attachments/assets/63c800f1-7828-44c1-9c80-877c569111ed" />

The scripts froze because each one locked a different vault first and then tried to lock the other.

- The sync_up script locked Vault Alpha and then waited for Vault Beta.

- The sync_down script locked Vault Beta and then waited for Vault Alpha.

Since both were holding one lock and waiting for the other, neither could finish. This situation is called a deadlock.

## Level 4: Site-to-Site Sync (Multiplayer Deadlock)
<img width="1919" height="159" alt="Screenshot 2026-04-03 150739" src="https://github.com/user-attachments/assets/53f4408a-5b26-4bc6-ab6f-093020274ccc" />

Both players ran their scripts at the same time. Player A locked their own Alpha vault and then tried to lock Player B’s Beta vault, while Player B locked their own Beta vault and then tried to lock Player A’s Alpha vault. Because each was holding one lock and waiting for the other, both froze.

---
## Level 5: Global Resource Ordering (The Patch)

<img width="1919" height="244" alt="Screenshot 2026-04-03 153155" src="https://github.com/user-attachments/assets/e514daf4-937b-4ff1-91e1-948fe39c6016" />

We changed the scripts so both always lock Alpha first, then Beta.
This stopped the circular wait because both followed the same order.
Player B just waited for Alpha, then continued safely after Player A finished.

---

## Level 6: Deadlock Recovery (The Timeout Patch) 

<img width="1500" height="169" alt="image" src="https://github.com/user-attachments/assets/f0660016-add7-4df9-9e6b-d79b7fd4cb73" />

We added a timeout so the script only waits 5 seconds for Alpha.
If the lock is busy too long, it aborts instead of freezing forever.
This keeps the server healthy by preventing infinite deadlocks and wasted memory

---

## Level 7: Safe Ejection (Teardown) 
<img width="1441" height="116" alt="Screenshot 2026-04-03 160355" src="https://github.com/user-attachments/assets/4da096d6-3d3b-4077-91af-c7c2cbf72e50" />
Proper teardown unmounts and deletes loop devices safely.
Without it, data can be corrupted and orphaned devices remain in the kernel.
This cleanup is critical for system stability and freeing resources.

---







