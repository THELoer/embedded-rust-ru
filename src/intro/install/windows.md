# Windows

## `arm-none-eabi-gdb`

ARM предоставляет установщики `.exe` для Windows. Возьмите один отсюда [gcc] и следуйте инструкциям.
Непосредственно перед завершением процесса установки отметьте опцию "Add path to environment variable".
Затем проверьте, что инструменты в вашем `%PATH%`:

``` text
$ arm-none-eabi-gdb -v
GNU gdb (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 8.1.0.20180315-git
(..)
```

[gcc]: https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads

## OpenOCD

Официального бинарного релиза OpenOCD для Windows нет, но если вы не в настроении компилировать его самостоятельно, проект xPack предоставляет бинарную дистрибуцию [здесь][openocd]. Следуйте предоставленным инструкциям по установке. Затем обновите переменную окружения `%PATH%`, чтобы включить путь, куда были установлены бинарные файлы. (`C:\Users\USERNAME\AppData\Roaming\xPacks\@xpack-dev-tools\openocd\0.10.0-13.1\.content\bin\`,
если вы использовали простую установку) 

[openocd]: https://xpack.github.io/openocd/

Проверьте, что OpenOCD в вашем `%PATH%` с помощью:

``` text
$ openocd -v
Open On-Chip Debugger 0.10.0
(..)
```

## QEMU

Возьмите QEMU с [официального сайта][qemu].

[qemu]: https://www.qemu.org/download/#windows

## Драйвер USB ST-LINK

Вам также потребуется установить [этот драйвер USB], иначе OpenOCD не будет работать. Следуйте инструкциям установщика и убедитесь, что устанавливаете правильную версию (32-битную или 64-битную) драйвера.

[этот драйвер USB]: http://www.st.com/en/embedded-software/stsw-link009.html

Это все! Перейдите к [следующему разделу].

[следующему разделу]: verify.md
