title: linux配置支持中文字体
date: 2018-11-06T04:49:37.266Z
tags: [linux]
categories: [运维]
---
> 调用html转图片的服务时，本地调试没问题，部署到服务器上中文就转成了乱码，找了资料才知道是因为linux上没有安装对应字体。

- 查看字体包生效目录

```
vim /etc/fonts/fonts.conf
```
<!--more-->

- 由配置可知，我们在这四个文件夹中添加对应字体即可，笔者选用`~/.fonts`目录，当然添加自己的字体目录也可以
```
<!-- Font directory list -->

        <dir>/usr/share/fonts</dir>
        <dir>/usr/X11R6/lib/X11/fonts</dir> 
        <dir>/usr/local/share/fonts</dir>
        <dir>~/.fonts</dir>

```
```
cd ~
mkdir .fonts
上传字体包
链接：https://pan.baidu.com/s/1dEvIHJWY5REvCnOrxQ5lpg 
提取码：nmyw 
```

- 上传对应字体包
```
appops@hzabj-pgad-qa5:~/.fonts$ ls
simhei.ttf  simsun.ttc
```

- 清理缓存并生效
```
fc-cache

fc-list查看是否生效
appops@hzabj-pgad-qa5:~/.fonts$ fc-list
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSans-Oblique.ttf: DejaVu Sans:style=Oblique
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSans-BoldOblique.ttf: DejaVu Sans:style=Bold Oblique
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSerif-BoldItalic.ttf: DejaVu Serif:style=Bold Italic
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSerif.ttf: DejaVu Serif:style=Book
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSerifCondensed.ttf: DejaVu Serif,DejaVu Serif Condensed:style=Condensed,Book
/home/appops/.fonts/simsun.ttc: NSimSun,新宋体:style=Regular
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSerifCondensed-BoldItalic.ttf: DejaVu Serif,DejaVu Serif Condensed:style=Condensed Bold Italic,Bold Italic
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSans.ttf: DejaVu Sans:style=Book
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSerifCondensed-Bold.ttf: DejaVu Serif,DejaVu Serif Condensed:style=Condensed Bold,Bold
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSans-ExtraLight.ttf: DejaVu Sans,DejaVu Sans Light:style=ExtraLight
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSansMono-Bold.ttf: DejaVu Sans Mono:style=Bold
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSansMono-BoldOblique.ttf: DejaVu Sans Mono:style=Bold Oblique
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSansCondensed-Oblique.ttf: DejaVu Sans,DejaVu Sans Condensed:style=Condensed Oblique,Oblique
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSansMono-Oblique.ttf: DejaVu Sans Mono:style=Oblique
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSerif-Bold.ttf: DejaVu Serif:style=Bold
/home/appops/.fonts/simhei.ttf: SimHei,黑体:style=Regular,Normal,obyčejné,Standard,Κανονικά,Normaali,Normál,Normale,Standaard,Normalny,Обычный,Normálne,Navadno,Arrunta
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSansCondensed-BoldOblique.ttf: DejaVu Sans,DejaVu Sans Condensed:style=Condensed Bold Oblique,Bold Oblique
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSerifCondensed-Italic.ttf: DejaVu Serif,DejaVu Serif Condensed:style=Condensed Italic,Italic
/home/appops/.fonts/simsun.ttc: SimSun,宋体:style=Regular
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSerif-Italic.ttf: DejaVu Serif:style=Italic
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSans-Bold.ttf: DejaVu Sans:style=Bold
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSansMono.ttf: DejaVu Sans Mono:style=Book
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSansCondensed-Bold.ttf: DejaVu Sans,DejaVu Sans Condensed:style=Condensed Bold,Bold
/usr/share/fonts/truetype/ttf-dejavu/DejaVuSansCondensed.ttf: DejaVu Sans,DejaVu Sans Condensed:style=Condensed,Book
```

- 重启服务即可
