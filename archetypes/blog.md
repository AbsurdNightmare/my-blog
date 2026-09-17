+++
date = '{{ .Date }}'
draft = true
title = '{{ replace .File.ContentBaseName "-" " " | title }}'
# 摘要：列表页显示在标题下方的那段话
summary = ''
# 标签，可以有多个；标签名建议用中文或英文短词
tags = []
# 系列：同名系列的文章会在文末互相推荐。不需要就整行删掉
# series = []
# 设为 true 会在首页「推荐阅读」里出现
# featured = false
+++

在这里写正文。
