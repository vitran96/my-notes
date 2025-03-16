# Roll back an update

Link: https://unix.stackexchange.com/questions/552688/is-it-possible-to-roll-back-a-flatpak-update

First you look for the commit you are interested in; this example is with GIMP:

```
$ flatpak remote-info --log flathub org.gimp.GIMP

GNU Image Manipulation Program - Create images and edit photographs

        ID: org.gimp.GIMP
       Ref: app/org.gimp.GIMP/x86_64/stable
      Arch: x86_64
    Branch: stable
   Version: 2.10.14
   License: GPL-3.0+ AND LGPL-3.0+
Collection: org.flathub.Stable
  Download: 108.9 MB
 Installed: 308.1 MB
   Runtime: org.gnome.Platform/x86_64/3.32

       Sdk: org.gnome.Sdk/x86_64/3.32
    Commit: 8003d469b2224a1abe54b45793c9692e822cacee860d94861f5fc5e43111a689
    Parent: 3fdfdf0963ba67629f287092229d9f5659bc9a2eeb1158c060b09d8a603acbcf
   Subject: Fix CPU detection on GEGL build. (3b047adf)
      Date: 2019-10-30 00:56:11 +0000
   History: 

    Commit: 3fdfdf0963ba67629f287092229d9f5659bc9a2eeb1158c060b09d8a603acbcf
   Subject: Fix typo! (a752f7df)
      Date: 2019-09-02 20:28:05 +0000

    Commit: 798d8cd8a6a2ccf1bf21c0f5e9749dd4d97ecd511a3af1922d6fedf6c26e3a7c
   Subject: Release GIMP 2.10.12! (1366fa63)
      Date: 2019-06-13 02:25:52 +0000
    ...
```

So, if you wanted to go back to v2.10.12, you'd deploy this commit:

```
flatpak update \
  --commit=798d8cd8a6a2ccf1bf21c0f5e9749dd4d97ecd511a3af1922d6fedf6c26e3a7c \
  org.gimp.GIMP
```

# Freeze a package

Link: https://unix.stackexchange.com/users/50403/syco

Prevent GIMP from being updated the next time you ran `flatpak update` (since v1.5)

```bash
flatpak mask org.gimp.GIMP
```
