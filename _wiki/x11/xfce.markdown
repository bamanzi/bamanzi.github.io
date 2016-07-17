---
layout: post
title: XFCE Tips
---

## XFCE Terminal's drop-down feature (guake style)

    xfce4-terminal --drop-down
	
It requires XFCE Terminal 0.6+ (i.e. XFCE >=4.10).  For Ubuntu, we can use the ppa: [xubuntu-dev/xfce-4.12](http://pav.lv/ppa/xubuntu-dev/xfce-4.12 ).

from: [How To Use Xfce4 Terminal 0.6.x As A Drop-Down Console (Guake-style) ~ Web Upd8: Ubuntu / Linux blog](http://www.webupd8.org/2012/05/install-xfce-410-in-xubuntu-1204.html )

## How to tile window by mouse to left/right edge in Xfce

You need xfwm4 >= 4.10.

Using the *Window Manager - Advanced options* you can disable the scrolling of workspaces when dragging the window left and right. This will tile the window on these borders.


Alternatively if you dont use workspaces, by simply **reducing the workspaces to 1** then windows will automatically snap to left and right.

via [xubuntu - How to tile window by mouse to left/right edge in Xfce (like in Unity)? - Ask Ubuntu](http://askubuntu.com/questions/483341/how-to-tile-window-by-mouse-to-left-right-edge-in-xfce-like-in-unity )

For xfce-4.8 (ubuntu-12.04),  there's a PPA for patched xfwm4: [fossfreedom/xfwm4](http://pad.lv/ppa/fossfreedom/xfwm4 ). (via <http://askubuntu.com/questions/103456/automatically-size-windows-using-xfce-like-in-gnome )

