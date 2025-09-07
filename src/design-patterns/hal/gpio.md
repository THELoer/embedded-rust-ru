# Рекомендации для интерфейсов GPIO

<a id="c-zst-pin"></a>
## Типы пинов по умолчанию имеют нулевой размер (C-ZST-PIN)

Интерфейсы GPIO, предоставляемые HAL, должны предоставлять выделенные типы нулевого размера для каждого пина на каждом интерфейсе или порте, обеспечивая абстракцию GPIO с нулевыми накладными расходами, когда все назначения пинов статически известны.

Каждый интерфейс или порт GPIO должен реализовывать метод `split`, возвращающий структуру со всеми пинами.

Пример:

```rust
pub struct PA0;
pub struct PA1;
// ...

pub struct PortA;

impl PortA {
    pub fn split(self) -> PortAPins {
        PortAPins {
            pa0: PA0,
            pa1: PA1,
            // ...
        }
    }
}

pub struct PortAPins {
    pub pa0: PA0,
    pub pa1: PA1,
    // ...
}
```

<a id="c-erased-pin"></a>
## Типы пинов предоставляют методы для стирания пина и порта (C-ERASED-PIN)

Пины должны предоставлять методы стирания типов, которые переводят их свойства из времени компиляции во время выполнения, обеспечивая большую гибкость в приложениях.

Пример:

```rust
/// Порт A, пин 0.
pub struct PA0;

impl PA0 {
    pub fn erase_pin(self) -> PA {
        PA { pin: 0 }
    }
}

/// Пин на порте A.
pub struct PA {
    /// Номер пина.
    pin: u8,
}

impl PA {
    pub fn erase_port(self) -> Pin {
        Pin {
            port: Port::A,
            pin: self.pin,
        }
    }
}

pub struct Pin {
    port: Port,
    pin: u8,
    // (эти поля могут быть упакованы для уменьшения занимаемой памяти)
}

enum Port {
    A,
    B,
    C,
    D,
}
```

<a id="c-pin-state"></a>
## Состояние пина должно быть закодировано в параметрах типа (C-PIN-STATE)

Пины могут быть настроены как вход или выход с различными характеристиками в зависимости от микросхемы или семейства. Это состояние должно быть закодировано в системе типов, чтобы предотвратить использование пинов в некорректных состояниях.

Дополнительное, специфичное для микросхемы состояние (например, сила тока) также может быть закодировано таким образом с использованием дополнительных параметров типа.

Методы для изменения состояния пина должны предоставляться как `into_input` и `into_output`.

Кроме того, должны быть предоставлены методы `with_input_state` и `with_output_state`, которые временно изменяют состояние пина.

Пример:

```rust
# use std::marker::PhantomData;
mod sealed {
    pub trait Sealed {}
}

pub trait PinState: sealed::Sealed {}
pub trait OutputState: sealed::Sealed {}
pub trait InputState: sealed::Sealed {
    // ...
}

pub struct Output<S: OutputState> {
    _p: PhantomData<S>,
}

impl<S: OutputState> PinState for Output<S> {}
impl<S: OutputState> sealed::Sealed for Output<S> {}

pub struct PushPull;
pub struct OpenDrain;

impl OutputState for PushPull {}
impl OutputState for OpenDrain {}
impl sealed::Sealed for PushPull {}
impl sealed::Sealed for OpenDrain {}

pub struct Input<S: InputState> {
    _p: PhantomData<S>,
}

impl<S: InputState> PinState for Input<S> {}
impl<S: InputState> sealed::Sealed for Input<S> {}

pub struct Floating;
pub struct PullUp;
pub struct PullDown;

impl InputState for Floating {}
impl InputState for PullUp {}
impl InputState for PullDown {}
impl sealed::Sealed for Floating {}
impl sealed::Sealed for PullUp {}
impl sealed::Sealed for PullDown {}

pub struct PA1<S: PinState> {
    _p: PhantomData<S>,
}

impl<S: PinState> PA1<S> {
    pub fn into_input<N: InputState>(self, input: N) -> PA1<Input<N>> {
        todo!()
    }

    pub fn into_output<N: OutputState>(self, output: N) -> PA1<Output<N>> {
        todo!()
    }

    pub fn with_input_state<N: InputState, R>(
        &mut self,
        input: N,
        f: impl FnOnce(&mut PA1<N>) -> R,
    ) -> R {
        todo!()
    }

    pub fn with_output_state<N: OutputState, R>(
        &mut self,
        output: N,
        f: impl FnOnce(&mut PA1<N>) -> R,
    ) -> R {
        todo!()
    }
}

// То же самое для `PA` и `Pin`, и других типов пинов.
```
