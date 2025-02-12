+++
date = '{{ .Date }}'
draft = true
title = '{{ replace .File.ContentBaseName "-" " " | title }}'
description = ""
author = "Steven Chang"
slug = "{{ .File.BaseFileName }}"
archive = ['{{ .Date | dateFormat "2006" }}']
tags = []
categories = []
cover = { image = '/posts/{{ .File.BaseFileName }}/{{ .Date | dateFormat "2006_01" }}/cover.png', alt = 'Cover Image'}
+++

## Title