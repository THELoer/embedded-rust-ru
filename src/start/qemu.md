# QEMU

Мы начнем с написания программы для [LM3S6965], микроконтроллера Cortex-M3. Мы выбрали его в качестве начальной цели, потому что он [может быть эмулирован](https://wiki.qemu.org/Documentation/Platforms/ARM#Supported_in_qemu-system-arm) с использованием QEMU, так что в этом разделе вам не придется возиться с оборудованием, и мы сможем сосредоточиться на инструментах и процессе разработки.

[LM3S6965]: http://www.ti.com/product/LM3S6965

**ВАЖНО**  
В этом руководстве мы будем использовать имя "app" для проекта. Везде, где вы видите слово "app", заменяйте его на имя, которое вы выбрали для своего проекта. Или вы можете назвать свой проект "app" и избежать замен.

## Создание нестандартной программы на Rust

Мы будем использовать шаблон проекта [`cortex-m-quickstart`] для создания нового проекта. Созданный проект будет содержать базовое приложение: хорошую отправную точку для нового встраиваемого приложения на Rust. Кроме того, проект будет содержать директорию `examples` с несколькими отдельными приложениями, демонстрирующими ключевые функции встраиваемого Rust.

[`cortex-m-quickstart`]: https://github.com/rust-embedded/cortex-m-quickstart

### Использование `cargo-generate`
Сначала установите `cargo-generate`:

```console
cargo install cargo-generate
```

Затем создайте новый проект:

```console
cargo generate --git https://github.com/rust-embedded/cortex-m-quickstart
```

```text
 Project Name: app
 Creating project called `app`...
 Done! New project created /tmp/app
```

```console
cd app
```

### Использование `git`

Склонируйте репозиторий:

```console
git clone https://github.com/rust-embedded/cortex-m-quickstart app
cd app
```

Затем заполните заполнители в файле `Cargo.toml`:

```toml
[package]
authors = ["{{authors}}"] # "{{authors}}" -> "John Smith"
edition = "2018"
name = "{{project-name}}" # "{{project-name}}" -> "app"
version = "0.1.0"
```

Теперь давайте настроим отладку в GDB, чтобы увидеть, как работает программа. Мы будем использовать пример `hello.rs` из директории `examples`.

Сначала скомпилируйте пример:

```console
cargo build --example hello
```

Запустите QEMU в одном терминале:

```console
cargo run --example hello
```

В другом терминале запустите GDB:

```console
arm-none-eabi-gdb target/thumbv7m-none-eabi/debug/examples/hello
```

В GDB подключитесь к QEMU:

```console
(gdb) target remote :1234
```

Теперь вы можете установить точку останова на функции `Reset`, которая является точкой входа программы:

```console
(gdb) break Reset
Breakpoint 1 at 0x8000942: file src/lib.rs, line 473.
```

Запустите программу до точки останова:

```console
(gdb) continue
Continuing.

Breakpoint 1, app::__cortex_m_rt_reset () at src/lib.rs:473
473     unsafe extern "C" fn Reset() -> ! {
```

> **ПРИМЕЧАНИЕ**: Если при установке точки останова на `Reset`, как показано выше, GDB выдает предупреждения вроде:  
>  
> `core::num::bignum::Big32x40::mul_small () at src/libcore/num/bignum.rs:254`  
> `    src/libcore/num/bignum.rs: No such file or directory.`  
>  
> Это известная ошибка. Вы можете спокойно игнорировать эти предупреждения, скорее всего, вы находитесь в `Reset()`.

Этот обработчик сброса в конечном итоге вызовет нашу основную функцию. Давайте пропустим все до нее, используя точку останова и команду `continue`. Сначала посмотрим, где мы хотим установить точку останова, с помощью команды `list`:

```console
list main
```

Это покажет исходный код из файла `examples/hello.rs`:

```text
6       use panic_halt as _;
7
8       use cortex_m_rt::entry;
9       use cortex_m_semihosting::{debug, hprintln};
10
11      #[entry]
12      fn main() -> ! {
13          hprintln!("Hello, world!").unwrap();
14
15          // Выход из QEMU
```

Мы хотим установить точку останова перед "Hello, world!", которая находится на строке 13. Сделайте это с помощью команды `break`:

```console
break 13
```

Теперь мы можем указать GDB запустить программу до нашей основной функции с помощью команды `continue`:

```console
continue
```

```text
Continuing.

Breakpoint 1, hello::__cortex_m_rt_main () at examples\hello.rs:13
13          hprintln!("Hello, world!").unwrap();
```

Теперь мы близки к коду, который выводит "Hello, world!". Давайте продвинемся вперед с помощью команды `next`:

```console
next
```

```text
16          debug::exit(debug::EXIT_SUCCESS);
```

На этом этапе вы должны увидеть "Hello, world!" в терминале, где запущен `qemu-system-arm`:

```text
$ qemu-system-arm (..)
Hello, world!
```

Вызов `next` еще раз завершит процесс QEMU:

```console
next
```

```text
[Inferior 1 (Remote target) exited normally]
```

Теперь вы можете выйти из сессии GDB:

```console
quit
```
