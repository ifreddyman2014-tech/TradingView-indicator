# HTF OB M30 + LTF M1 Entry System - TradingView Indicator

## Описание

Профессиональный индикатор Pine Script v5 для торговли на Forex (оптимизирован для GBPUSD), реализующий стратегию "HTF Order Block M30 + LTF M1 Entry".

### Основная концепция

Индикатор использует мультитаймфреймовый анализ:
- **HTF (M30)**: Определение Order Block зон и общего направления тренда
- **LTF (M1)**: Точный вход по Break of Structure (BOS), displacement и реакции на Order Block или FVG

## Установка

1. Откройте TradingView
2. Нажмите на график → Pine Editor (внизу экрана)
3. Создайте новый индикатор (New → Blank indicator script)
4. Скопируйте весь код из файла `HTF_OB_LTF_Entry_Indicator.pine`
5. Вставьте в редактор
6. Нажмите "Save" и "Add to Chart"

## Рекомендуемые настройки

### Таймфрейм графика
**ВАЖНО**: Запускайте индикатор на **M1 (1-minute)** графике для максимальной точности LTF логики.

### Символ
- Оптимизирован для: **GBPUSD**
- Работает на любых Forex парах, но параметры displacement могут требовать калибровки

### Начальные параметры (Input Settings)

#### HTF Settings
- **HTF Timeframe**: `30` (M30)
- **HTF Pivot Length**: `3-5` (меньше = быстрее реакция, больше = меньше ложных сигналов)
- **OB Zone Mode**: `Wick` (использовать весь диапазон свечи) или `Body` (только тело)

#### LTF Settings
- **LTF Pivot Length**: `3` (для M1 графика)

#### Displacement Settings
- **Displacement Body Multiplier**: `1.8` (агрессивность: 1.5-2.0)
- **Body Average Lookback**: `20`
- **Displacement ATR Multiplier**: `1.5`
- **ATR Length**: `14`
- **Close Position Threshold**: `0.6` (0.6 = закрытие в верхних/нижних 60% диапазона)

#### BOS Settings
- **BOS Buffer (pips)**: `2.0` (для GBPUSD: 2-3 pips оптимально)

#### Entry Logic
- **Use LTF OB Entry**: `✓` (включено)
- **Use FVG Entry**: `✓` (включено)

#### Risk Management
- **SL Method**: `Swing` (использовать swing low/high) или `OB` (использовать границу OB)
- **TP1 (R)**: `1.0` (первая цель - 1:1 risk/reward)
- **TP2 (R)**: `2.0`
- **TP3 (R)**: `3.0`
- **Partial Close % at TP1**: `50%` (закрыть половину позиции на TP1)

#### Visualization
- Все галочки включены для обучения. После освоения можно отключить лишние элементы.

## Как читать индикатор

### Зоны (Boxes)

1. **M30 Bull/Bear OB** (зелёный/красный, полупрозрачный)
   - Зоны Higher Timeframe Order Block
   - Расширяются вправо (extend right)
   - Это главные зоны интереса

2. **LTF Bull/Bear OB** (синий/оранжевый)
   - Order Block на Low Timeframe (M1)
   - Формируются после displacement
   - Используются для точного входа (retest)

3. **FVG** (голубой/фиолетовый)
   - Fair Value Gap (imbalance)
   - Альтернативная зона входа после BOS

### Метки (Labels)

- **"In HTF OB"**: Цена вошла в зону HTF Order Block
- **"Displacement"**: Обнаружена импульсная свеча (displacement candle)
- **"BOS"**: Подтверждён Break of Structure на LTF
- **"BUY" / "SELL"** (с параметрами): Потенциальная точка входа

### Стрелки

- **Зелёный треугольник вверх**: BUY сигнал
- **Красный треугольник вниз**: SELL сигнал

### Линии

- **Красная**: Stop Loss
- **Жёлтая**: TP1 (partial close)
- **Оранжевая**: TP2
- **Зелёная**: TP3

## Логика торговли (пошагово)

### Для LONG позиции:

1. **Ждём HTF Bull OB**
   - На M30 произошёл bullish BOS (пробой предыдущего swing high)
   - Определена зона последней медвежьей свечи перед импульсом

2. **Цена возвращается в HTF OB на M1**
   - Появляется метка "In HTF OB"

3. **Ищем Displacement на M1**
   - Сильная бычья свеча внутри HTF OB
   - Метка "Displacement"

4. **Подтверждение через LTF BOS**
   - Цена пробивает последний swing high на M1 с буфером
   - Метка "BOS"

5. **Формирование зоны входа**
   - Появляется LTF Bull OB (синяя зона) или FVG

6. **Ретест и вход**
   - Цена возвращается в LTF OB или FVG
   - Появляется подтверждающая свеча:
     * Bullish engulfing
     * Сильное бычье закрытие (close в верхних 70% диапазона)
   - **Появляется BUY стрелка** ✓

7. **Управление позицией**
   - Entry: по close сигнальной свечи
   - SL: ниже swing low или ниже LTF OB (по настройке)
   - TP1: закрыть 50% на 1R
   - TP2/TP3: hold оставшиеся 50%

### Для SHORT позиции:

Аналогично, но в обратном направлении:
- HTF Bear OB на M30
- Bearish displacement
- Bearish BOS
- Retest LTF Bear OB/FVG
- Bearish подтверждение

## Настройка алертов

1. Кликните правой кнопкой на индикатор → "Add alert on HTF OB LTF Entry System"
2. Выберите условие:
   - **"Price Enters HTF Bull/Bear OB"**: уведомление при входе в HTF зону
   - **"Bullish/Bearish Displacement Detected"**: обнаружен импульс
   - **"Bullish/Bearish BOS Confirmed"**: подтверждён BOS
   - **"BUY/SELL Entry Signal"**: ГЛАВНЫЙ алерт - готовый сетап для входа

3. Настройте:
   - Options → "Once Per Bar Close" (чтобы избежать repaint)
   - Expiration time
   - Alert actions (notification, email, webhook)

## Важные замечания

### ⚠️ Repaint

**Pivot Points** могут перерисовываться!
- `ta.pivothigh/ta.pivotlow` требуют N баров справа для подтверждения
- В коде используется подтверждённые pivot'ы (lookback = pivot_length)
- **Решение**: алерты срабатывают только при закрытии бара ("Once Per Bar Close")

**Индикатор НЕ repaint-ит сигналы входа** - они формируются по закрытию свечи.

### 📊 Фильтры качества

Индикатор строгий и НЕ "стреляет в воздух":
- Entry сигнал появляется только при совпадении ВСЕХ условий
- Если сигналов слишком много → увеличьте параметры displacement (body multiplier, ATR multiplier)
- Если сигналов мало → уменьшите BOS buffer, уменьшите displacement thresholds

### 🎯 Оптимизация под конкретную пару

Для волатильных пар (GBP, XAU):
- Увеличьте BOS buffer (3-5 pips)
- Увеличьте displacement multipliers (2.0-2.5)

Для спокойных пар (EUR/CHF):
- Уменьшите BOS buffer (1-2 pips)
- Уменьшите displacement multipliers (1.5-1.8)

### 💡 Best Practices

1. **Не торгуйте вслепую**: изучите логику, наблюдайте за индикатором на истории
2. **Используйте управление капиталом**: риск 1-2% на сделку
3. **Учитывайте сессии**: GBPUSD активен в London/NY sessions
4. **Фильтруйте по новостям**: избегайте входов перед NFP, CPI и т.д.
5. **Backtesting**: протестируйте на истории, но помните о pivot repaint (используйте bar replay)

## Кастомизация визуализации

Если график загроможден:

1. **Отключите HTF OB boxes** → Settings → Visualization → Show HTF OB Zones = `OFF`
   (зоны всё равно работают, просто не видны)

2. **Отключите метки** → Show Labels = `OFF`
   (оставьте только стрелки входов)

3. **Скройте FVG/LTF OB** → Show FVG Zones = `OFF`, Show LTF OB Zones = `OFF`

4. **Измените цвета** → В коде строки типа `color.new(color.green, 90)`:
   - Первый параметр = цвет
   - Второй параметр = прозрачность (0-100, где 100 = полностью прозрачный)

## Поддержка и обратная связь

Индикатор создан как образовательный инструмент для изучения Smart Money Concepts (SMC) и ICT методологии.

**Не является торговой рекомендацией**. Используйте на свой риск.

---

## Краткая памятка для трейдера

### Идеальный сетап LONG:

```
1. ✅ HTF Bull OB сформирован на M30
2. ✅ Цена вернулась в зону HTF OB (метка "In HTF OB")
3. ✅ Displacement вверх на M1 (метка "Displacement")
4. ✅ BOS вверх на M1 (метка "BOS")
5. ✅ Сформирован LTF Bull OB или FVG (синяя зона)
6. ✅ Цена вернулась в LTF зону (retest)
7. ✅ Подтверждающая бычья свеча
8. 🎯 BUY стрелка + метка с параметрами

→ ВХОД по close свечи
→ SL ниже swing low / LTF OB
→ TP1 @ 1R (закрыть 50%)
→ TP2 @ 2R, TP3 @ 3R (hold 50%)
```

### Идеальный сетап SHORT:

```
Аналогично, но bearish направление
```

---

**Успешной торговли! 📈**
