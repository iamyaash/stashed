---
date: '2025-12-06T17:41:43+05:30'
draft: true
title: "GPG: Fix 'database_open waiting for lock'"
summary: "Shows 'database_open waiting for lock', which is blocking me from signing commits. GPG creates a temporaray lock file in ~/.gnupg/*.lock* (like pubring.db.lock) which prevent the database access."
tags: [ "GPG" ]
params:
    author: "Yashwanth Rathakrishnan"
    ShowToc: true
    TocOpen: true
    ShowReadingTime: true
    ShowCodeCopyButtons: true
    ShowBreadCrumbs: true
---
