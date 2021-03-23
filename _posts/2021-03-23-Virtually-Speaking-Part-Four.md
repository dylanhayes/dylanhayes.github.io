---
layout: post
title: Virtually Speaking - Part four
---


I previously blogged about [speaking virtually](/Speaking-Virtually) and some of the challenges. In the previous blog posts we looked at ways to get up and running with OBS and customise the experience. Since then, I have found the although presenting online has become second nature, there are situations where things could be improved.

Before the pandemic put us all at home, the world was increasingly "mobile first", and software followed that. But the experience of using a mobile in a demo or a talk failed to do it justice. Anybody who deals with HTML knows how to hit F12 and bring up the developer tools, and show a preview of a site of a mobile device, but this doesn't work on for apps, and I've always found the experience of showing a non technical audience a tiny browser window and telling them that is what a phone user could see didn't always get the message across.

Since then I tried pointing a secondary camera at a carefully mounted phone, and whilst it convincingly shows something running on a phone, it wasn't always that easy to read.

What I actually wanted was to show the mobile phones screen in OBS, and then to be able to decorate the screen with logos, and ideally a camera view. This is possible, but requires additional software to put the mobile phones screen on your PC, and then you can simply create a new scene in OBS and add the window as a source. 

*Let's go through this step by step.*

The first thing we need is to get the phone's screen on the PC. The instructions are for Android as a source and Windows 10 as a destination. This is also possible with any combination of Android/Apple devices and/or a PC/Mac running OBS, but each use case is slightly difference and you'll need to find specific instructions.

## Phone to PC

### Option 1
Windows Connect and Android mirroring. Window 10 previously featured a handy app called Connect, but this now requires you to install it manually [Windows 10 - Features that have been removed - Windows Deployment | Microsoft Docs](https://docs.microsoft.com/en-us/windows/deployment/planning/windows-10-removed-features#:~:text=The%20Connect%20app%20for%20wireless,install%20the%20Wireless%20Display%20app.). This is a shame as it's free and works well. Once you have the app running, simply go to your Android device, and from settings look for "Screen Cast" and select you PC. This is very simple, and will work over Wi-Fi as long as both devices are connected to the same network (be warned when on some networks as a guest the two devices may not see each others).

![Windows 10 Connect](../../images/2021-03-23/mryzen.png)

![Android Cast](../../images/2021-03-23/cast.jpg)

### Option 2

If you  enable developer mode [Configure on-device developer options](https://developer.android.com/studio/debug/dev-options) on the phone, it is possible to project the device's screen into a window on the PC using an applications called "ScrCpy". You can find this application either bundled in Android developer Studio, or as a standand alone application [GitHub - Genymobile/scrcp](https://github.com/Genymobile/scrcpy). The screen sharing can be done via a USB cable or Wifi, and it is very fast. The USB cable is easier to setup, and has the advantage that both devices need not be able to see each other on the network.

### Option 3
There are various 3rd party apps made to allow projection from a phone to a Windows 10 PC. Most operate with a freemium model, so that you'll need to pay for all but the most basic functions. I have not tried any of these as option 1 and 2 has been sufficient for my needs.

### Option 4
Microsoft has a Windows 10 app called "Your Phone" that has recently started supporting running Android apps on a Windows 10 PC. So far there this limited to certain phones, but if this rolls out more widely it could be the easiest method of all.

My personal preference is that ScrCpy (option 2) is the most performant, but casting (option 1) is the easiest to get working as you don't need to enable developer mode on the Android device.

## Window to OBS

Having got your device on a window on your PC, next we need to bring this into OBS. First we need to create a new scene called "Phone". Then, we add a new Window capture.

![OBS Window Capture](../../images/2021-03-23/windowcapture.png)

Then choose the window which is displaying your phone (ScrCpy, Windows Connect etc)

![OBS Window Capture](../../images/2021-03-23/scrcpy.png)

And then it's as simple as placing this where you want at the size you like.

In my example, as the phone window is vertical and there's a lot of spare space, I have cropped in on a camera feed as a background. Please forgive the lockdown v3 hair.

![OBS Phone and PC](../../images/2021-03-23/phoneonpc.png)

You'll need to make sure the window is the right aspect ratio to avoid any ugly black bars. Frustratingly, Windows Connect pads the phone display with a lot of blank window, and doesn't look nearly as good as SrCpy. I found the best solution was to resize the Connect window to be bigger than the screen and use the crop and pad filter to chop the unused part of the window away.

![Crop filter](../../images/2021-03-23/crop.png)

## Going further

At this point it looking pretty good, but I wanted to put a little bit of space around the phone display to separate it from the camera background.

I found the screen resolution of the phone, and created a colour source with the same dimensions, and then placed this just behind the window and resized it just a little bit bigger to make a nice fame.

![Crop filter](../../images/2021-03-23/frame.png)

The by adding a little bit of transparency with a filter, I could fade this just a little bit. 

![Crop filter](../../images/2021-03-23/framepreview.png)

## Going even further

Much as I liked this, I felt the need to do just one more thing. I was able to get a neater border and a drop shadow by using some additional filter plugins I downloaded and installed on OBS called 
[ShaderFilter](https://github.com/Oncorporation/obs-shaderfilter/), but we'll save this for a future blog, as realistically the effort isn't justified by the improvement in asthetics.

![Drop shadow and Shader Filters](../../images/2021-03-23/framepreview.png)