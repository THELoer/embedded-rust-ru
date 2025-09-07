# Контракты проектирования

В предыдущей главе мы создали интерфейс, который *не* обеспечивал соблюдение контрактов проектирования. Давайте еще раз посмотрим на наш воображаемый регистр конфигурации GPIO:

| Имя          | Бит(ы)        | Значение | Значение        | Примечания |
|--------------|--------------|---------|----------------|-----------|
| enable       | 0            | 0       | отключено      | Отключает GPIO |
|              |              | 1       | включено       | Включает GPIO |
| direction    | 1            | 0       | вход           | Устанавливает направление на вход |
|              |              | 1       | выход          | Устанавливает направление на выход |
| input_mode   | 2..3         | 00      | высокое сопротивление | Устанавливает вход как высокое сопротивление |
|              |              | 01      | подтяжка вниз  | Входной пин подтянут вниз |
|              |              | 10      | подтяжка вверх | Входной пин подтянут вверх |
|              |              | 11      | н/д            | Недопустимое состояние. Не устанавливать |
| output_mode  | 4            | 0       | установить низкий | Выходной пин притянут к низкому уровню |
|              |              | 1       | установить высокий | Выходной пин притянут к высокому уровню |
| input_status | 5            | x       | входное значение | 0, если вход < 1.5 В, 1, если вход >= 1.5 В |

Если вместо этого мы проверяли бы состояние перед использованием базового оборудования, обеспечивая соблюдение наших контрактов проектирования во время выполнения, мы могли бы написать код, который выглядит следующим образом:

```rust,ignore
/// Интерфейс GPIO
struct GpioConfig {
    /// Структура конфигурации GPIO, сгенерированная svd2rust
    periph: GPIO_CONFIG,
}

impl GpioConfig {
    pub fn set_enable(&mut self, is_enabled: bool) {
        self.periph.modify(|_r, w| {
            w.enable().set_bit(is_enabled)
        });
    }

    pub fn set_direction(&mut self, is_output: bool) -> Result<(), ()> {
        if self.periph.read().enable().bit_is_clear() {
            // Для установки направления пин должен быть включен
            return Err(());
        }

        self.periph.modify(|r, w| {
            w.direction().set_bit(is_output)
        });

        Ok(())
    }

    pub fn set_input_mode(&mut self, variant: InputMode) -> Result<(), ()> {
        if self.periph.read().enable().bit_is_clear() {
            // Для установки режима входа пин должен быть включен
            return Err(());
        }
        if self.periph.read().direction().bit_is_set() {
            // Для установки режима входа направление должно быть входным
            return Err(());
        }

        self.periph.modify(|_r, w| {
            w.input_mode().variant(variant)
        });

        Ok(())
    }

    pub fn set_output_mode(&mut self, is_high: bool) -> Result<(), ()> {
        if self.periph.read().enable().bit_is_clear() {
            // Для установки режима выхода пин должен быть включен
            return Err(());
        }
        if self.periph.read().direction().bit_is_clear() {
            // Для установки режима выхода направление должно быть выходным
            return Err(());
        }

        self.periph.modify(|_r, w| {
            w.output_mode().set_bit(is_high)
        });

        Ok(())
    }

    pub fn get_input_status(&self) -> bool {
        self.periph.read().input_status().bit_is_set()
    }
}
```

Теперь давайте используем типовые состояния для кодирования этих состояний в типах, чтобы обеспечить соблюдение контрактов проектирования на этапе компиляции:

```rust,ignore
struct Enabled;
struct Disabled;
struct Input;
struct Output;
struct HighZ;
struct PulledLow;
struct PulledHigh;

struct GpioConfig<E, D, M> {
    periph: GPIO_CONFIG,
    enabled: E,
    direction: D,
    mode: M,
}

impl GpioConfig<Disabled, Input, HighZ> {
    pub fn new(periph: GPIO_CONFIG) -> Self {
        GpioConfig {
            periph,
            enabled: Disabled,
            direction: Input,
            mode: HighZ,
        }
    }

    pub fn into_enabled_input(self) -> GpioConfig<Enabled, Input, HighZ> {
        self.periph.modify(|_r, w| w.enable().set_bit(true));
        GpioConfig {
            periph: self.periph,
            enabled: Enabled,
            direction: Input,
            mode: HighZ,
        }
    }
}

impl GpioConfig<Enabled, Input, HighZ> {
    pub fn bit_is_set(&self) -> bool {
        self.periph.read().input_status().bit_is_set()
    }

    pub fn into_enabled_output(self) -> GpioConfig<Enabled, Output, PulledHigh> {
        self.periph.modify(|_r, w| w.direction().set_bit(true));
        GpioConfig {
            periph: self.periph,
            enabled: Enabled,
            direction: Output,
            mode: PulledHigh,
        }
    }

    pub fn into_input_pull_down(self) -> GpioConfig<Enabled, Input, PulledLow> {
        self.periph.modify(|_r, w| w.input_mode().pull_low());
        GpioConfig {
            periph: self.periph,
            enabled: Enabled,
            direction: Input,
            mode: PulledLow,
        }
    }

    pub fn into_input_pull_up(self) -> GpioConfig<Enabled, Input, PulledHigh> {
        self.periph.modify(|_r, w| w.input_mode().pull_high());
        GpioConfig {
            periph: self.periph,
            enabled: Enabled,
            direction: Input,
            mode: PulledHigh,
        }
    }
}

impl GpioConfig<Enabled, Input, PulledLow> {
    pub fn bit_is_set(&self) -> bool {
        self.periph.read().input_status().bit_is_set()
    }

    pub fn into_enabled_output(self) -> GpioConfig<Enabled, Output, PulledHigh> {
        self.periph.modify(|_r, w| w.direction().set_bit(true));
        GpioConfig {
            periph: self.periph,
            enabled: Enabled,
            direction: Output,
            mode: PulledHigh,
        }
    }

    pub fn into_input_pull_up(self) -> GpioConfig<Enabled, Input, PulledHigh> {
        self.periph.modify(|_r, w| w.input_mode().pull_high());
        GpioConfig {
            periph: self.periph,
            enabled: Enabled,
            direction: Input,
            mode: PulledHigh,
        }
    }
}
```

Теперь давайте посмотрим, как будет выглядеть код, использующий это:

```rust,ignore
/*
 * Пример 1: Из неконфигурированного в вход с высоким сопротивлением
 */
let pin: GpioConfig<Disabled, _, _> = get_gpio();

// Нельзя сделать это, пин не включен!
// pin.into_input_pull_down();

// Теперь переводим пин из неконфигурированного во вход с высоким сопротивлением
let input_pin = pin.into_enabled_input();

// Чтение с пина
let pin_state = input_pin.bit_is_set();

// Нельзя сделать это, входные пины не имеют этого интерфейса!
// input_pin.set_bit(true);

/*
 * Пример 2: Из входа с высоким сопротивлением во вход с подтяжкой вниз
 */
let pulled_low = input_pin.into_input_pull_down();
let pin_state = pulled_low.bit_is_set();

/*
 * Пример 3: Из входа с подтяжкой вниз в выход, установленный на высокий уровень
 */
let output_pin = pulled_low.into_enabled_output();
output_pin.set_bit(true);

// Нельзя сделать это, выходные пины не имеют этого интерфейса!
// output_pin.into_input_pull_down();
```

Этот способ определенно удобен для хранения состояния пина, но почему стоит делать это именно так? Почему это лучше, чем хранить состояние в виде `enum` внутри структуры `GpioConfig`?

## Функциональная безопасность на этапе компиляции

Поскольку мы обеспечиваем соблюдение наших проектных ограничений полностью на этапе компиляции, это не влечет затрат во время выполнения. Невозможно установить режим вывода, когда пин находится в режиме ввода. Вместо этого вы должны пройти через состояния, сначала преобразовав его в выходной пин, а затем установив режим вывода. Благодаря этому отсутствует штраф за проверку текущего состояния перед выполнением функции во время выполнения.

Кроме того, поскольку эти состояния обеспечиваются системой типов, для пользователей этого интерфейса больше нет места для ошибок. Если они попытаются выполнить недопустимый переход состояния, код просто не скомпилируется!
