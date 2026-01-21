## Комп'ютерні системи імітаційного моделювання
## СПм-24-1, **Яровий Валерій Вікторович**
### Лабораторна робота №**2**. Редагування імітаційних моделей у середовищі NetLogo


<br>

### Варіант 8 - Модель досліджує прагнення "групуватися" із "своїми" і толерантність до чужаків.
[Segregation Simple Extension 1](https://www.netlogoweb.org/launch#http://www.netlogoweb.org/assets/modelslib/IABM%20Textbook/chapter%203/Segregation%20Extensions/Segregation%20Simple%20Extension%201.nlogo)


Додати можливість агенту зникнути, вірогідність чого залежить від кількості наявних поряд з ним "чужих" і кількості представників їхньої групи у їх власному оточенні. Виводити графік зміни розміру популяції.

### Зміни у логіці роботи моделі згідно варіанту


### 1. Нові змінні
У моделі додано нову змінну для кожного агента:

- **vanish-prob** (turtles-own) — ймовірність зникнення агента на даному кроці.


### 2. Зміни в поведінці агентів
Тепер агенти не тільки переміщуються, але й можуть зникати залежно від оточення:

- Для кожного агента рахується кількість сусідів:
  - **same** — кількість сусідів того ж кольору
  - **total** — загальна кількість сусідів
  - **different** — кількість сусідів іншого кольору

- Визначається частка чужих сусідів:
  - `diff-share = different / total`

- Для кожного чужого сусіда визначається, наскільки він оточений своїми:
  - `foreign-strength` — середня частка “своїх” у сусідів іншого кольору

- Імовірність зникнення агента обчислюється за формулою: `vanish-prob = diff-share * foreign-strength`

<pre>
to update-vanish-prob
  ask turtles [
    let same count (turtles-on neighbors) with [ color = [color] of myself ]
    let total count turtles-on neighbors
    let different total - same

    if total = 0 [
      set vanish-prob 0
      stop
    ]

    let diff-share different / total
    let foreign-strength 0

    let foreign-neighbors turtles-on neighbors

    ask foreign-neighbors [
      if color != [color] of myself [
        let my-color color
        let my-same count (turtles-on neighbors) with [ color = my-color ]
        let my-total count turtles-on neighbors
        if my-total > 0 [
          set foreign-strength foreign-strength + (my-same / my-total)
        ]
      ]
    ]

    if different > 0 [
      set foreign-strength foreign-strength / different
    ]

    set vanish-prob diff-share * foreign-strength
  ]
end



to vanish-turtles
  ask turtles [
    if random-float 1 < vanish-prob [
      die
    ]
  ]
end
</pre>

### 4. Графік популяції
Додано графік **Population**, який відображає чисельність агентів у часі:

- **вісь X** — ticks
- **вісь Y** — кількість агентів (turtles)

Графік оновлюється в кінці кожного tick.


![alt text](image.png)