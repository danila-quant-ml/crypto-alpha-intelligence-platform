# Crypto Alpha Intelligence Platform

**Private/local AI/ML система рыночной аналитики для BTC.**

Локальный трейдерский аналитический кабинет, который объединяет авторские ML-модели, time-series feature engineering, live market data infrastructure, order-flow аналитику, options-flow контекст, news/on-chain слои, LLM-аналитику, SQLite logging, dashboard UI и outcome monitoring.

Это не trading bot, не публичный demo и не notebook-only research проект.
Это end-to-end AI/ML система, которая превращает рыночные данные в структурированный, логируемый и проверяемый рыночный интеллект.


<br>

</p>
<p align="center">
  <img src="docs/assets/Главная_1.png" alt="Главный экран dashboard" width="900">
</p>

<p align="center">
  <em>Главный экран локального трейдерского кабинета: модели, market context, signals и system health.</em>
</p>


<br>





## Что демонстрирует проект

* Авторские ML-модели для определения рыночного режима BTC и directional context.
* Строгий no-leakage подход и cutoff-based feature engineering.
* Walk-forward / pretest validation дисциплину.
* Runtime inference с зафиксированными feature lists и thresholds.
* SQLite-based signal logging, model runs, outcomes и system events.
* Live futures/order-flow infrastructure через Bybit WebSocket collector.
* WebSocket Live Chart через VPS service и SSH tunnel.
* Options-flow / Hiro-like material state layer.
* Options Zones, Options Pressure и Flow near Zones.
* Manual Zones и Zone Reaction Engine с review/outcomes.
* News, on-chain и LLM Analyst context layers.
* Read-only dashboard для операторского workflow.
* Makefile-based daily workflow: session open/close, evidence update, backup.
* Unittest/smoke-test покрытие model, storage, dashboard, flow, LLM и review слоев.



<br>

</p>
<p align="center">
  <img src="docs/assets/График_1.png" alt="Главный экран dashboard" width="900">
</p>

<p align="center">
  <em>WebSocket Chart</em>
</p>


<br>


## Ядро ML

**Система разделяет два разных рыночных вопроса:**

```text
Expansion Model
→ Похож ли текущий день на expansion day или balanced/range day?

Direction Model
→ Если рыночный контекст активен, какой directional bias вероятнее?
```

Такое разделение делает систему более интерпретируемой и ближе к реальному рабочему процессу.

**Expansion Model** - работает как фильтр рыночного режима.

**Direction Model** - работает как отдельный слой directional context.

## Кратко: метрики

| Модель                  | Метрики                                                                      |
| ----------------------- | ---------------------------------------------------------------------------- |
| Expansion STRONG tier   | precision = 0.9667, worst-fold precision = 0.9000, pooled precision = 0.9600 |
| Expansion MEDIUM tier   | precision = 0.8216, recall = 0.7294, pooled recall = 0.7031                  |
| Direction/Profile layer | mean balanced accuracy = 0.8078, mean macro F1 = 0.7377                      |
| News Macro Model        | holdout precision = 90.5%, PR-AUC = 0.8247, ROC-AUC = 0.7893                 |

**Проект делает акцент на честной validation-дисциплине и защите от подгонки под историю:**

**Главный принцип проекта:**

```text
- без будующих свечей
- без утечек после точки предикта
- без признаков из будущего
- без бесконечного feature mining ради красивой метрики
- без ручной подгонки thresholds под holdout
- без смешивания research-гипотез с operational-логикой
- без оптимизации признаков и поиска лучшей комбинации

Переоптимизация = 0

- зафиксированный список признаков рыночного контекста
- зафиксированный порог без подгонки 
- все признаки создаются строго до cutoff
- walk-forward / pretest checks
- separate live-forward monitoring
```

Метрики в проекте не получены через искусственную оптимизацию на истории.
Я не строил модель по принципу “перебирать признаки, пока backtest не станет красивым”.

Признаки отражают только тот рыночный контекст, который реально доступен до момента принятия решения: структура движения до cutoff, волатильность, положение цены относительно важных уровней, intraday context и pre-cutoff рыночная микроструктура.

В рабочем контуре модель получает тот же тип вектора признаков, который был доступен в реальном времени. Список признаков и порог зафиксированы, порядок признаков проверяется, а новые исследовательские идеи не переносятся в логику без отдельной проверки.

## Архитектура верхнего уровня

```text
Рыночные данные
→ Проверка качества данных
→ Построение признаков
→ Упакованные model packages
→ Runtime inference
→ Логирование сигналов
→ Сборка Market Context
→ Review / Outcomes
→ Read-only Dashboard
→ Operator Workflow
```

**Дополнительные аналитические слои:**

```text
Futures Order Flow
Options Flow
Options Zones
News Intelligence
On-chain Context
LLM Analyst
Manual Zones
Zone Reaction Engine
```

Идея архитектуры в том, что каждый слой отвечает за отдельную задачу: данные сначала проходят quality checks, затем превращаются в признаки, модели запускаются в контролируемом контуре, результаты сохраняются в SQLite, после чего dashboard показывает состояние системы, а review/outcome слои позволяют проверить, что произошло после сигналов.

Dashboard остается read-only: он показывает состояние рынка, моделей и контекстных слоев, но не выполняет скрытых write-действий и не ломает operational workflow.


## Что внутри системы

### ML / Alpha Research

* Expansion Model для определения рыночного режима.
* Direction/Profile Model для directional bias.
* News Macro Model для оценки macro/news expansion risk.
* No-leakage feature engineering.
* Cutoff-based time-series validation.
* Frozen feature lists, thresholds и model package checks.
* Historical replay и live-forward monitoring.

### Live Market Data Infrastructure

* Bybit WebSocket futures/order-flow collector.
* Raw trades, BBO, 1s / 5s / 1m aggregations.
* CVD, delta volume, VWAP, high/low/close.
* Compact SQLite export с VPS.
* Local read-only flow reader.
* WebSocket Live Chart через SSH tunnel.

### Derivatives / Options Intelligence

* Hiro-like options-flow material state layer.
* ACTIVATING / CONFIRMED / FAILED_ACTIVATION / CANCELLED states.
* Options Pressure snapshots.
* Options Zones and call/put walls.
* Flow near Zones.
* Zone Reaction Engine.
* Trap / absorption / big trades context.

### Dashboard / Operator Experience

* Read-only local dashboard.
* Latest Signal.
* Session Desk.
* Market Context / Final Bias.
* Expansion / Direction / News model pages.
* Hiro / Options Flow.
* On-chain.
* LLM Analyst.
* Forecast Evidence.
* Context Review.
* Zone Review.
* Browser Alerts.
* System Health.

### Review / Feedback Loop

Система не просто сохраняет сигнал, а проверяет, что произошло после него:

```text
+30m
+60m
to 12:00 NY
to 16:00 NY
MFE
MAE
realized range
```

Outcome/review слои позволяют анализировать:

```text
- какие сигналы были
- какие модели подтверждали друг друга
- где был конфликт
- что произошло после сигнала
- какие зоны отработали
- какие режимы полезны
- где модель ошибалась
```

## Stack

```text
Python
Pandas
NumPy
CatBoost
Scikit-learn
SQL
SQLite
Streamlit / FastAPI-style dashboard
WebSocket
REST API
Linux
Bash
Makefile
Git
Unit Testing
LLM Engineering
OpenAI API
Bybit / Deribit market data
Time-series validation
MLOps-style workflow
```

## Подробное описание

Полное описание проекта, архитектуры, моделей, метрик, validation-подхода, dashboard и live workflow:  
[Full Project Case](docs/full_project_case_study.md)