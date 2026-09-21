# Darkwood Secret Python (education case)
Команда игры «Секреты Темнолесья» хотят привлечь новую аудиторию и подготовить статью о развитии индустрии игр в начале XXI века. Для статьи понадобится изучить развитие игровой индустрии с 2000 по 2013 год. Задача — познакомиться с данными, проверить их корректность и провести предобработку, получив необходимый срез данных.

**Цель проекта** - проанализировать датасет /datasets/new_games.csv, который содержит информацию о продажах игр разных жанров и платформ, а также пользовательские и экспертные оценки игр, и выявить ключевые закономерности.

**Задача проекта** - познакомится с данными, проверить их корректность и провести предобработку, получив необходимый срез данных. Кроме того, необходимо категоризовать игры по оценкам пользователей и экспертов, а также выделить топ-7 платформ по количеству игр, выпущенных за весь требуемый период.


## Описание данных

Данные `/datasets/new_games.csv` содержат информацию о продажах игр разных жанров и платформ, а также пользовательские и экспертные оценки игр:
- `Name` — название игры.
- `Platform` — название платформы.
- `Year of Release` — год выпуска игры.
- `Genre` — жанр игры.
- `NA sales` — продажи в Северной Америке (в миллионах проданных копий).
- `EU sales` — продажи в Европе (в миллионах проданных копий).
- `JP sales` — продажи в Японии (в миллионах проданных копий).
- `Other sales` — продажи в других странах (в миллионах проданных копий).
- `Critic Score` — оценка критиков (от 0 до 100).
- `User Score` — оценка пользователей (от 0 до 10).
- `Rating` — рейтинг организации ESRB (англ. Entertainment Software Rating Board). Эта ассоциация определяет рейтинг компьютерных игр и присваивает им подходящую возрастную категорию.

## Загрузка и знакомство с данными

Датасет `/datasets/new_games.csv` содержит 11 столбцов и 16956 строк, в которых находится информацию о продажах игр разных жанров и платформ, а также пользовательские и экспертные оценки игр. Названия столбцов отражают содержимое данных, однако прописаны в неудобном для работы виде.

```python
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 16956 entries, 0 to 16955
Data columns (total 11 columns):
 #   Column           Non-Null Count  Dtype  
---  ------           --------------  -----  
 0   Name             16954 non-null  object 
 1   Platform         16956 non-null  object 
 2   Year of Release  16681 non-null  float64
 3   Genre            16954 non-null  object 
 4   NA sales         16956 non-null  float64
 5   EU sales         16956 non-null  object 
 6   JP sales         16956 non-null  object 
 7   Other sales      16956 non-null  float64
 8   Critic Score     8242 non-null   float64
 9   User Score       10152 non-null  object 
 10  Rating           10085 non-null  object 
dtypes: float64(4), object(7)
memory usage: 1.4+ MB
```
Изучим типы данных и их корректность:

- **Числовые значения с плавающей запятой (float64).** Четыре столбца имеют тип данных float64:
   - Столбец, `Year of Release`, содержит год выпуска игры. Для таких данных рекомендуется использовать тип int64.
   - `NA sales`, `Other sales` - содержат продажи в Северной Америке и других странах (в миллионах проданных копий). Данные величины часто бывают дробными, следовательно, float64 здесь уместен.
   - Столбец,`Critic Score`, содержит оценку критиков(от 0 до 100). Для таких данных рекомендуется использовать тип int64.
- **Строковые данные (object).** Семь столбцов имеют тип данных object:
   - `Name`, `Platform` и `Genre` содержат строковую информацию (название игры, название платформы и жанр), что логично для текстовых данных. Здесь тип данных object подходит.
   - `EU sales` и `JP sales` хранят информацию о продажах в Европе и в Японии (в миллионах проданных копий). Для таких данных рекомендуется использовать тип float64.
   - Столбец `User Score` содержит оценку пользователей (от 0 до 10), представленную не целочисленными значениями, а значит, здесь уместен тип данных float64.
   - `Rating` также хранит текстовые данные, но их можно рассматривать как категориальные признаки. В этом случае можно использовать тип category, чтобы улучшить производительность и оптимизировать память, если набор значений ограничен и заведомо известен.
После анализа видно, что более 50% столбцов нуждаются в преобразовании к рекомендуемым типам данных.

| Name | Platform | Year of Release | Genre | NA sales | EU sales | JP sales | Other sales | Critic Score | User Score | Rating |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Wii Sports | Wii | 2006 | Sports | 41.36 | 28.96 | 3.77 | 8.45 | 76.0 | 8 | E |
| Super Mario Bros. | NES | 1985 | Platform | 29.08 | 3.58 | 6.81 | 0.77 | NaN | NaN | NaN |
| Mario Kart Wii | Wii | 2008 | Racing | 15.68 | 12.76 | 3.79 | 3.29 | 82.0 | 8.3 | E |
| Wii Sports Resort | Wii | 2009 | Sports | 15.61 | 10.93 | 3.28 | 2.95 | 80.0 | 8 | E |
| Pokemon Red/Pokemon Blue | GB | 1996 | Role-Playing | 11.27 | 8.89 | 10.22 | 1.00 | NaN | NaN | NaN |

## Проверка ошибок в данных и их предобработка

```python
# Выводим на экрам список столбцов
games.columns
# Приведем данные к нижнему регистру
games.columns = games.columns.str.lower()
games.columns
# Приведем название столбцов к стилю snake case, заменив пробелы на нижнее подчеркивание
games.columns = games.columns.str.replace(' ', '_')
games.columns
```
```
Index(['name', 'platform', 'year_of_release', 'genre', 'na_sales', 'eu_sales',
       'jp_sales', 'other_sales', 'critic_score', 'user_score', 'rating'],
      dtype='object')
```

## Типы данных:

Некорректные типы данных часто возникают из‑за нечисловых пометок (вроде «N/A» или «nan») и лишних символов в ячейках, которые мешают автоматическому распознаванию формата.
Преобразование типов данных. 
```python
# Заменяем пропуски и нечисловые значения NaN
games['year_of_release'] = pd.to_numeric(games['year_of_release'], errors = 'coerce')
games['critic_score'] = pd.to_numeric(games['critic_score'], errors = 'coerce')
games['eu_sales'] = pd.to_numeric(games['eu_sales'], errors = 'coerce')
games['jp_sales'] = pd.to_numeric(games['jp_sales'], errors = 'coerce')
games['user_score'] = pd.to_numeric(games['user_score'], errors = 'coerce')

# Преобразовываем в необходимый тип данных
games[['year_of_release', 'critic_score']] = games[['year_of_release', 'critic_score']].astype('Int64')
games[['eu_sales', 'jp_sales', 'user_score']] = games[['eu_sales', 'jp_sales', 'user_score']].astype('float64')

games.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 16956 entries, 0 to 16955
Data columns (total 11 columns):
 #   Column           Non-Null Count  Dtype  
---  ------           --------------  -----  
 0   name             16954 non-null  object 
 1   platform         16956 non-null  object 
 2   year_of_release  16681 non-null  Int64  
 3   genre            16954 non-null  object 
 4   na_sales         16956 non-null  float64
 5   eu_sales         16950 non-null  float64
 6   jp_sales         16952 non-null  float64
 7   other_sales      16956 non-null  float64
 8   critic_score     8242 non-null   Int64  
 9   user_score       7688 non-null   float64
 10  rating           10085 non-null  object 
dtypes: Int64(2), float64(5), object(4)
memory usage: 1.5+ MB
```

## Наличие пропусков в данных:
```python
# Рассчитаем число пропусков в каждом столбце.
games.isna().sum()

name                  2
platform              0
year_of_release     275
genre                 2
na_sales              0
eu_sales              6
jp_sales              4
other_sales           0
critic_score       8714
user_score         9268
rating             6871
dtype: int64
```
```python
# Подсчитываем общее количество строк
total_str = len(games)
total_str

16956
```
``` python
# Подсчитываем процент строк с пропусками
games.isna().sum() / total_str * 100

name                0.011795
platform            0.000000
year_of_release     1.621845
genre               0.011795
na_sales            0.000000
eu_sales            0.035386
jp_sales            0.023590
other_sales         0.000000
critic_score       51.391838
user_score         54.659118
rating             40.522529
dtype: float64
```
В данных наблюдаются пропуски в следующих столбцах:

- `critic_score` и `user_score`: в 8714 и в 9268 строках (более 50% данных) отсутствует информация об оценки критиков и оценке пользователей. Оценки критиков и пользователей часто отсутствуют одновременно и к ним часто присоединяется столбец rating. Большинство пропусков относятся к играм до 2000 года. Данные пропуски стоит игнорировать, так как их заполнение каким-либо значением может повлиять на дальнейший анализ.
- `rating`: в 6871 строках (40.5% данных) отсутствует информация о возрастном рейтинге - рейтинге организации ESRB. Аналогично оценкам - данные пропуски стоит игнорировать, так как их заполнение каким либо значением может повлиять на дальнейший анализ.
- `year_of_release`: в 275 строках (1.6% данных) отсутствуют данные о годе выпуска игры. Небольшая доля, вероятная причина — человеческий фактор при заполнении базы или отсутствие точной даты для редких/устаревших релизов. Для заполнения пропусков можно взять медианный год выпуска игр для платформы, к которой относится игра.
`eu_sales` и `jp_sales`: в 6 и 4 строках (менее 0.04% данных) отсутствует информация о продажах в Европе и Японии. Скорее всего, единичные ошибки выгрузки. Следовательно, данные пропуски логично заменить на среднее значение в зависимости от названия платформы и года выхода игры.
`genre` и `name`: всего в 2 строчках пропущены значения названия игры и ее жанра, данные пропуски можно не принимать во внимание и оставить без изменений.

```python
# Заполняем пропуске в столбце year_of_release
median_values = games.groupby('platform')['year_of_release'].transform('median')
games['year_of_release'] = games['year_of_release'].fillna(median_values)

# Заполняем пропуски в столбце eu_sales
mean_eu = games.groupby(['platform', 'year_of_release'])['eu_sales'].transform('mean')
games['eu_sales'] = games['eu_sales'].fillna(mean_eu)

# Заполняем пропуски в столбце jp_sales
mean_jp = games.groupby(['platform', 'year_of_release'])['jp_sales'].transform('mean')
games['jp_sales'] = games['jp_sales'].fillna(mean_jp)
```
```python
# Проверим пропуски еще раз
games.isna().sum()

name                  2
platform              0
year_of_release       0
genre                 2
na_sales              0
eu_sales              0
jp_sales              0
other_sales           0
critic_score       8714
user_score         9268
rating             6871
dtype: int64
```

## Явные и неявные дубликаты в данных:
Изучим уникальные значения в категориальных данных:
```python
games['genre'].unique()

array(['Sports', 'Platform', 'Racing', 'Role-Playing', 'Puzzle', 'Misc',
       'Shooter', 'Simulation', 'Action', 'Fighting', 'Adventure',
       'Strategy', nan, 'MISC', 'ROLE-PLAYING', 'RACING', 'ACTION',
       'SHOOTER', 'FIGHTING', 'SPORTS', 'PLATFORM', 'ADVENTURE',
       'SIMULATION', 'PUZZLE', 'STRATEGY'], dtype=object)
```
```python
games['platform'].unique()

array(['Wii', 'NES', 'GB', 'DS', 'X360', 'PS3', 'PS2', 'SNES', 'GBA',
       'PS4', '3DS', 'N64', 'PS', 'XB', 'PC', '2600', 'PSP', 'XOne',
       'WiiU', 'GC', 'GEN', 'DC', 'PSV', 'SAT', 'SCD', 'WS', 'NG', 'TG16',
       '3DO', 'GG', 'PCFX'], dtype=object)
```
```python
games['rating'].unique()

array(['E', nan, 'M', 'T', 'E10+', 'K-A', 'AO', 'EC', 'RP'], dtype=object)
```
```python
games['year_of_release'].unique()

<IntegerArray>
[2006, 1985, 2008, 2009, 1996, 1989, 1984, 2005, 1999, 2007, 2010, 2013, 2004,
 1990, 1988, 2002, 2001, 2011, 1998, 2015, 2012, 2014, 1992, 1997, 1993, 1994,
 1982, 2016, 2003, 1986, 2000, 1995, 1991, 1981, 1987, 1980, 1983]
Length: 37, dtype: Int64
```

Проведем нормализацию данных с текстовыми значениями. Названия или жанры игр приведем к нижнему регистру, а названия рейтинга — к верхнему

```python
games['genre'] = games['genre'].str.lower()
games['name'] = games['name'].str.lower()
games['rating'] = games['rating'].str.upper()
```
```python
# Считаем количество явных дубликатов
games.duplicated().sum()

241
```
```python
# Удаляем дубликаты
games = games.drop_duplicates()
# Проверяем 
games.duplicated().sum()

0
```

При изучении уникальных значений в категориальных данных были выявлены неявные дубликаты, связанные с опечатками и разным способом написания. Данные были нормализованы и изучены явные дубликаты. Был найден и удален 241 дубликат.

В процессе подготовки данных были удалены только дубликаты - 241 строка.

```python
# Подсчитаем относительное значение удаленных строк. Всего строк было 16956
241 / 16956

0.014213257843831092
```

#### Промежуточный вывод:
Проведена предобработка данных, в рамках которой названия столбцов приведены к стилю snake_case, выявлены и устранены некорректные типы данных.

Проведена работа с пропусками: более 50% данных содержат пропуски в оценках критиков и пользователей, 40,5% — в возрастном рейтинге ESRB. Эти пропуски оставлены без заполнения, так как их объём слишком велик для корректной замены. В 2 строках отсутствовали название и жанр — эти строки также оставили без изменений. Пропуски в годе выпуска (около 1,6%) заполнены медианой по платформе. Единичные пропуски в продажах в Европе (6 строк) и Японии (4 строки) заполнены средним значением по платформе и году выпуска.

Устранены неявные дубликаты: жанры приведены к нижнему регистру, рейтинги — к верхнему. Удалены явные дубликаты.
В результате очистки удалено 241 строк (1.4% от исходного объёма данных).

## Фильтрация данных
Коллеги хотят изучить историю продаж игр в начале XXI века, и их интересует период с 2000 по 2013 год включительно. Отберите данные по этому показателю. Сохраните новый срез данных в отдельном датафрейме, например df_actual.
```python
# Создаем срез: игры с 2000 по 2013 год включительно
df_actual = games[
    (games['year_of_release'] >= 2000) & 
    (games['year_of_release'] <= 2013)
].copy()
df_actual.head()
```
| Name | Platform | Year of Release | Genre | NA Sales | EU Sales | JP Sales | Other Sales | Critic Score | User Score | Rating |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Wii Sports | Wii | 2006 | Sports | 41.36 | 28.96 | 3.77 | 8.45 | 76 | 8.0 | E |
| Mario Kart Wii | Wii | 2008 | Racing | 15.68 | 12.76 | 3.79 | 3.29 | 82 | 8.3 | E |
| Wii Sports Resort | Wii | 2009 | Sports | 15.61 | 10.93 | 3.28 | 2.95 | 80 | 8.0 | E |
| New Super Mario Bros. | DS | 2006 | Platform | 11.28 | 9.14 | 6.50 | 2.88 | 89 | 8.5 | E |
| Wii Play | Wii | 2006 | Misc | 13.96 | 9.18 | 2.93 | 2.84 | 58 | 6.6 | E |

```python
df_actual.info()

<class 'pandas.core.frame.DataFrame'>
Int64Index: 13021 entries, 0 to 16954
Data columns (total 11 columns):
 #   Column           Non-Null Count  Dtype  
---  ------           --------------  -----  
 0   name             13021 non-null  object 
 1   platform         13021 non-null  object 
 2   year_of_release  13021 non-null  Int64  
 3   genre            13021 non-null  object 
 4   na_sales         13021 non-null  float64
 5   eu_sales         13021 non-null  float64
 6   jp_sales         13021 non-null  float64
 7   other_sales      13021 non-null  float64
 8   critic_score     7318 non-null   Int64  
 9   user_score       6606 non-null   float64
 10  rating           8899 non-null   object 
dtypes: Int64(2), float64(5), object(4)
memory usage: 1.2+ MB
```
```python
# Приведем столбец user_score к числовому значению
df_actual['user_score'] = games.loc[df_actual.index, 'user_score'] # берём значения по тем же индексам, которые есть в df_actual
df_actual['user_score'] = pd.to_numeric(df_actual['user_score'], errors='coerce')
df_actual.info()

<class 'pandas.core.frame.DataFrame'>
Int64Index: 13021 entries, 0 to 16954
Data columns (total 11 columns):
 #   Column           Non-Null Count  Dtype  
---  ------           --------------  -----  
 0   name             13021 non-null  object 
 1   platform         13021 non-null  object 
 2   year_of_release  13021 non-null  Int64  
 3   genre            13021 non-null  object 
 4   na_sales         13021 non-null  float64
 5   eu_sales         13021 non-null  float64
 6   jp_sales         13021 non-null  float64
 7   other_sales      13021 non-null  float64
 8   critic_score     7318 non-null   Int64  
 9   user_score       6606 non-null   float64
 10  rating           8899 non-null   object 
dtypes: Int64(2), float64(5), object(4)
memory usage: 1.2+ MB
```
## Категоризация данных

#### Разделим все игры по оценкам пользователей и выделим такие категории: высокая оценка (от 8 до 10 включительно), средняя оценка (от 3 до 8, не включая правую границу интервала) и низкая оценка (от 0 до 3, не включая правую границу интервала).
```python
# Категоризация пользовательских оценок
df_actual['user_score_category'] = pd.cut(
    df_actual['user_score'],
    bins=[0, 3, 8, 10],                 
    labels=['низкая оценка', 'средняя оценка', 'высокая оценка'],
    right=False)

# Добавляем категорию для пропусков и заполняем NaN
df_actual['user_score_category'] = (
    df_actual['user_score_category']
    .cat.add_categories(['нет оценки'])
    .fillna('нет оценки'))
```
| Name | Platform | Year of Release | Genre | NA Sales | EU Sales | JP Sales | Other Sales | Critic Score | User Score | Rating | User Score Category |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Wii Sports | Wii | 2006 | Sports | 41.36 | 28.96 | 3.77 | 8.45 | 76 | 8.0 | E | Высокая оценка |
| Mario Kart Wii | Wii | 2008 | Racing | 15.68 | 12.76 | 3.79 | 3.29 | 82 | 8.3 | E | Высокая оценка |
| Wii Sports Resort | Wii | 2009 | Sports | 15.61 | 10.93 | 3.28 | 2.95 | 80 | 8.0 | E | Высокая оценка |
| New Super Mario Bros. | DS | 2006 | Platform | 11.28 | 9.14 | 6.50 | 2.88 | 89 | 8.5 | E | Высокая оценка |
| Wii Play | Wii | 2006 | Misc | 13.96 | 9.18 | 2.93 | 2.84 | 58 | 6.6 | E | Средняя оценка |

#### Разделим все игры по оценкам критиков и выделим такие категории: высокая оценка (от 80 до 100 включительно), средняя оценка (от 30 до 80, не включая правую границу интервала) и низкая оценка (от 0 до 30, не включая правую границу интервала).
```python
df_actual['critic_score_category'] = pd.cut(
    df_actual['critic_score'],
    bins = [0, 30, 80, 100],
    labels = ['низкая оценка', 'средняя оценка', 'высокая оценка'],
    include_lowest=True)

df_actual['critic_score_category'] = (
    df_actual['critic_score_category']
    .cat.add_categories(['нет оценки'])
    .fillna('нет оценки'))
```
| Name | Platform | Year of Release | Genre | NA Sales | EU Sales | JP Sales | Other Sales | Critic Score | User Score | Rating | User Score Category | Critic Score Category |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Wii Sports | Wii | 2006 | Sports | 41.36 | 28.96 | 3.77 | 8.45 | 76 | 8.0 | E | Высокая оценка | Средняя оценка |
| Mario Kart Wii | Wii | 2008 | Racing | 15.68 | 12.76 | 3.79 | 3.29 | 82 | 8.3 | E | Высокая оценка | Высокая оценка |
| Wii Sports Resort | Wii | 2009 | Sports | 15.61 | 10.93 | 3.28 | 2.95 | 80 | 8.0 | E | Высокая оценка | Средняя оценка |
| New Super Mario Bros. | DS | 2006 | Platform | 11.28 | 9.14 | 6.50 | 2.88 | 89 | 8.5 | E | Высокая оценка | Высокая оценка |
| Wii Play | Wii | 2006 | Misc | 13.96 | 9.18 | 2.93 | 2.84 | 58 | 6.6 | E | Средняя оценка | Средняя оценка |

После категоризации данных проверим результат: сгруппируем данные по выделенным категориям и посчитаем количество игр в каждой категории:
```python
df_actual.groupby('user_score_category')['name'].count()

user_score_category
низкая оценка      119
средняя оценка    4159
высокая оценка    2328
нет оценки        6415
Name: name, dtype: int64
```
```python
df_actual.groupby('critic_score_category')['name'].count()

critic_score_category
низкая оценка       70
средняя оценка    5729
высокая оценка    1519
нет оценки        5703
Name: name, dtype: int64
```
#### Выделим топ-7 платформ по количеству игр, выпущенных за весь актуальный период.
```python
# Группируем игры по платформе и считаем количество
gr_platform = df_actual.groupby('platform')['name'].count()
# Сортируем по убыванию и берём первые 7
gr_platform.sort_values(ascending=False).head(7)

platform
PS2     2161
DS      2150
Wii     1309
PSP     1196
X360    1151
PS3     1112
XB       824
Name: name, dtype: int64
```
# Итоговый вывод
В конце напишите основной вывод и отразите, какую работу проделали. Не забудьте указать описание среза данных и новых полей, которые добавили в исходный датасет.

В ходе работы с датасетом new_games.csv провели предобработку и очистку данных: привели названия столбцов к стилю snake_case, скорректировали типы данных — год выпуска и оценки критиков перевели в int64; обработали пропуски — в оценках критиков, пользователей и рейтинге ESRB их оставили без заполнения из‑за большой доли, пропуски в годе выпуска заполнили медианой по платформе, единичные пропуски в продажах по Европе и Японии — средним значением по платформе и году выпуска; устранили дубликаты — привели жанры к нижнему регистру, рейтинги к верхнему,в результате удалили 241 строку (1,4 % от исходного объёма).

Далее сформировали целевой срез данных — отобрали записи за период с 2000 по 2013 год включительно и сохранили их в датафрейме df_actual. Затем провели категоризацию оценок: пользовательские оценки (0–10) разбили на три группы — высокая (от 8 до 10), средняя (от 3 до менее 8), низкая (от 0 до менее 3); оценки критиков (0–100) — на высокую (от 80 до 100), среднюю (от 30 до менее 80) и низкую (от 0 до менее 30).

Для проверки результатов подсчитали количество игр в каждой категории с помощью value_counts — это позволило оценить распределение оценок. В завершение определили топ‑7 платформ по количеству выпущенных игр за 2000–2013 годы путем группировки по платформе и подсчета числа записей.
