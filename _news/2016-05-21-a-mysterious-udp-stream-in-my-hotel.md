---
title: Reverse Engineering A Mysterious UDP Stream in My Hotel
website: http://wiki.gkbrk.com/Hotel_Music.html
date: 2016-05-21
---

Hey everyone, I have been staying at a hotel for a while. It's one of those modern ones with smart TVs and other connected goodies. I got curious and opened Wireshark, as any tinkerer would do.

I was very surprised to see a huge amount of UDP traffic on port 2046. I looked it up but the results were far from useful. This wasn't a standard port, so I would have to figure it out manually.

At first, I suspected that the data might be a television stream for the TVs, but the packet length seemed too small, even for a single video frame.

