# Содержание

<!--

Организация этой книги всё ещё находится в процессе разработки.

Для дополнительной информации и координации обратитесь к https://github.com/rust-embedded/book/issues

-->

- [Введение](./intro/index.md)
    - [Аппаратное обеспечение](./intro/hardware.md)
    - [`no_std`](./intro/no-std.md)
    - [Инструменты](./intro/tooling.md)
    - [Установка](./intro/install.md)
        - [Linux](./intro/install/linux.md)
        - [MacOS](./intro/install/macos.md)
        - [Windows](./intro/install/windows.md)
        - [Проверка установки](./intro/install/verify.md)
- [Начало работы](./start/index.md)
  - [QEMU](./start/qemu.md)
  - [Аппаратное обеспечение](./start/hardware.md)
  - [Регистры с отображением в память](./start/registers.md)
  - [Полухостинг](./start/semihosting.md)
  - [Паника](./start/panicking.md)
  - [Исключения](./start/exceptions.md)
  - [Прерывания](./start/interrupts.md)
  - [Ввод/вывод](./start/io.md)
- [Периферийные устройства](./peripherals/index.md)
    - [Первая попытка на Rust](./peripherals/a-first-attempt.md)
    - [Проверка заимствования](./peripherals/borrowck.md)
    - [Синглтоны](./peripherals/singletons.md)
- [Статические гарантии](./static-guarantees/index.md)
    - [Программирование с использованием типовых состояний](./static-guarantees/typestate-programming.md)
    - [Периферийные устройства как конечные автоматы](./static-guarantees/state-machines.md)
    - [Контракты проектирования](./static-guarantees/design-contracts.md)
    - [Абстракции с нулевой стоимостью](./static-guarantees/zero-cost-abstractions.md)
- [Портируемость](./portability/index.md)
- [Параллелизм](./concurrency/index.md)
- [Коллекции](./collections/index.md)
- [Шаблоны проектирования](./design-patterns/index.md)
    - [HAL](./design-patterns/hal/index.md)
        - [Контрольный список](./design-patterns/hal/checklist.md)
        - [Именование](./design-patterns/hal/naming.md)
        - [Взаимодействие](./design-patterns/hal/interoperability.md)
        - [Предсказуемость](./design-patterns/hal/predictability.md)
        - [GPIO](./design-patterns/hal/gpio.md)
- [Советы для разработчиков на C для встраиваемых систем](./c-tips/index.md)
    <!-- TODO: Определить разделы -->
- [Взаимодействие](./interoperability/index.md)
    - [Немного C с вашим Rust](./interoperability/c-with-rust.md)
    - [Немного Rust с вашим C](./interoperability/rust-with-c.md)
- [Неклассифицированные темы](./unsorted/index.md)
  - [Оптимизации: компромисс между скоростью и размером](./unsorted/speed-vs-size.md)
  - [Выполнение математических операций](./unsorted/math.md)

---

[Приложение A: Глоссарий](./appendix/glossary.md)
