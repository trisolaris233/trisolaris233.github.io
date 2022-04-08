+++
title = "The easiet way to view webp images on Arch Linux"
date = "2020-04-08"
+++



Yesterday... Or just early today, I was just looking for some pictures about Misaki Mei, Ano kawaii girl that I came up with at a moment, she was from an anime which cames from a light novel together with a manga. It's funny that the three edition has quite a different stories. I had only watched the anime during last summer, the picnic anime faded with so cute a girl involved. Who TM would be scared?

{% asset_img misaki_mei.jpg %}

That was the case I found some .webp images.

And I found the `Gwenview`, my KDE Plasma default image viewer, I love it pretty much, but it did not support to view webp image files.

Then I go to google for help, and I ended up finding a pile of like Top n Best Programs to view webp files on Linux, at this time, the google was not better than Baidu as before it used to. Then I used the gThumb.

But gThumb was a mother-fucker program, I did not like it. For I am convinced that `GwenView NB`.So I was eager to find alternative way to solve the simple requirement of viewing webp images.

Luckily, I fond `vwebp`, I loved this name because `IT IS CLEAR`. You can easily download vwebp from [google's website](https://developers.google.com/speed/webp/download)(ps: You should search with keyword webp rather than vwebp, which would only give you shit)

You just simply downloaded it and extracted it. the directory will look like this:
```shell
zytc@etc:Downloads/libwebp-1.0.3-linux-x86-64 $ ll  
total 52K
drwxr-xr-x 2 zytc zytc 4.0K Jul  5  2019 bin
drwxr-xr-x 2 zytc zytc 4.0K Jul  5  2019 doc
drwxr-xr-x 3 zytc zytc 4.0K Jul  5  2019 include
drwxr-xr-x 2 zytc zytc 4.0K Jul  5  2019 lib
-rw-r--r-- 1 zytc zytc  24K Jul  5  2019 README
-rw-r--r-- 1 zytc zytc 8.8K Jul  5  2019 README.mux
```


and the executable binary is in the directory `bin`, what you need to do is simply create a symbolic link to the `vwebp` binary to one of your `$PATH `directory, generally `/usr/bin`, as below:




```shell
zytc@etc:~$ ln -s bin/vwebp /usr/bin/webp
```

and it would do what you want. However, I created and try to run the command it crashed with an error.


```shell
zytc@etc:Downloads/libwebp-1.0.3-linux-x86-64 $ vwebp --version
vwebp: error while loading shared libraries: libglut.so.3: cannot open shared object file: No such file or directory
```

it seemed a f**king library is needed for vwebp to run properly. In fact, our system lacked of `glut`, which provides several openGL operations, you can install with `yay -S glut` and select the `freeglut` to install it. After all the stuff get over, it just got its way. then input 
```shell
zytc@etc:~$ vwebp ~/gallery/Misaki_mei/ &
zytc@etc:~$ sudo pacman -Rs gthumb-lite
```


{% asset_img screenshot.png %}

**Ohhhhhhhhhhhhhhhhhhhhhhh**, F**k, kanojo wa so cute!!!!!(●´∀`●)

## Summing Up

so the easies way to view webp images on arch linux is
- Download the webp tools from google, as above
- Install glut
- Create symbolic links or other ways you can get `vwebp` work.
- ~~UNINSTALL GTHUMB~~
