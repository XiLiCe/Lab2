<h1 id="lab2" align="center"><b>Разработка программы для распознавания именованных сущностей в тексте: имена, даты, организации и др.</b></h1>
<p>Данный пункт содержит в себе объяснение кода по лабораторной работе по предмету "Компьютерная лингвистика и обработка естественного языка"</p>


<a href="#download">Загрузка кода</a><p></p>
<a href="#start_code">Запуск кода</a><p></p>
<a href="#text_primer">Пример текста<a><p></p>
<a href="#what">Как работает</a>


<h2 id="download">Загружаем код</h2>
<p>Файл detection_in_text.ipynb содержит код моей лабораторной работы.</p>
<p>Нужно скачать и открыть его через GoogleCollab</p>


<h2 id="start_code">Запуск кода</h2>
<p>Для запуска нужно запустить каждый блок кода по порядку</p><p></p>
<p>После запуска мы увидим поле в которое нужно ввести текст в котором мы хотим распознать сущности</p><p></p>
<img src="https://github.com/XiLiCe/Lab2/blob/XiLiCe-patch-1/pole.png">
<p>Как только мы введем туда текст мы получим текст, сущность,токен текста</p>
<img src="https://github.com/XiLiCe/Lab2/blob/XiLiCe-patch-1/output.png">


<h2 id="text_primer">Пример текста</h2>
<p>Стерлитамак — город в России, второй (после Уфы) по численности населения город в Башкирии, административный центр Стерлитамакского района, в состав которого не входит. Город республиканского значения, образует муниципальное образование Городской округ город Стерлитамак как единственный населённый пункт в его составе. Крупный центр химической промышленности и машиностроения, один из центров Стерлитамакской агломерации. Основан в 1735 году как почтовая станция Ашкадарский Ям, статус города присвоен в 1781 году. В прошлом уездный город Уфимской области Уфимского наместничества (1781/82–1796)год; Оренбургской губернии (1796–1865)год, Уфимской губернии (1865–1920)год, столица Автономной Советской Башкирской Республики (1919—1922)год, а также административный центр Стерлитамакского кантона (1920—1930)год и Стерлитамакской области БАССР (1952—1953)год</p>


<h2 id="what">Как работает?</h2>
<p>В первую очеред мы загружаем и импортируем нужные нам библиотеки и модель Spacy для русского языка</p>

```python
!pip install dateparser
```

```python
import spacy
import dateparser
```

```python
!python -m spacy download ru_core_news_md
```


<!--<img src="https://github.com/XiLiCe/Lab2/blob/XiLiCe-patch-2/import.jpg">-->
<p>В данной части кода мы загружаем модель spaCy(для русского языка) обрабатываем текст и извлекаем именованные сущности</p>

```python
def extract_named_entities_spacy_russian_bioes(text):
    nlp = spacy.load("ru_core_news_md")
    doc = nlp(text)

    named_entities = [(ent.text, ent.start_char, ent.end_char, ent.label_)
```

<!--<img src="https://github.com/XiLiCe/Lab2/blob/XiLiCe-patch-2/one_code.jpg">-->
<p>Так же мы ищем и извлекаем даты. Мы ищем токен содержащий слово год, и если они соответствуют условиям то они пропускаются. Затем с помощью 'dateparser' мы пытаемся разобрать токен как дату. Если разбор успешен то информация добавляется в список 'named_entities'</p>

```python
    for sent in doc.sents:
        for i, token in enumerate(sent):
            if token.text.lower() == 'год' and i > 0 and sent[i - 1].is_digit and i < len(sent) - 1 and not sent[i + 1].is_alpha:
                continue
            if dateparser.parse(token.text, languages=['ru']):
                start, end = token.idx, token.idx + len(token)
                named_entities.append((token.text, start, end, "DATE"))

    return named_entities
```


<!--<img src="https://github.com/XiLiCe/Lab2/blob/XiLiCe-patch-2/date_code.jpg">-->
<p>Мы заменяем английские метки именованных сущностей на русские метки</p>

```python
def map_to_russian_labels(entity_type):
    label_mapping = {
        "PER": "Человек",
        "LOC": "Локация",
        "ORG": "Организация",
        "DATE": "Дата",
    }
    return label_mapping.get(entity_type, entity_type)
```

<!--<img src="https://github.com/XiLiCe/Lab2/blob/XiLiCe-patch-2/rus.jpg">-->
<p>Создаём функцию 'print_entities' которая выводит информацию об именованных сущностях в читаемом для нас формате с использованием русских меток</p>

```python
def print_entities(entities):
    for entity, start, end, entity_type in entities:
        russian_label = map_to_russian_labels(entity_type)
        print(f"{entity}: {russian_label} ({start}-{end})"
```

<!--<img src="https://github.com/XiLiCe/Lab2/blob/XiLiCe-patch-2/output2.jpg">-->
<p>После всего кода мы запрашиваем ввод текста от пользователся, вызываем функцию извлечения именованных сущностей, и выводим результат на экран в читаемом виде</p>

```python
if __name__ == "__main__":
    input_text = input()
    entities = extract_named_entities_spacy_russian_bioes(input_text)
    print_entities(entities)
```

<!--<img src="https://github.com/XiLiCe/Lab2/blob/XiLiCe-patch-2/end.jpg">->
