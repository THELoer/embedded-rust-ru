# Проверка установки

В этом разделе мы проверяем, что некоторые требуемые инструменты / драйверы были правильно установлены и настроены.

Подключите ваш ноутбук / ПК к плате discovery с помощью кабеля Mini-USB. Плата discovery имеет два разъема USB; используйте тот, с меткой "USB ST-LINK", который находится в центре края платы.

Также проверьте, что заголовок ST-LINK установлен. Смотрите картинку ниже; заголовок ST-LINK выделен.

<p align="center">
<img title="Подключенная плата discovery" src="../../assets/verify.jpeg">
</p>

Теперь выполните следующую команду:

``` console
openocd -f interface/stlink.cfg -f target/stm32f3x.cfg
```

> **ПРИМЕЧАНИЕ**: Старые версии openocd, включая релиз 0.10.0 от 2017 года, не содержат новый (и предпочтительный) файл `interface/stlink.cfg`; вместо этого может потребоваться использовать `interface/stlink-v2.cfg` или `interface/stlink-v2-1.cfg`.

Вы должны получить следующий вывод, и программа заблокирует консоль:

``` text
Open On-Chip Debugger 0.10.0
Licensed under GNU GPL v2
For bug reports, read
        http://openocd.org/doc/doxygen/bugs.html
Info : auto-selecting first available session transport "hla_swd". To override use 'transport select <transport>'.
adapter speed: 1000 kHz
adapter_nsrst_delay: 100
Info : The selected transport took over low-level target control. The results might differ compared to plain JTAG/SWD
none separate
Info : Unable to match requested speed 1000 kHz, using 950 kHz
Info : Unable to match requested speed 1000 kHz, using 950 kHz
Info : clock speed 950 kHz
Info : STLINK v2 JTAG v27 API v2 SWIM v15 VID 0x0483 PID 0x374B
Info : using stlink api v2
Info : Target voltage: 2.919881
Info : stm32f3x.cpu: hardware has 6 breakpoints, 4 watchpoints
```

Содержимое может не совпадать точно, но вы должны получить последнюю строку о точках останова и наблюдения. Если вы получили ее, завершите процесс OpenOCD и перейдите к [следующему разделу].

[следующему разделу]: ../../start/index.md

Если вы не получили строку "breakpoints", попробуйте одну из следующих команд.

``` console
openocd -f interface/stlink-v2.cfg -f target/stm32f3x.cfg
```

``` console
openocd -f interface/stlink-v2-1.cfg -f target/stm32f3x.cfg
```

Если одна из этих команд работает, это значит, что у вас старая аппаратная ревизия платы discovery. Это не будет проблемой, но запомните этот факт, поскольку вам потребуется немного по-другому настроить вещи позже. Вы можете перейти к [следующему разделу].

Если ни одна из команд не работает от обычного пользователя, попробуйте запустить их с правами root (например, `sudo openocd ..`). Если команды работают с правами root, проверьте, что [правила udev] установлены правильно.

[правила udev]: linux.md#udev-rules

Если вы дошли до этого момента и OpenOCD не работает, пожалуйста, откройте [issue], и мы поможем вам!

[issue]: https://github.com/rust-embedded/book/issues
