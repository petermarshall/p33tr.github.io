---
layout: post
title: How to replace text in windows
categories: [code, github actions powershell]
---


When deploying a file to a server I need to replace some text with a differnt text. The code is deployed using github actions. There are pre-defined actiosn but they are all made to run under linux. My build process is runing in a windows virtual nachine. So how do I do it on a windows virtual machine?

Powershell is available, and we can use a powershell command.

We can read the file content, replace the text and pipe it back to the same file as output.

'''
$ powershell -Command "(get-content text.txt) -replace 'test', 'xxx' | Out-File -encoding ASCII text.txt"
'''

No need to use a github action.




