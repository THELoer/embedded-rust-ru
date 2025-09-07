# macOS

Все инструменты можно установить с помощью [Homebrew] или [MacPorts]:

[Homebrew]: http://brew.sh/
[MacPorts]: https://www.macports.org/

## Установка инструментов с [Homebrew]

``` text
$ # GDB
$ brew install arm-none-eabi-gdb

$ # OpenOCD
$ brew install openocd

$ # QEMU
$ brew install qemu
```

> **ПРИМЕЧАНИЕ** Если OpenOCD падает, может потребоваться установка последней версии с помощью: 
```text
$ brew install --HEAD openocd
```

## Установка инструментов с [MacPorts]

``` text
$ # GDB
$ sudo port install arm-none-eabi-gcc

$ # OpenOCD
$ sudo port install openocd

$ # QEMU
$ sudo port install qemu
```

Это все! Перейдите к [следующему разделу].

[следующему разделу]: verify.md
