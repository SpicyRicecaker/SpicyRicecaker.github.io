---
layout: "../../layouts/BlogPost.astro"
title: "How to natively play a movie with subtitles on iPadOS"
description: ""
pubDate: "August 17 2023"
# heroImage: "/placeholder-hero.jpg"
---

Prerequisites: 
- iPad running iPadOS 16 or greater
- Macbook running MacOS Catalina or greater
- A USB C cable
- A "movie" file supported by apple, e.g. a `.mp4` file
- A subtifile file

## 1. Mux the subtitle file into the mp4 file itself. 

There are various ways to do this, but the easiest is to use [`subler`](https://subler.org/), an open source muxer. Download it, select your mp4 file, and drag the subtitle file into the window.

## 2. Add muxed movie into Apple TV

On your macbook, open the TV app and import your muxed movie by either clicking `File > Import` in the menu bar or `command + o`. If you go the library tab in the TV app, you should see your movie, and when playing it back in the TV app, you should be able to see your muxed subtitles in the movie.

## 3. Sync muxed movie from Apple TV app in macbook to Apple TV app on iPad

Connect the ipad to the macbook via usb c. Click trust on the iPad. Then, open the finder app on macbook, and under "Locations" ensure your ipad is visible.

Now go to your TV app on macos. In the library tab, right click your muxed movie, hover over "add to device", and select your ipad. Now wait for a while for the movie to be done syncing.

## 4. Open Apple TV app on iPad & Begin viewing movie!

Profit!