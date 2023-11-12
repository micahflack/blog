---
layout: post
title: Mock Research Proposal
---

I recently finished my mock research proposal for one of my classes this semester, Computer and Cyber Science Research Design and Implementation (CSC-807-DT1). The goal of this assignment was to familiarize ourselves with the research process, and proposals, and to solidify any ideas for our dissertations; which, is ultimately the goal of the program. Despite this experience, I am no more or less of a writing fan than I was before.

Research proposal title:

- PrintShop: Assessing OS and Capabilities of Serial Print Devices

See the following links:

- [Paper](https://raw.githubusercontent.com/micahflack/DSU/master/CSC807/Homework3/main.pdf)
- [Slides](https://raw.githubusercontent.com/micahflack/DSU/master/CSC807/Homework3/Homework3-slides.pdf)
- [Presentation](https://youtu.be/dG9xN_p8wAc)
- [Class Git Repo](https://github.com/micahflack/DSU/tree/master)

If you are curious about learning how to use Latex, it was a requirement for this assignment. My feelings about the tool is mixed. Obviously, if you compare it to something like Google Docs or MS Word, there are bound to be some growing pains. But it does make the overall theme and structure of your papers more consistent.

The best thing about Latex is probably how it handles your references, figures, and any tables you have created. Typically, when you create a figure or a table you will attach a label to it. With this label you can use the following tag anywhere to reference it and Latex will handle the formatting and naming of what goes there. For instance, if you wanted to reference some table you made and it was called, "table:cool_devices". You would use the following tag to reference it, " Table \ref:{table:cool_devices}". When the paper is generated/built, it will place the actual table number instead - "Table 1.1"; the example assumes table:cool_devices is the first table of chapter 1.

If you want to use Latex offline, and not something like Overleaf, this is what I used:

- Zotero (citation manager)
- VSCode (LaTex editor)
- LaTex Workshop (extension for VSCode)
- Zotero LaTeX (extension for VSCode)
- Zotero: Better Bibtex (zotero extension)
- Github (version control)

On Linux, you might need to install the texlive-full package. TexStudio is another good offline editor for LaTex. Very powerful, but very confusing. There was too much to adjust to and I was already used to using VSCode.