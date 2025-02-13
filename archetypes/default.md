+++
date = '{{ .Date }}'
draft = true
title = '{{ replace .File.ContentBaseName "-" " " | title }}'
description = ""
author = 'Steven Chang'
slug = '{{ .File.BaseFileName }}'
archive = ['{{ .Date | dateFormat "2006" }}']
tags = []
categories = []
cover = { image = '/posts/{{ .Date | dateFormat "2006" }}/{{ .Date | dateFormat "01" }}/{{ .File.BaseFileName }}/cover.png', alt = 'Cover Image'}
+++

## Title