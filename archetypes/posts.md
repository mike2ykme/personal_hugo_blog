---
title: "{{ replace .Name "-" " " | title }}"
sub_title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
author: "{{ .Site.Params.Author }}"
image: "{{ "/images/woods.jpg" | relURL }}"
alt_text: "{{ replace .Name "-" "" | title }} screenshot"
year: "{{ dateFormat "2006" .Date }}"
month: "{{ dateFormat "2006/01" .Date }}"
draft: false
categories:
- Personal
- Thoughts
tags:
- software
- html
---

Lorem ipsum dolor sit amet, consectetur adipisicing elit. Impedit natus sint debitis corporis nobis perspiciatis tempore a dolorum animi cum, modi voluptatibus. Earum, explicabo velit?

<!--more-->

Lorem ipsum dolor sit amet, consectetur adipisicing elit. Impedit natus sint debitis corporis nobis perspiciatis tempore a dolorum animi cum, modi voluptatibus. Earum, explicabo velit?Lorem ipsum dolor sit amet, consectetur adipisicing elit. Impedit natus sint debitis corporis nobis perspiciatis tempore a dolorum animi cum, modi voluptatibus. Earum, explicabo velit?
