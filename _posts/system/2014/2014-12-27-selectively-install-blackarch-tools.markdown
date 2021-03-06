---
layout: post
title:  "Selectively Install BlackArch Tools"
date:   2014-12-27 11:58:15 +0700
# published: false
categories: system
tags: [security, arch, package manager]
author: epsi

excerpt:
  With BlackArch you can install tools by category.
  One category today. And other category the day after.
  You can install only what you need. And keep our system clean.

related_link_ids: 
  - 14040246  # Arch Install
  - 16020803  # Update Arch no Bloated  
  - 14122608  # Unbundling AUR
  - 14042750  # BlackArch as Repository

---

Today Tips: Selectively install BlackArch tools for those who has limited bandwith.

	With BlackArch you can install tools by category.

	One category today.
	And other category the day after.

	You can install only what you need.
	And keep our system clean.

-- -- --

It is true, with Kali phenomenon, n00b jump install Kali for first time GNU/Linux.

It is also true the fact that BlackArch is completely unknown alien for most pen test beginner.

In some countries, let's say mine, what those high-school-beginner want is to hack wifi.  But hey... take the bright side, they desire to learn GNU/Linux. More user than ubuntu user. An army of n00b. But a potential GNU/Linux user.

Ironic, that those student whose willing to learn, they usually do not have enough resource. An obsolete notebook, 3rd world country, non-english speaking and very limited internet access. Some even use ssh injection, hacking cellular line. You know what I mean.

How about getting serious with BlackArch. Well, there's no way, a student download a 4.2 GB. It would take days to have complete ISO. A few student that I know, sneak into their school, just to have a free wifi, so they can install GNU/Linux distro. The other option is waiting for midnight, tethering from cellular after midnight is are cheaper. The only option for student who live near the jungle.

**sigh**

[![BlackArch XMonad: Category][image-ss-blackarch-categories]{: .img-responsive }][photo-ss-blackarch-categories]

I can't give them a good solution. But at least I can give option for those who are willing to learn.

With BlackArch you can install tools by category. One category today. And other category the day after. Until all completely installed. Or you can install only what you need. And keep our system clean.

To install a category of tools, run

{% highlight bash %}
># pacman -S blackarch-<category>
{% endhighlight %}

To see the blackarch categories, run

{% highlight bash %}
># pacman -Sg | grep blackarch
{% endhighlight %}

![BlackArch XMonad: All Category][image-ss-blackarch-all-categories]{: .img-responsive }

But first you need Arch (or maybe Manjaro) installed first on your system.

**sigh**

-- -- --

Official Site

* <https://blackarch.org/>

This is just another unproven idea for non-so-wealthy student.
And I am not sure if this could help either.

Have a good day everyone :-)

-- -- --

Note: Today is my favorite day.


[//]: <> ( -- -- -- links below -- -- -- )

[image-ss-blackarch-categories]: {{ site.url }}/assets/posts/system/2014/12/blackarch-categories.png
[photo-ss-blackarch-categories]: https://photos.google.com/album/AF1QipNVMF7qcC-QwEs9Hb8xD2ywBu9GrLt09jCFT_4U/photo/AF1QipNfE6VynnLVLAZQFncSnGgydNF0q8W2Vgn07XM7
[image-ss-blackarch-all-categories]: {{ site.url }}/assets/posts/system/2014/12/blackarch-all-categories.png
