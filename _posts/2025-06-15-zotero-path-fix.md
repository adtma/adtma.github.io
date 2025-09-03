---
title: 'Fixing Zotero Path Issue'
date: 2025-06-15
categories:
  - blog
tags:
  - zotero
  - python
  - Zotfile
  - Zotmoov
---
One of the best decisions I made during grad school was learning to use Zotero early in my Master's program. This game-changing application has saved me hours, probably days, of time organizing bibliographies for my papers. Zotero will always be the first application I install on any new computer or laptop. I first discovered it through [one of my professors](https://dasaptaerwin.notion.site/DASAPTA-ERWIN-SHORT-BIO-600a09a192c344609d685064ad014674).

Recently, my PI gave me an iMac for my office at EOAS FSU. I installed all my usual software, including Zotero. But shortly after finishing the setup, I discovered a problem with my installation. Beyond managing citations and creating bibliographies, I use Zotero to organize PDF files of scientific papers across my computers. I sync this folder through OneDrive between my personal laptop and work machines, using an add-on called Zotfile to manage PDFs within Zotero. The latest version of Zotero I installed on the work iMac, however, no longer supports Zotfile. This meant I couldn't access any of the PDF files stored in my Zotero library.

After searching for solutions online, I found that most Zotero users have switched to [Zotmoov](https://github.com/wileyyugioh/zotmoov) as an alternative to Zotfile. Unfortunately, after installing Zotmoov, my PDFs remained inaccessible. Digging through various articles online, I discovered the issue was related to how Zotfile stores PDF file paths. For reasons I still don't fully understand, my Zotfile installation used absolute paths with Windows directory formatting (like `C:\Users\datua\OneDrive\Zotmoov\Young\2009\Young et al. - 2009 - A major drop in seawater 87Sr86Sr during the Middle Ordovician (Darriwilian) Links to volcanism an.pdf`), making them unrecognizable on macOS.

There's a manual fix for this, but it's incredibly tedious. It involves manually deleting each broken link and relinking every PDF file individually.

I knew there had to be a way to automate this process. After digging through posts on the Zotero forum, I learned that attachment link paths are stored in a SQLite database within the Zotero data directory. The fact that these paths live in a database meant I could automatically modify them. I should be able to write a Python script for this, but since I didn't really have time to work on it properly, I decided to get some help from an LLM.

I use Claude for tasks like this. I described my goal, provided examples, and outlined detailed implementation steps in my prompts. After a couple of minor tweaks, it produced a Python script that works flawlessly.

If you're dealing with similar issues, I've shared the script [here](https://github.com/datuadiatma/fix_zotero_path). There is no additional library that you need to install, as the script only uses the standard libraries. Once you run the script it will ask you to (1) Preview, (2) do a test run, or (3) change all broken paths. I recommend doing (1) first, see if the new paths look ok, do a test run in which the script change only two broken paths. Check if it works and do (3) when you are happy with the results.

```bash
(base) Datus-iMac:Zotero datuadiatma$ python fix_zotero_path.py 
Zotero Attachment Path Fixer
==============================
1. Preview changes (see what would be updated)
2. Test mode (fix only 2 attachments)
3. Fix all attachment paths

Select option (1, 2, or 3):
```

If you choose preview it will show you something that looks like this.

```bash
ID 1181: C:\Users\datua\OneDrive\Zotmoov\Mills\2017\Mills et al. - 2017 - Elevated CO 2 degassing rates prevented the return of Snowball Earth during the Phanerozoic.pdf -> /Users/datuadiatma/Library/CloudStorage/OneDrive-Personal/Zotmoov/Mills/2017/Mills et al. - 2017 - Elevated CO 2 degassing rates prevented the return of Snowball Earth during the Phanerozoic.pdf
ID 1182: C:\Users\datua\OneDrive\Zotmoov\Pogge von Strandmann\2017\Pogge von Strandmann et al. - 2017 - Lithium isotope behaviour during weathering in the Ganges Alluvial Plain.pdf -> /Users/datuadiatma/Library/CloudStorage/OneDrive-Personal/Zotmoov/Pogge von Strandmann/2017/Pogge von Strandmann et al. - 2017 - Lithium isotope behaviour during weathering in the Ganges Alluvial Plain.pdf
ID 1183: C:\Users\datua\OneDrive\Zotmoov\Penniston-Dorland\2017\Penniston-Dorland et al. - 2017 - Lithium Isotope Geochemistry.pdf -> /Users/datuadiatma/Library/CloudStorage/OneDrive-Personal/Zotmoov/Penniston-Dorland/2017/Penniston-Dorland et al. - 2017 - Lithium Isotope Geochemistry.pdf
```

Once you are happy with how they look, re-run the script choose option (2), test it, and choose (3) to apply the fix to all of your pdf attachement.

Overall, I am really happy with the results of the script. It only took me a couple of hours to troubleshoot the issue and come up with a solution. I hope this is helpful. Let me know if you encounter similar problems or have any questions.