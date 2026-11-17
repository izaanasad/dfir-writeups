# Forensic Imaging — TryHackMe

**Room:** [Forensic Imaging](https://tryhackme.com/room/forensicimaging) · **Focus:** Creating, verifying and mounting disk images on Linux · **Tools:** `lsblk`, `dc3dd`/`dd`, `md5sum`/`sha256sum`, `mount`, `losetup`

> Answers and flags are not published. TryHackMe's Acceptable Use Policy (v2.0, Aug 2026) prohibits sharing answers for active rooms.

---

## Summary

Forensic imaging creates an exact bit-for-bit copy of storage media, including deleted files, slack and unallocated space, so analysis never touches the original. The room covers why imaging matters, how to image a device from the Linux command line, how to prove the image is identical with hashes, other imaging types (memory, virtual machines, remote), and how to mount an image read-only to examine it.

## What I learned

- **A forensic image isn't a file copy.** Copying files misses deleted data and unallocated space and changes access timestamps. A bit-for-bit image captures everything and preserves the original state.
- **Integrity is proven by hashes, not trust.** Hash the source, hash the image, and the two must match. The hash is recorded in the chain-of-custody documentation and re-checked whenever the evidence changes hands.
- **Document the session.** `history` (or `script`) records every command run during acquisition, which belongs in the case notes.
- **Imaging extends beyond disks.** Memory (volatile, so it's captured first), virtual machine disks and remote acquisition each need their own approach.
- **Mount read-only.** An image is attached as a loop device and mounted with `ro`, so that browsing it can't alter the evidence.

## Workflow I practised

```bash
lsblk                                            # identify the target block device
sudo dc3dd if=/dev/sdX of=case.img hash=md5 log=imaging.log   # image + hash + log in one step
md5sum /dev/sdX case.img                         # verify source and image match
sudo mkdir -p /mnt/evidence
sudo mount -o loop,ro case.img /mnt/evidence     # read-only examination
ls -la /mnt/evidence
sudo umount /mnt/evidence
```

In the practical exercise I imaged a 1 GB loop device, verified the image's hash, mounted it read-only and examined its contents.

## Analyst takeaway

The order matters: **identify → image → hash → verify → mount read-only → analyse**. Skipping the verification step is the most common way evidence loses its value, because once you can't prove the image matches the source, nothing you find in it holds up.
