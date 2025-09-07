# Семихостинг

Семихостинг — это механизм, который позволяет встраиваемым устройствам выполнять ввод/вывод на хосте и в основном используется для записи сообщений в консоль хоста. Семихостинг требует отладочной сессии и почти ничего больше (никаких дополнительных проводов!), поэтому его очень удобно использовать. Недостаток в том, что он очень медленный: каждая операция записи может занимать несколько миллисекунд в зависимости от используемого аппаратного отладчика (например, ST-Link).

Крейт [`cortex-m-semihosting`] предоставляет API для выполнения операций семихостинга на устройствах Cortex-M. Программа ниже — это версия "Hello, world!" с использованием семихостинга:

[`cortex-m-semihosting`]: https://crates.io/crates/cortex-m-semihosting

```rust,ignore
#![no_main]
#![no_std]

use panic_halt as _;

use cortex_m_rt::entry;
use cortex_m_semihosting::hprintln;

#[entry]
fn main() -> ! {
    hprintln!("Hello, world!").unwrap();

    loop {}
}
```

Если вы запустите эту программу на оборудовании, вы увидите сообщение "Hello, world!" в логах OpenOCD.

```text
$ openocd
(..)
Hello, world!
(..)
```

Вам нужно сначала включить семихостинг в OpenOCD из GDB:

```console
(gdb) monitor arm semihosting enable
semihosting is enabled
```

QEMU понимает операции семихостинга, поэтому приведенная выше программа также будет работать с `qemu-system-arm` без необходимости запуска отладочной сессии. Обратите внимание, что вам нужно передать флаг `-semihosting-config` в QEMU для включения поддержки семихостинга; эти флаги уже включены в файл `.cargo/config.toml` шаблона.

```text
$ # Эта программа заблокирует терминал
$ cargo run
     Running `qemu-system-arm (..)
Hello, world!
```

Существует также операция семихостинга `exit`, которая может быть использована для завершения процесса QEMU. Важно: **не** используйте `debug::exit` на оборудовании; эта функция может повредить вашу сессию OpenOCD, и вы не сможете отлаживать другие программы, пока не перезапустите ее.

```rust,ignore
#![no_main]
#![no_std]

use panic_halt as _;

use cortex_m_rt::entry;
use cortex_m_semihosting::debug;

#[entry]
fn main() -> ! {
    let roses = "blue";

    if roses == "red" {
        debug::exit(debug::EXIT_SUCCESS);
    } else {
        debug::exit(debug::EXIT_FAILURE);
    }

    loop {}
}
```

```text
$ cargo run
     Running `qemu-system-arm (..)

$ echo $?
1
```

Один последний совет: вы можете настроить поведение паники на `exit(EXIT_FAILURE)`. Это позволит вам писать тесты `no_std` с проверкой прохождения, которые можно запускать на QEMU.

Для удобства крейт `panic-semihosting` имеет функцию "exit", которая при включении вызывает `exit(EXIT_FAILURE)` после записи сообщения о панике в stderr хоста.

```rust,ignore
#![no_main]
#![no_std]

use panic_semihosting as _; // features = ["exit"]

use cortex_m_rt::entry;
use cortex_m_semihosting::debug;

#[entry]
fn main() -> ! {
    let roses = "blue";

    assert_eq!(roses, "red");

    loop {}
}
```

```text
$ cargo run
     Running `qemu-system-arm (..)
panicked at 'assertion failed: `(left == right)`
  left: `"blue"`,
 right: `"red"`', examples/hello.rs:15:5

$ echo $?
1
```

**ПРИМЕЧАНИЕ**: Чтобы включить эту функцию в `panic-semihosting`, отредактируйте раздел зависимостей в `Cargo.toml`, где указан `panic-semihosting`:

```toml
panic-semihosting = { version = "VERSION", features = ["exit"] }
```

где `VERSION` — желаемая версия. Для получения дополнительной информации о функциях зависимостей обратитесь к разделу [`указание зависимостей`] книги Cargo.

[`указание зависимостей`]: https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html
