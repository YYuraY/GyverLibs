# GyverLibs
## Написанные с нуля/модифицированные (AlexGyver) библиотеки для Arduino
### **GyverFilters (текущая 1.1)** - библиотека с некоторыми удобными фильтрами:
+ **GFilterRA** - компактная альтернатива фильтра бегущее среднее (Running Average)
	+ Класс **GFilterRA**
	+ Класс **GFilterRA(coef, step)**
	+ **setCoef(coef)** - настройка коэффициента фильтрации (0.00-1.00)
	+ **setStep (step)** - настройка шага дискретизации (период фильтрации) - встроенный таймер! миллисекунды
	+ **filtered(value)** - возвращает фильтрованное значение
	+ **filteredTime(value)** - возвращает фильтрованное с опорой на встроенный таймер
+ **GMedian3** - быстрый медианный фильтр 3-го порядка (отсекает выбросы)
	+ Класс **GMedian3**
	+ **filtered(value)** - выводит медиану из трёх последних обращений (там свой встроенный массив)
+ **GMedian** - медианный фильтр N-го порядка (отсекает выбросы). Порядок настраивается в GyverFilters.h - **MEDIAN_FILTER_SIZE**
	+ Класс **GMedian**
	+ **filtered(value)** - выводит медиану из N последних обращений (там свой встроенный массив)
+ **GABfilter** - альфа-бета фильтр (разновидность Калмана для одномерного случая)
	+ Класс **GABfilter(delta, sigma_process, sigma_noise)** - период дискретизации (измерений), process variation, noise variation
	+ **setParameters(delta, sigma_process, sigma_noise)** - перенастроить параметры
	+ **filtered(value)** - возвращает фильтрованное значение
+ **GKalman** - упрощённый Калман для одномерного случая (на мой взгляд лучший из фильтров)
	+ Класс **GKalman(разброс измерения, разброс оценки, скорость изменения значений)** - читайте пример
	+ **setParameters(разброс измерения, разброс оценки, скорость изменения значений)** - перенастроить параметры
	+ **filtered(value)** - возвращает фильтрованное значение
### **GyverHacks (текущая 1.4)** - библиотека с некоторыми удобными хаками:
+ **GTimer** - компактная альтернатива конструкции таймера с millis()
	+ Класс **GTimer (period)** - установка периода
	+ **setInterval(period)** - настройка периода вызова
	+ **reset()** - сброс
+ **GParsingStream** - парсинг данных из Serial
	+ parsingStream((int*)&intData) - автоматическая расфасовка пакетов вида **$110 25 600 920;** в массив intData
	+ **dataReady()** - функция-флаг принятия нового пакета данных
	+ sendPacket((int*)&intData, sizeof(intData)) - функция отправки в порт пакета вида **$110 25 600 920;** из массива
+ **Дополнительно** - несколько клёвых удобных функций
	+ **setPWMPrescaler(pin, prescaler)** - установка частоты ШИМ для разных пинов (смотри пример)
	+ **getVCC()** - получить напряжение питания в милливольтах (мВ)
	+ **getVoltage(pin)** - получить напряжение на аналоговом пине с учётом реального питания (мВ)
	+ **getConstant(voltage)** - авто калибровка константы. В функцию подать напряжение питания в мВ (смотри пример)
	+ **setConstant(constant)** - устанвока константы для дальнеёших расчётов
	+ **getTemp()** - получить примерную температуру ядра
### **GyverRGB (текущая 1.3)** - удобное управление RGB светодиодом или лентой. Возможности:
- **Класс GRGB (r_pin, g_pin, g_pin)** - настройка подключения
- **init()** - инициализация
- **setRGB(R, G, B)** - установка цвета в пространстве RGB. R, G, B принимают 0-255
    - **setHSV(H, S, V)** - установка цвета в пространстве HSV (цвет, насыщенность, яркость). Принимают 0-255
- **reverse(true)** - установка реверса ШИМ для LED драйверов
- **setColor(color_hex)** - установка цвета HEX кодом вида 0xFFFFFF
- Несколько предустановленных цветов вида **G_RED**, **G_PINK**
### **GyverMotor (текущая 1.0)** - библиотека для управления моторами через драйвер полного моста. Возможности:
+ Класс **GMotor (dig_pin, pwm_pin)** - указать пины управления. Второй обязательно ШИМ пин!
+ **init()** - инициализация мотора
+ **init10bit(freq)** - инициализация мотора для управления ШИМом через timer1. freq - частота мотора (по умолчанию 20 кГц). pwm_pin должен быть 10 или 11!
+ **setSpeed(duty)** - установить скорость вращения (0 - 255)
+ **setSpeed10bit(duty)** - установить скорость вращения (0 - 1023)
+ **setMode(mode)** - режимы работы: FORWARD - вперёд, BACKWARD - назад, STOP - стоп.
+ **reverse(reverse_mode)** - реверс направления, по умолчанию выключен. В скобках передать true, чтобы изменить направление
### **GyverButton (текущая 2.1)** - библиотека для полной отработки нажатия кнопки. Возможности:
+ Класс **GButton (pin)** - указать пин, куда подключена кнопка (PIN --- КНОПКА --- GND)
+ **setDebounce(time)** - время антидребезга (по умолчанию 80 мс)
+ **setTimeout(time)** - таймаут на удержание/повторное нажатие (по умолчанию 500 мс)
+ **tick()** - опрос кнопки с программным антидребезгом контактов
+ **inverse(state)** - state: true (кнопка подключается к 5V, пин подтянут к GND чере 10 кОм), state: false (кнопка подключена просто к GND)
+ **isPress(), isRelease(), isClick(), isHolded(), isHold()** - отработка нажатия, удерживания отпускания кнопки
+ **isSingle(), isDouble(), isTriple** - отработка одиночного, двойного и тройного нажатия (вынесено отдельно)
+ **getClicks()** - отработка любого количества нажатий кнопки (функция возвращает число нажатий)
+ **isStep()** - возвращает true при удержании, по таймеру. Удобна для управления значением! Смотри пример
+ **setStepTimeout(time)** - настрйока интервала инкремента (по умолчанию 500 мс)
+ Пример использования в папке examples, показывает все возможности библиотеки
+ Отличия от oneBtton и подобных библиотек: методы библиотеки не создают новые функции, что упрощает применение в сотни раз
### **GyverEncoder (текущая 2.0)** - библиотека для отработки энкодера. Возможности:
+ Класс **Encoder (CLK, DT, SW)** - подключение пинов
+ **setType(type)** - установка типа энкодера. 0 - полушаговый, 1 - полношаговый (если энкодер работает через шаг, смени тип!!!)
+ **invert()** - инвертировать направление
+ **tick()** - отработка (опрос)
+ **isTurn(), isRight(), isLeft()** - отработка поворота с антидребезгом
+ **isRightH(), isLeftH()** - отработка поворота с нажатием
+ **isPress(), isRelease(), isHolded(), isHold()** - отработка нажатия кнопки с антидребезгом
+ Отработка "нажатого поворота" - такого вы нигде не найдёте. Расширяет возможности энкодера ровно в 2 раза
+ Пример использования в папке examples, показывает все возможности библиотеки
### **GyverRTOS (текущая 1.0)** - система реального времени для Arduino. Возможности:
- Мы создаём несколько функций с разным периодом выполнения (задачи)
    - Настраиваем период пробуждения системы (минимально 15 мс)  
    Далее всё автоматически:
    - Рассчитывается время до выполнения самой "ближней" задачи
    - Система периодически просыпается и считает таймеры
    - При наступлении времени выполнения ближайшей задачи, она выполняется. После этого снова выполняется расчёт времени до новой ближайшей задачи
    - Как итог: Ардуино спит (в зависимости от периодов) 99.999% времени, просыпаясь только для проверки флага и расчёта таймера
- Смотри пример
### **TM74HC595_Gyver (текущая 1.1)** - библиотека для дисплея на сдвиговике TM74HC595
+ Подробное описание здесь http://alexgyver.ru/tm74hc595_display/
+ Пример использования в папке examples, показывает все возможности библиотеки
### **TM1637_Gyver (текущая 1.1)** - библиотека для дисплея на сдвиговике TM1637
+ Подробное описание здесь http://alexgyver.ru/tm1637_display/
+ Пример использования в папке examples, показывает все возможности библиотеки