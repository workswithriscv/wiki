---
redirect_from: "/"
---

# Welcome to the Works With RISC-V Wiki

This is a community resource linked to the [Unofficial RISC-V Discord](https://discord.gg/CTdTs5RUMe). We are not officially part of RISC-V International, but we do follow the [RISC-V Code of Conduct](https://riscv.org/community-code-of-conduct/) and the [RISC-V Antitrust Compliance Policy and Checklist](https://riscv.org/antitrust/). Memebership in RISC-V International is not required to participate, and we are not bound by any of the RISC-V IP agreements, so everything included in this Wiki is public information. If you believe there is problem with the content of this wiki related to the above policies, please contact [edolnx](https://github.com/edolnx), who is the owner of this project, so that he can resolve your issue. [All of the content of this wiki is publicly available on GitHub](https://github.com/workswithriscv/wiki) and [Pull Requests](https://github.com/workswithriscv/wiki/pulls) are welcome to add or change content.

# Goals

The goal of this wiki is to provide information and resources for using RISC-V hardware and software. As such, we also have an artifact repository attached with various software and system images using a Wasabi object storage bucket fronted by the CloudFlare CDN. If you wish to add something to the repository please open a [GitHub issue](https://github.com/workswithriscv/wiki/issues) with a link to the content you would like and what page(s) you plan to link the content to. We can support pulling content from http(s), ftp, dropbox, MEGA, Baidu Cloud, Google Drive, and OneDrive.

# Organization of Content

This wiki is built with the [git-wiki](https://www.drassil.org/git-wiki/main_page) project and published using GitHub Pages. Within the repo, you will see the pages are largely split into to major taxonomies: "hardware" and "software". 

## Hardware

For each piece of hardware, we organize by the SoC (aka the main chip). There is a folder for each chip, and pages for each chip within the folder. There is typically a `boards` file for each chip that shows and links to known boards for each chip. The specifications and operating system support is typically the same between all the boards so this makes sense.

For each piece of hardware, there is usually a BSP or Board Support Package which is the default software shipped on the board. There is also bootloader support for each board, typically Das U-Boot, and information for that is typically unique for each board (especially when a vendor fork is needed, which is very common for newly released SoCs). Both the vendor bootloaders and anything related to the BSP should be on the hardware pages instead of the software pages, as they are typically specific to one SoC or board.

## Software

This category is used for pieces of software that work across different SoCs. Examples are Generic Operating System Distributions (ex: Debian, Ubuntu, Alma) and applicationn which can be used across distros (ex: Firefox, Chromium, ffmpeg).

Software is a bit tricky compared to the hardware support pages. For example: most distros include a browser and/or ffmpeg, but they are out of date. So on the software page we keep track of major milestones for RISC-V support, and may offer specific builds for some distros. We may also offer vector-enabled builds for use on specific boards (since there is a lot of variance on vector support, but this is defined on each SoC page).

In the event of bugs in packages shipped in distros, we can offer builds with fixes and pages about them on the Software pages but we also need to push those bug reports upstream to the affected distro. In some cases, the the best bet may be to just use a different distro.

Another major component here would be GPU drivers. Many of the SoCs are all using Imagination GPUs which can support open source drivers. We will track if the necessary firmware blobs are available, status of the kernel components, support within Mesa (and hopefully newer packages), and support for Zink for enabling legacy OpenGL and GLES support on these Vulkan Only GPU implementations.