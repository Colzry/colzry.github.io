---
title: "{{ replace .Name "-" " " | title }}"
description: "{{ .Name }}"
keywords: "{{replace .Name "-" ","}}"

date: {{ .Date }}
lastmod: {{ .Date }}

categories:
  -
tags
  -
  -

author: Colzry
math: mathjax
mermaid: true
---

# {{ .Name }}
