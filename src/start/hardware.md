# Аппаратное обеспечение

К этому моменту вы уже должны быть немного знакомы с инструментами и процессом разработки. В этом разделе мы перейдем к реальному аппаратному обеспечению; процесс останется в основном тем же. Давайте начнем.

## Знайте ваше оборудование

Перед началом вам нужно определить некоторые характеристики целевого устройства, поскольку они будут использоваться для настройки проекта:

- Ядро ARM. Например, Cortex-M3.
- Есть ли у ядра ARM FPU? Ядра Cortex-M4**F** и Cortex-M7**F** имеют FPU.
- Сколько флэш-памяти и оперативной памяти имеет целевое устройство? Например, 256 КиБ флэш-памяти и 32 КиБ оперативной памяти.
- Где отображаются флэш-память и оперативная память в адресном пространстве? Например, оперативная память обычно располагается по адресу `0x2000_0000`.

Эту информацию можно найти в техническом описании или справочном руководстве вашего устройства.

В этом разделе мы будем использовать наше эталонное оборудование — плату STM32F3DISCOVERY. Эта плата содержит микроконтроллер STM32F303VCT6. Этот микроконтроллер имеет:

- Ядро Cortex-M4F с однопрецизионным FPU.
- 256 КиБ флэш-памяти, расположенной по адресу `0x0800_0000`.
- 40 КиБ оперативной памяти, расположенной по адресу `0x2000_0000`. (Есть еще одна область оперативной памяти, но для простоты мы ее проигнорируем).

## Настройка

Мы начнем с нуля с новым экземпляром шаблона. Обратитесь к [предыдущему разделу о QEMU] для напоминания о том, как это сделать без использования `cargo-generate`.

[предыдущий раздел о QEMU]: qemu.md

```text
$ cargo generate --git https://github.com/rust-embedded/cortex-m-quickstart
 Project Name: app
 Creating project called `app`...
 Done! New project created /tmp/app

$ cd app
```

Первым шагом является установка целевого компилятора по умолчанию в файле `.cargo/config.toml`.

```console
tail -n5 .cargo/config.toml
```

```toml
# Выберите ОДИН из этих целей компиляции
# target = "thumbv6m-none-eabi...
```

Теперь нам нужно создать GDB-скрипт для загрузки программы и взаимодействия с платой. В шаблоне уже есть один GDB-скрипт с именем `openocd.gdb`, созданный на этапе `cargo generate`, и он должен работать без изменений. Давайте взглянем на него:

```console
cat openocd.gdb
```

```text
target extended-remote :3333

# Печать деманглированных символов
set print asm-demangle on

# Обнаружение необработанных исключений, жестких сбоев и паник
break DefaultHandler
break HardFault
break rust_begin_unwind

monitor arm semihosting enable

load

# Запуск процесса с немедленной остановкой процессора
stepi
```

Теперь выполнение команды `<gdb> -x openocd.gdb target/thumbv7em-none-eabihf/debug/examples/hello` немедленно подключит GDB к OpenOCD, включит семихостинг, загрузит программу и запустит процесс.

Альтернативно, вы можете превратить `<gdb> -x openocd.gdb` в пользовательский запускатель, чтобы `cargo run` одновременно компилировал программу *и* запускал сессию GDB. Этот запускатель включен в `.cargo/config.toml`, но закомментирован.

```console
head -n10 .cargo/config.toml
```

```toml
[target.thumbv7m-none-eabi]
# Раскомментируйте это, чтобы `cargo run` запускал программы на QEMU
# runner = "qemu-system-arm -cpu cortex-m3 -machine lm3s6965evb -nographic -semihosting-config enable=on,target=native -kernel"

[target.'cfg(all(target_arch = "arm", target_os = "none"))']
# Раскомментируйте ОДИН из этих трех вариантов, чтобы `cargo run` запускал сессию GDB
# Какой вариант выбрать, зависит от вашей системы
runner = "arm-none-eabi-gdb -x openocd.gdb"
# runner = "gdb-multiarch -x openocd.gdb"
# runner = "gdb -x openocd.gdb"
```

```text
$ cargo run --example hello
(..)
Loading section .vector_table, size 0x400 lma 0x8000000
Loading section .text, size 0x1e70 lma 0x8000400
Loading section .rodata, size 0x61c lma 0x8002270
Start address 0x800144e, load size 10380
Transfer rate: 17 KB/sec, 3460 bytes/write.
(gdb)
```
