---
layout: default
title: Installation
nav_order: 3
has_children: false
permalink: /docs/installation
---

# Installation

Ayakashi runs on [Node.js](https://nodejs.org/).  
If you don't have node installed in your system please go ahead and install it first.  
The latest LTS release is **recommended**.  
<br/>
Then install Ayakashi as a global module:

```bash
npm install -g ayakashi
```

The above command will install Ayakashi and make it available globally in your system.  
<br/>
More installation options will be available in the future.

## Updating

To update Ayakashi to the latest version, run:

```bash
npm update -g ayakashi
```

## Installing missing Chromium dependencies on a Linux server

If you try to run Ayakashi on a fresh linux server, you might get the following error:  
`Something went wrong Error: Can't launch Chrome!`.  
<br/>
That's because Ayakashi downloads and uses a portable version of Chromium, so some system dependencies might be missing.  

### Option 1 - Install the missing dependencies

<details style="cursor:pointer"><summary>Ubuntu/Debian</summary>
<p>

<div class="language-bash highlighter-rouge">
<div class="highlight">
{%- highlight bash -%}
sudo apt-get install gconf-service \
libasound2 \
libatk1.0-0 \
libatk-bridge2.0-0 \
libc6 \
libcairo2 \
libcups2 \
libdbus-1-3 \
libexpat1  \
libfontconfig1 \
libgcc1 \
libgconf-2-4 \
libgdk-pixbuf2.0-0 \
libglib2.0-0 \
libgtk-3-0 \
libnspr4 \
libpango-1.0-0 \
libpangocairo-1.0-0 \
libstdc++6 \
libx11-6 \
libx11-xcb1 \
libxcb1 \
libxcomposite1 \
libxcursor1 \
libxdamage1 \
libxext6 \
libxfixes3 \
libxi6 \
libxrandr2 \
libxrender1 \
libxss1 \
libxtst6 \
ca-certificates \
fonts-liberation \
libappindicator1 \
libnss3 \
lsb-release \
xdg-utils \
wget
{%- endhighlight -%}
</div>
</div>

</p>
</details>

<details style="cursor:pointer"><summary>CentOS</summary>
<p>

<div class="language-bash highlighter-rouge">
<div class="highlight">
{%- highlight bash -%}
sudo yum install pango.x86_64 \
libXcomposite.x86_64 \
libXcursor.x86_64 \
libXdamage.x86_64 \
libXext.x86_64 \
libXi.x86_64 \
libXtst.x86_64 \
cups-libs.x86_64 \
libXScrnSaver.x86_64 \
libXrandr.x86_64 \
GConf2.x86_64 \
alsa-lib.x86_64 \
atk.x86_64 \
gtk3.x86_64 \
ipa-gothic-fonts \
xorg-x11-fonts-100dpi \
xorg-x11-fonts-75dpi \
xorg-x11-utils \
xorg-x11-fonts-cyrillic \
xorg-x11-fonts-Type1 \
xorg-x11-fonts-misc
{%- endhighlight -%}
</div>
</div>
<br/>
And then also update the nss library
<div class="language-bash highlighter-rouge">
<div class="highlight">
{%- highlight bash -%}
sudo yum update nss -y
{%- endhighlight -%}
</div>
</div>
</p>
</details>

### Option 2 - Install Chromium from your package manager

You can also install chromium from your package manager.  
It's a bigger download than installing the dependencies individually but it's easier
and it will ensure everything is installed correctly.  

<div class="language-bash highlighter-rouge">
<div class="highlight">
{%- highlight bash -%}
sudo apt-get install chromium-browser
{%- endhighlight -%}
</div>
</div>

<script>
    const codeBlocks = Array.from(document.querySelectorAll(".highlight figure"));
    codeBlocks.forEach(function(block, i) {
        const parent = block.parentNode;
        const pre = document.createElement("pre");
        pre.className += "highlight";
        pre.appendChild(block.querySelector("code"));
        parent.appendChild(pre);
        parent.removeChild(block);
    });
</script>
