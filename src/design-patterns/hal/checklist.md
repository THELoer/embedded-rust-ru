# Контрольный список шаблонов проектирования HAL

- **Именование** *(крейт соответствует соглашениям об именовании в Rust)*
  - [ ] Крейт назван правильно ([C-CRATE-NAME])
- **Взаимодействие** *(крейт хорошо взаимодействует с функциональностью других библиотек)*
  - [ ] Типы-обертки предоставляют метод деструктора ([C-FREE])
  - [ ] HAL переэкспортируют свой крейт доступа к регистрам ([C-REEXPORT-PAC])
  - [ ] Типы реализуют трейты `embedded-hal` ([C-HAL-TRAITS])
- **Предсказуемость** *(крейт позволяет писать читаемый код, который работает так, как выглядит)*
  - [ ] Используются конструкторы вместо трейтов расширения ([C-CTOR])
- **Интерфейсы GPIO** *(Интерфейсы GPIO следуют общему шаблону)*
  - [ ] Типы пинов по умолчанию нулевого размера ([C-ZST-PIN])
  - [ ] Типы пинов предоставляют методы для стирания пина и порта ([C-ERASED-PIN])
  - [ ] Состояние пина должно быть закодировано как параметры типа ([C-PIN-STATE])

[C-CRATE-NAME]: naming.html#c-crate-name

[C-FREE]: interoperability.html#c-free
[C-REEXPORT-PAC]: interoperability.html#c-reexport-pac
[C-HAL-TRAITS]: interoperability.html#c-hal-traits

[C-CTOR]: predictability.html#c-ctor

[C-ZST-PIN]: gpio.md#c-zst-pin
[C-ERASED-PIN]: gpio.md#c-erased-pin
[C-PIN-STATE]: gpio.md#c-pin-state
