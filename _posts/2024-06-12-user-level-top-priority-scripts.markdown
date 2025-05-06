---
layout: post
title:  "User-level top-priority scripts"
date:   2024-06-12 20:11:03 -0600
categories: snippet
tags: bin bash linux script sh
---
Recommended: Set-up your own **$HOME/bin/** for user-level top-priority scripts
```bash
mkdir ~/bin
```
```bash
echo "export PATH=$HOME/bin:$PATH" >> ~/.bashrc
```
