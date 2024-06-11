---
redirect_from: "/"
---

# Welcome to the Works With RISC-V Wiki

This is a community resource linked to the [Unofficial RISC-V Discord](https://discord.gg/CTdTs5RUMe). We are not officially part of RISC-V International, but we do follow the [RISC-V Code of Conduct](https://riscv.org/community-code-of-conduct/) and the [RISC-V Antitrust Compliance Policy and Checklist](https://riscv.org/antitrust/). Memebership in RISC-V International is not required to participate, and we are not bound by any of the RISC-V IP agreements, so everything included in this Wiki is public information. If you believe there is problem with the content of this wiki related to the above policies, please contact [edolnx](https://github.com/edolnx), who is the owner of this project, so that he can resolve your issue. [All of the content of this wiki is publicly available on GitHub](https://github.com/workswithriscv/wiki) and [Pull Requests](https://github.com/workswithriscv/wiki/pulls) are welcome to add or change content.

# Goals

The goal of this wiki is to provide information and resources for using RISC-V hardware and software. As such, we also have an artifact repository attached with various software and system images using a Wasabi object storage bucket fronted by the CloudFlare CDN. If you wish to add something to the repository please open a [GitHub issue](https://github.com/workswithriscv/wiki/issues) with a link to the content you would like and what page(s) you plan to link the content to. We can support pulling content from http(s), ftp, dropbox, MEGA, Baidu Cloud, Google Drive, and OneDrive.

# Organization of Content

This wiki is built atop the [git-wiki](https://www.drassil.org/git-wiki/main_page) project and published using GitHub Pages. We organize the content of this wiki based on the SoC for hardware, and the upstream projects for software. This can be somewhat confusing in certain cases where we make packages for a specific distrobution - the idea is to push changes upstream first and let the distros pull from there. However, some packages seem like they should be upstream but are instead managed by the distro (the Linux Kernel being the biggest example here), in those cases we are pushing towards the distro and not upstream. If you're confused, no problem ask for help. We're probably confused as well.