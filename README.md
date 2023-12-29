# Aspect-based sentiment analysis
## Распределение обязанностей
* Ксения Дунаева - разметка аспектов, тональность аспектов
* Алёна Зенина - тональность аспектов, evaluation
* Варвара Смирнова - тональность категеорий
## Aspect Extraction

Поскольку задача выделения аспектов сводится к задаче выделения именованных сущностей, мы решили использовать метод Conditional Random Fields, применяемый для NER. Для этого мы разметили тренировочный корпус в формате BIO и обучили CRF, в качестве признаков выделии word.lower(), word[-3:], word[-2:], word.isupper(), word.isdigit(), POS, BOS, EOS. Попытки убрать один из признаков приводили к ухудшению качества модели. 

*Результаты*: при обучении accuracy 0.921 на всех тэгах, включая O, однако f1 score, recall и precision для всех категорий, кроме O, значительно ниже accuracy. 

*Проблемы*: хуже всего удается выделить сущности, состоящие из нескольких токенов (I-). Главная проблема модели - недобучение из-за дисбаланса классов: примеров с O достаточно, а остальных -- нет. 

*Файлы*: файлы находятся в папке [`aspect_extraction`](https://github.com/zadushevno/nlp-4-project/tree/main/aspect_extraction):
* [`aspect_extraction_train.ipynb`](https://github.com/zadushevno/nlp-4-project/blob/main/aspect_extraction/aspect_extraction_train.ipynb) -- обучение
* [`crf.pkl`](https://github.com/zadushevno/nlp-4-project/blob/main/aspect_extraction/crf.pkl) -- модель
* [`aspect_extraction_test.ipynb`](https://github.com/zadushevno/nlp-4-project/blob/main/aspect_extraction/aspect_extraction_test.ipynb) -- тестирование; для запуска на тестовых данных необходимо указать путь к файлу в переменной `test_texts_path` в начале кода. Полученный в результате работы тетрадки файл [`pred_aspects.txt`](https://github.com/zadushevno/nlp-4-project/blob/main/aspect_extraction/pred_aspects.txt) необходимо сохранить, чтобы затем использовать для определения тональности каждого вхождения.

## Aspect Sentiment Analysis

Для определения тональности каждого упоминания аспекта мы дообучили модель sberbank-ai/ruBert-base для классификации данных вида "@ aspect mention @ text". Этот метода показался нам подходящим, так как Bert в целом хорошо справляется с задачами классификации и определения тональности. 

*Результаты*: accuracy около 0.90 на валидационном датасете при обучении (0.92 по полностью совпавшим упоминаниям, 0.89 по частично)

*Проблемы*: из-за маленького количество both в тренировочном датасете, в полученном датасете both не появляется совсем

*Файлы*: файлы находятся в папке [`aspect_sentiment`](https://github.com/zadushevno/nlp-4-project/tree/main/aspect_sentiment):
* [`aspect_classification_train.ipynb`](https://github.com/zadushevno/nlp-4-project/blob/main/aspect_sentiment/aspect_classification_train.ipynb) -- обучение
* [`aspect_classification_test.ipynb`](https://github.com/zadushevno/nlp-4-project/blob/main/aspect_sentiment/aspect_classification_test.ipynb) -- тестирование

Для запуска на тестовых данных необходимо в тетрадке [`aspect_classification_test.ipynb`](https://github.com/zadushevno/nlp-4-project/blob/main/aspect_sentiment/aspect_classification_test.ipynb) указать путь к файлу [`pred_aspects.txt`](https://github.com/zadushevno/nlp-4-project/blob/main/aspect_extraction/pred_aspects.txt), который получился в предыдущем шаге, и путь к файлу с текстами. Поскольку файл с весами получился очень большим, мы разместили модель на [гугл-диске](https://drive.google.com/drive/u/0/folders/1e8WjOU0U33uCS4XWpkWmb2F6G2aMXak2). Чтобы запустить тетрадку с моделью в колабе, нужно
* на гугл-диске зайти в "доступные мне"
* нажать правой кнопкой мыши на папку absa-project, затем систематизировать < добавить ярлык
* добавить ярлык на ваш гугл-диск
* подключиться к вашему гугл-диску в колабе, загрузить модель (это все есть в коде)

## Overall Sentiment Analysis

Для определения тональности апекта в целом использован частотный метод: каких сентиментов больше, тот и приписывается, если pos и neg примерно одинаково, то приписывается both.

*Результаты*: accuracy 0.80

*Проблемы*: алгоритм достаточно простой, практически как в бейзлайне, с небольшими улучшениями

*Файлы*: файлы находятся в папке [`category_sentiment`](https://github.com/zadushevno/nlp-4-project/tree/main/category_sentiment)
* [`pred_full_tone.ipynb`](https://github.com/zadushevno/nlp-4-project/blob/main/category_sentiment/pred_full_tone.ipynb) -- алогритм работы 
* [`pred_full_results.txt`](https://github.com/zadushevno/nlp-4-project/blob/main/category_sentiment/pred_full_results.txt) -- результаты

## Evaluation
Evaluation находится вот в [этой тетрадке](https://github.com/zadushevno/nlp-4-project/blob/main/Evaluation.ipynb), нужно скачать полученные файлы и образцы и прописать к ним путь, чтобы заново ее запустить. В результате, во всех трех частячх работы мы получили результаты заметно выше, чем в бейзлайне.
