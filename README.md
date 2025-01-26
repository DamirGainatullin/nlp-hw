### Описание решения

#### 1. **Исследование данных (EDA)**  
- В ходе анализа данных был выявлен явный дисбаланс классов, где один из классов был представлен значительно меньшим числом примеров.  
- Пропусков в данных не было.  
- Были добавлены дополнительные признаки:
  - Количество слов в твите.
  - Количество символов в твите.
  - **Тональность твита**, рассчитанная с помощью предобученной модели **rubert-base-cased-sentiment**, так как более лёгкие модели (TextBlob и Vader) показывали низкую точность классификации.  

#### 2. **Нормализация и эмбединги текста**
- Для нормализации и лемматизации текста использовались библиотеки **pymorphy2** и **nltk**
- Для токенизации **word_tokenize**  
- Для представления текстовых данных использовались **fastText эмбединги размерностью 300**.  

#### 3. **Борьба с дисбалансом классов**  
- Для борьбы с дисбалансом классов использовался метод **SMOTE (Synthetic Minority Over-sampling Technique)**, чтобы увеличить количество примеров меньшего класса, генерируя новые точки на основе соседей в пространстве признаков.  
- SMOTE применялся только для **Random Forest**, но не для **LSTM**, так как LSTM имеет встроенные механизмы работы с дисбалансом при использовании `sample_weight`.

#### 4. **Модель Random Forest**  
- Для обучения была выбрана модель **Random Forest**.  
- С помощью **кросс-валидации** были найдены **оптимальные гиперпараметры**

#### 5. **Модель LSTM**  
- Также была использована модель **LSTM** для сравнения.  
- Архитектура модели: 
  - Эмбединги текста подавались на два последовательных слоя LSTM.
  - Дополнительные признаки (количество слов, символов и тональность) подавались в Dense-слой отдельно и затем объединялись с выходом LSTM.
  - Финальный слой с сигмоидной активацией выполнял бинарную классификацию.
- Для борьбы с дисбалансом использовался `sample_weight`, который давал больший вес примерам класса меньшинства.  
- Для улучшения результатов LSTM:
  - Добавлен дополнительный полносвязный слой.
  - Увеличено количество эпох обучения с использованием **Early Stopping** для предотвращения переобучения.

#### 6. **Сравнение результатов**  
- Несмотря на доработки LSTM, **Random Forest** показал лучшие результаты:  
  - Более высокая F1-метрика.
  - Лучшая интерпретируемость и стабильность результатов.  

#### 7. **Финальное обучение**  
- Для финальной версии модели тренировочная выборка (`train`) и валидационная выборка (`val`) были объединены.  
- Обучение Random Forest выполнялось на объединённых данных с оптимальными гиперпараметрами, найденными с помощью кросс-валидации.  
