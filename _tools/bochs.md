
Install bochs on Mac
```
  brew install bochs
```


## config
```
# 第一步，首先设置Bochs 在运行过程中能够使用的内存，本例为32MB。
# 关键字为：megs
megs： 32

# 第二步，设置对应真实机器的BIOS 和VGA BIOS。
# 对应两个关键字为：romimage 和 vgaromimage
romimage: file=/实际路径/bochs/share/bochs/BIOS-bochs-latest
vgaromimage: file=/实际路径/bochs/share/bochs/VGABIOS-lgpl-latest

# 第三步，设置Bochs 所使用的磁盘，软盘的关键字为floppy。
# 若只有一个软盘，则使用floppya 即可，若有多个，则为floppya，floppyb…
#floppya: 1_44=a.img, status=inserted

# 第四步，选择启动盘符。
#boot: floppy #默认从软盘启动，将其注释
boot: disk #改为从硬盘启动。我们的任何代码都将直接写在硬盘上，所以不会再有读写软盘的操作。

# 第五步，设置日志文件的输出。
log: bochs.out

# 第六步，开启或关闭某些功能。
# 下面是关闭鼠标，并打开键盘。
mouse: enabled=0
keyboard_mapping: enabled=1,
map=/实际路径/bochs/share/bochs/keymaps/x11-pc-us.map

# 硬盘设置
ata0: enabled=1, ioaddr1=0x1f0, ioaddr2=0x3f0, irq=14

# 下面的是增加的bochs 对gdb 的支持，这样gdb 便可以远程连接到此机器的1234 端口调试了
gdbstub: enabled=1, port=1234, text_base=0, data_base=0, bss_base=0

```

## bximage
Create floppy disk


Create hd
```
bin/bximage -hd -mode="flat" -size=60 -q hd60M.img
```

## Debug
experiment : 0x12345678 在内存中是怎样存储的
```
b 0x7c00           # 在内存的0x7c00 处插入断点 0x7c00 是BIOS 把mbr 加载到内存后会跳转过去的地址

xp/4 0x200000      
# 显示物理内存0x200000 开始处的4 个字节，可见其为00、00、00、00，
# 地址是从左到右逐渐升高的，其中每一对00 就占用1 个字节，它们的值都是0。

现在用setpmem 命令 在该地址处写入0x12345678

再用xp/4 命令查看内存地址0x200000 处的内容，内存低地址到高地址，依次变成了0x78、0x56、0x34、0x12。

```