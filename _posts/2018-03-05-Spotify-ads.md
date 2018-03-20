---
layout: post
title: Disable spotify ads
description: "Do you hate spotify ads as much as me?"
modified: 2016-12-02
tags: [sample post]
categories: [intro]
image:
    feature: P_spotify/logo.jpg
---
Spotify is coded in such a way, that it will keep playing music if it can't gaing access to an ad. This script redirects all Spotify's requests 
to the address `0.0.0.0` at which there is no ad resources located; thus Spotify will never be able to reach and play an ad.
<br>That's all there is it to. Follow the instructions and enjoy the music! :musical_note::musical_note::musical_note:

<div markdown="0"><a href="https://gist.github.com/Sebastian-Nielsen/5480aa6c0775e7a4604ee34bdd17416c" class="btn btn-success">Get the code</a></div>


![Smithsonian Image]({{ site.url }}/images/P_spotify/file.PNG)
{: .image-right}

# Instructions
- Copy the code from the gist and paste it into a bat file. <br/>
  *(A bat file is a file with the file extension .bat)*
- Run it

\<code snippet\>

	@echo 
	title Spotify-noad
	:: BatchGotAdmin
	:-------------------------------------
	REM  --> Check for permissions
	   IF "%PROCESSOR_ARCHITECTURE%" EQU "amd64" (
	>nul 2>&1 "%SYSTEMROOT%\SysWOW64\cacls.exe" "%SYSTEMROOT%\SysWOW64\config\system"
	) ELSE (
	>nul 2>&1 "%SYSTEMROOT%\system32\cacls.exe" "%SYSTEMROOT%\system32\config\system"
	)
