# Выполнение математических операций с `#[no_std]`

Если вы хотите выполнять математические операции, такие как вычисление квадратного корня или экспоненты числа, и у вас доступна полная стандартная библиотека, ваш код может выглядеть следующим образом:

```rs
//! Некоторые математические функции с доступной стандартной библиотекой

fn main() {
    let float: f32 = 4.82832;
    let floored_float = float.floor();

    let sqrt_of_four = floored_float.sqrt();

    let sinus_of_four = floored_float.sin();

    let exponential_of_four = floored_float.exp();
    println!("Округлено вниз тестовое число {} до {}", float, floored_float);
    println!("Квадратный корень из {} равен {}", floored_float, sqrt_of_four);
    println!("Синус числа четыре равен {}", sinus_of_four);
    println!(
        "Экспонента числа четыре с основанием e равна {}",
        exponential_of_four
    )
}
```

Без поддержки стандартной библиотеки эти функции недоступны. Вместо этого можно использовать внешний крейт, например [`libm`](https://crates.io/crates/libm). Пример кода в этом случае будет выглядеть так:

```rs
#![no_main]
#![no_std]

use panic_halt as _;

use cortex_m_rt::entry;
use cortex_m_semihosting::{debug, hprintln};
use libm::{exp, floorf, sin, sqrtf};

#[entry]
fn main() -> ! {
    let float = 4.82832;
    let floored_float = floorf(float);

    let sqrt_of_four = sqrtf(floored_float);

    let sinus_of_four = sin(floored_float.into());

    let exponential_of_four = exp(floored_float.into());
    hprintln!("Округлено вниз тестовое число {} до {}", float, floored_float).unwrap();
    hprintln!("Квадратный корень из {} равен {}", floored_float, sqrt_of_four).unwrap();
    hprintln!("Синус числа четыре равен {}", sinus_of_four).unwrap();
    hprintln!(
        "Экспонента числа четыре с основанием e равна {}",
        exponential_of_four
    )
    .unwrap();
    // Выход из QEMU
    // ПРИМЕЧАНИЕ: не запускайте это на оборудовании; это может повредить состояние OpenOCD
    // debug::exit(debug::EXIT_SUCCESS);

    loop {}
}
```

Если вам нужно выполнять более сложные операции, такие как обработка сигналов DSP или продвинутая линейная алгебра на вашем микроконтроллере, следующие крейты могут быть полезны:

- [Привязка к библиотеке CMSIS DSP](https://github.com/jacobrosenthal/cmsis-dsp-sys)
- [`constgebra`](https://crates.io/crates/constgebra)
- [`micromath`](https://github.com/tarcieri/micromath)
- [`microfft`](https://crates.io/crates/microfft)
- [`nalgebra`](https://github.com/dimforge/nalgebra)
