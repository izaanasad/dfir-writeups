# Digital Forensics Case B4DM755 — TryHackMe

**Room:** [Digital Forensics Case B4DM755](https://tryhackme.com/room/caseb4dm755) · **Focus:** End-to-end forensic process, chain of custody, evidence acquisition · **Tools:** FTK Imager, EXIF/metadata analysis

> Answers and flags are not published. TryHackMe's Acceptable Use Policy (v2.0, Aug 2026) prohibits sharing answers for active rooms.

---

## Summary

A fictional criminal case built on Intro to Digital Forensics. A court has authorised a search of a suspect, and you follow the evidence from the scene to the courtroom: a first responder secures it, you acquire a forensic image in the lab, analyse the artefacts, and prepare them for presentation at trial.

## What I learned

### The legal frame comes first

- No search without a **search warrant**. Evidence gathered outside its scope risks being inadmissible.
- Every role in the process is defined. A first responder's job at the scene is different from a lab analyst's.

### Chain of custody is what makes evidence usable

- Artefacts are **bagged, sealed and tagged** before they leave the scene.
- Every transfer is documented: who handed it over, who received it, when and why. That **individual accountability** is what lets a court trust the evidence wasn't tampered with.
- Drives are checked for **encryption** before imaging, because pulling power or imaging the wrong way can lose access to encrypted data permanently.
- Analysis is only ever performed on a **copy**. The original is preserved, and **cryptographic hashes** prove the copy matches.

### FTK Imager in practice

- A hardware **write blocker** prevents the analysis machine from modifying the source device during acquisition.
- I learned FTK Imager's main panes: the **Evidence Tree** (hierarchical view of sources), the **File List** (contents of the selected folder) and the **Viewer** (contents of the selected file).
- I created a forensic image of a USB drive and **compared the hash of the physical drive with the hash of the image** to verify integrity.
- I recovered **deleted files** and checked them for corruption (for example, zero-byte recoveries).

### Analysis: files lie, metadata doesn't

- A file's visible extension isn't proof of what it is. **Checking file headers and metadata** revealed the true file type of a disguised file.
- **EXIF data** in photos identified the phone model that took them and, for one image, the **GPS coordinates** of a meeting. This tied a location and a time to the suspect.
- Password-protected archives, documents and source code in the recovered data pointed to involvement beyond the original allegation.

### The phases, mapped to the case

| Phase | In this case |
|---|---|
| Pre-search | Obtaining the warrant |
| Search & seizure | First responder secures the USB drive at the scene |
| Acquisition & analysis | Imaging, hashing, file recovery, metadata analysis in the lab |
| Reporting & presentation | Presenting artefacts with documentation in court, and re-verifying hashes at trial |

## Analyst takeaway

The technical work (imaging, recovery, EXIF) was the easy part. What makes it *evidence* is the paperwork around it: the warrant, the chain-of-custody log, and matching hashes at every hand-off. Even in corporate incident response, where nobody is going to court, I'd keep the same discipline, because you rarely know at the start of an investigation whether it'll end in HR action, a lawsuit or a police report.
