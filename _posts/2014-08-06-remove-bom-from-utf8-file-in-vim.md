---
layout: post
title: Remove BOM from UTF8 file in vim
date: 2014-08-06 10:47:04
categories: []
tags: vim
---

The UTF-8 BOM (Byte Order Mark) is a sequence of bytes (EF BB BF) that allows the reader to identify a file as being encoded in UTF-8.

To remove BOM in vim, do `:set nobomb`

To add BOM - `:set bomb`

