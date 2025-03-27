# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
- Русакова Анна Алексеевна
- РИ-230947
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * |  |
| Задание 2 | * |  |
| Задание 3 | * |  |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Научиться передавать в Unity данные из Google Sheets с помощью Python.

## Задание 1
### Выбрать одну из игровых переменных в игре СПАСТИ РТФ: Выживание (HP, SP, игровая валюта, здоровье и т.д.), описать её роль в игре, условия изменения / появления и диапазон допустимых значений. Построить схему экономической модели в игре и укажите место выбранного ресурса в ней.

### Ход работы:
- Выбор и описание игровой переменной.
  Рассмотрим HP. Показывает количество очков здоровья персонажа. Пока их количество больше нуля, персонаж жив, то есть за него можно продолжать играть. Здоровье падает при получение урона, например, от атак врагов (не заметила, есть ли другие источники урона в данной игре), и поднимается / восстанавливается, например, после успешной зачистки волны. У персонажа может быть от 0 до 30 очков здоровья.


## Задание 2
### С помощью скрипта на языке Python заполнить google-таблицу данными, описывающими выбранную игровую переменную в игре “СПАСТИ РТФ:Выживание”. Средствами google-sheets визуализировать данные в google-таблице (постройте график / диаграмму и пр.) для наглядного представления выбранной игровой величины. Описать характер изменения этой величины, недостатки в реализации этой величины (например, в игре может произойти условие наступления эксплойта) и предложить до 3-х вариантов модификации условий работы с переменной, чтобы сделать игровой опыт лучше.


- Написание скрипта получения 5 ед. урона в секунду:
```python
import gspread
import numpy as np

gc = gspread.service_account(filename = 'unitydatascience-455018-017ce5ad1ca4.json')
sh = gc.open("UnityWorkshop2")

current_hp = 30
i = 0
while current_hp >= 0:
    i += 1
    if i == 0:
        continue
    else:
        sh.sheet1.update_acell(('A' + str(i)), str(i))
        sh.sheet1.update_acell(('B' + str(i)), str(current_hp))
        print(current_hp)
        current_hp -= 5
```
![image](https://github.com/user-attachments/assets/ff22d3ac-f96c-4222-94ec-57cc171009fb)

![image](https://github.com/user-attachments/assets/7556f633-318c-4f38-bf79-3c44e1e047a2)

![image](https://github.com/user-attachments/assets/7fa3e49a-7a1e-47ee-a43a-5af2766e3c6d)

- Определить недостатки выбранной игровой величины и предложить модификации.
  Не знаю, какие недостатки вообще можно выделить у очков здоровья, работают как надо. Однако могу предложить добавить больше 1) механик с восстановлением или тратой своего здоровья взамен на что-то, 2) лут с врагов, который может хилить или наоборот наносить урон, но давать какой-то полезный эффект, 3) режим или какие-то предметы, которые снижают хп до 1, опять-таки, взамен на что-то очень полезное (например, как проклятые сундуки в Dead cells, открыв которые, ты умираешь с одного удара, пока не убьёшь определённое количество врагов)



## Задание 3
### Настроить на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной. Например, если выбрано здоровье главного персонажа вы можете выводить сообщения, связанные с его состоянием.

- Выбрать диапазоны показателей здоровья для каждого из состояний.
  Пусть от 21 до 30 hp состояние хорошее, от 1 до 20 - среднее, а 0 - плохое. Реализуем с помощью следующего скрипта:
```python
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] > 20 & dataSet["Mon_" + i.ToString()] <= 30 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
            Debug.Log("Horosho");
        }
        if (dataSet["Mon_" + i.ToString()] <= 20 & dataSet["Mon_" + i.ToString()] > 0 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
            Debug.Log("Sredne");
        }
        if (dataSet["Mon_" + i.ToString()] == 0 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
            Debug.Log("Ploho");
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1CEjtCsGKu_fHyNkAAnhjj7Op5AObTqc0w-XotEF8bTI/values/Лист1?key=AIzaSyASnu9QXtSW-cHLv__q0mYZNbbhJUFUgdM");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[1]));
        }
    }
    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```
![image](https://github.com/user-attachments/assets/7d8a7e47-4a47-499d-a951-c48cbe1b9407)


## Выводы
Я научилась генерировать наполнение для гугл-таблиц, визуализировать это наполнение и сопрячь это наполнение с проектом в Unity.


| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
