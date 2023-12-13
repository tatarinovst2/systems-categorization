# Классификация суицидальных текстов с помощью нейросетевых технологий

## Данные

За основу разработки алгоритма для классификации брался датасет с платформы Kaggle. 
Данный датасет вы можете найти [тут](https://www.kaggle.com/datasets/nikhileswarkomati/suicide-watch)

**Исходный датасет содержит:**

1. 232 074 текстов (содержатся в колонке text), 166.9 MB;
2. Каждый текст имеет метку “suicide” либо “non-suicide” (содержатся 
   в колонке class).

Датасет сбалансированный и не содержит дубликатов и значений NaN.
Тексты не лемматизированы, не токенизированы.

Для дальнейшей работы необходимо преобразовать тексты.

## Этап I: подготовка данных для задачи тематического моделирования

### Шаг 1: Уменьшение количества данных

Для более быстрого обучения моделей классификации из 232 074 текстов оставлены 
30 000 текстов.

### Шаг 2: Препроцессинг текстов 

* Тексты очищаются от ссылок и от всех символов, кроме букв, цифр и пробельных 
  символов с помощью регулярных выражений;
* Тексты приводятся к нижнему регистру;
* Кодируются лейблы (“non-suicide” как 0, “suicide” как 1).

### Шаг 3: Токенизация текстов

В данной работы мы сравнивали несколько способов токенизации. Мы использовали 
следующие инструменты:

* Razdel
* Регулярные выражения
* Bert
* NLTK

Однако для дальнейшей работы был выбран NLTK токенизатор, так как именно 
данный инструмент совершил наименьшее количество ошибок на данном наборе текстов.

### Шаг 4: Лемматизация текстов

Для лемматизации, как и для токенизации, мы использовали несколько инструментов:

* Simplemma
* Spacy
* NLTK

В итоге для работы была выбрана библиотека Spacy, так как данная библиотека наиболее 
точно лемматицировала тексты.

### Шаг 5: Векторизация 

Векторизация производилась с помощью Tf-Idf векторизатора.

## Этап II: подготовка данных для задачи бинарной классификации и мультиклссовой классификации

### Шаг 6: Уменьшение количества данных

Для задачи бинарной классификации мы используем 
[30 000 текстов](./Suicide_Detection_30000.csv).
Для задачи мультиклассовой классификации используется датасет из 
[15 000 текстов](./Suicide_Detection_with_topics_balanced.csv). 

### Шаг 7: Препроцессинг текстов 

* Тексты очищаются от ссылок и от всех символов, кроме букв, цифр и пробельных 
  символов с помощью регулярных выражений;
* Тексты приводятся к нижнему регистру;
* Удаляются стоп слова

### Шаг 8: Токенизация и лемматизация текстов

В задачи бинарной классификации для токенизации и лемматизации 
мы использовали библиотеку NLTK.

### Шаг 9: Векторизация 

Векторизация производилась с помощью модели `all-MiniLM-L6-v2` библиотеки
sentence-transformers.

## Этап III: алгоритм тематического моделирования

### Шаг 10: Тематическое моделирование

Тематическое моделирование производилось 4 способами:

* NMF
* Gensim LDA
* LDA
* LDA ARTM

Алгоритм LDA ARTM наиболее точно разделил тексты на 4 группы. Однако для того, 
чтобы создать классификатор, позволяющий определять тематику суицидных текстов, 
необходимо конкретизировать и расширить некоторые темы. Для этого необходимо 
было произвести ручную корректировку.

## Этап IV: разметка

### Шаг 11: Разметка данных

В результате дополнительной разметки данных, каждому тексту была присвоена 
одна из следующих меток:

`0` - просьбы о помощи, просьбы поговорить;

`1` - ненависть к себе, собственная ущербность, неполноценность;

`2` - психические заболевания;

`3` - наркотики, алкоголь, зависимости;

`4` - семья;

`5` - любовь, отношения;

`6` - учёба, работа;

`7` - самоповреждения, анорексия, булимия

## Этап V: нейронная сеть

### Шаг 12: Обучение модели бинарной классификации 

Модель написана с использованием библиотеки `PyTorch`. 

В модели 3 слоя:

* первый слой содержит 64 нейрона, в качестве функции активации используется функция ReLU;
* второй слой содержит 32 нейрона, в качестве функции активации используется функция ReLU;
* на выходном слое мы получаем 1 нейрон, в качестве функции активации используется Sigmoid.

Точность данной модели - **92%**

### Шаг 13: Обучение модели мультиклассовой классификации

Данная модель также написана на `PyTorch` и имеет 3 слоя:

* первый слой содержит 64 нейрона, в качестве функции активации используется функция ReLU;
* второй слой содержит 32 нейрона, в качестве функции активации используется функция ReLU;
* на выходном слое мы получаем 8 меток классов.

Точность данной модели - **53%**

## Где можно посмотреть результаты работы?

Вы можете посмотреть результаты нашей работы в открытом репозитории на 
[Github](https://github.com/tatarinovst2/systems-categorization).

Вы также можете клонировать наш репозиторий для локальной работы, предложить 
свои улучшения или создать issues. Для работы вам необходимо скачать либо исходный датасет 
с платформы Kaggle, либо воспользоваться подготовленным нами датасетами 
([скачать здесь](./Suicide_Detection_30000.csv)
для тематического моделирования и бинарной классификации, 
[скачать здесь](./Suicide_Detection_with_topics_balanced.csv)
для мультиклассовой классификации).

Обученную модель для задачи бинарной классификации вы можете найти 
[здесь](./Binary_Text_classification_(Systems).ipynb).
Если вас заинтересовала задача мультиклассовой классификации код вы можете
найти [тут](./Multi_class_text_classification_(Systems).ipynb).

Мы будем рады увидеть ваши улучшения в pull requests!