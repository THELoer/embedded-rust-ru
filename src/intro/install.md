# Установка инструментов

Эта страница содержит инструкции по установке некоторых инструментов, не зависящие от ОС:

### Цепочка инструментов Rust

Установите rustup, следуя инструкциям на [https://rustup.rs](https://rustup.rs).

**ПРИМЕЧАНИЕ** Убедитесь, что у вас версия компилятора не ниже 1.31. Команда `rustc -V` должна возвращать дату новее указанной ниже.

``` text
$ rustc -V
rustc 1.31.1 (b6c32da9b 2018-12-18)
```

Для экономии трафика и места на диске установка по умолчанию поддерживает только нативную компиляцию. Чтобы добавить поддержку кросс-компиляции для архитектур ARM Cortex-M, выберите один из следующих целевых объектов компиляции. Для платы STM32F3DISCOVERY, используемой в примерах этой книги, используйте цель `thumbv7em-none-eabihf`.
[Найдите подходящий Cortex-M для вас.](https://developer.arm.com/ip-products/processors/cortex-m#c-7d3b69ce-5b17-4c9e-8f06-59b605713133)

Cortex-M0, M0+ и M1 (архитектура ARMv6-M):
``` console
rustup target add thumbv6m-none-eabi
```

Cortex-M3 (архитектура ARMv7-M):
``` console
rustup target add thumbv7m-none-eabi
```

Cortex-M4 и M7 без аппаратной поддержки операций с плавающей запятой (архитектура ARMv7E-M):
``` console
rustup target add thumbv7em-none-eabi
```

Cortex-M4F и M7F с аппаратной поддержкой операций с плавающей запятой (архитектура ARMv7E-M):
``` console
rustup target add thumbv7em-none-eabihf
```

Cortex-M23 (архитектура ARMv8-M):
``` console
rustup target add thumbv8m.base-none-eabi
```

Cortex-M33 и M35P (архитектура ARMv8-M):
``` console
rustup target add thumbv8m.main-none-eabi
```

Cortex-M33F и M35PF с аппаратной поддержкой операций с плавающей запятой (архитектура ARMv8-M):
``` console
rustup target add thumbv8m.main-none-eabihf
```

### `cargo-binutils`

``` text
cargo install cargo-binutils

rustup component add llvm-tools
```
WINDOWS: убедитесь, что установлены C++ Build Tools для Visual Studio 2019. https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16 
### `cargo-generate`

Мы используем это позже для генерации проекта из шаблона.

``` console
cargo install cargo-generate
```

Примечание: в некоторых дистрибутивах Linux (например, Ubuntu) может потребоваться установка пакетов `libssl-dev` и `pkg-config` перед установкой cargo-generate.

### Инструкции, специфичные для ОС

Теперь следуйте инструкциям, специфичным для вашей ОС:

- [Linux](install/linux.md)
- [Windows](install/windows.md)
- [macOS](install/macos.md)
