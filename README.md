# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил:
- Хаврак Артур Юрьевич
- ФО-220007

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger

## Цель работы
Научиться передавать в Unity данные из Google Sheets с помощью Python.

## Задание 1
### Выберите одну из компьютерных игр, приведите скриншот её геймплея и краткое описание концепта игры. Выберите одну из игровых переменных в игре (ресурсы, внутри игровая валюта, здоровье персонажей и т.д.), опишите её роль в игре, условия изменения / появления и диапазон допустимых значений. Постройте схему экономической модели в игре и укажите место выбранного ресурса в ней.

Я выбрал игру "Counter Strike 2" и игровую переменную "деньги".

Counter-Strike 2 — это бесплатное улучшение для CS:GO, которое знаменует собой крупнейший технологический скачок в истории серии. Оно разработано на движке Source 2 и модернизирует игру благодаря реалистичному и физически корректному рендерингу, организации сети по последнему слову технологий и улучшенным инструментам для мастерской сообщества.

В целом, в экономической модели CS2 деньги играют важную роль, определяя доступность и качество снаряжения, влияя на стратегические решения и создавая возможности для торговли внутриигровыми предметами.

## Задание 2
### С помощью скрипта на языке Python заполните google-таблицу данными, описывающими выбранную игровую переменную в выбранной игре (в качестве таких переменных может выступать игровая валюта, ресурсы, здоровье и т.д.). Средствами google-sheets визуализируйте данные в google-таблице (постройте график, диаграмму и пр.) для наглядного представления выбранной игровой величины.

Пару сслов про экономику CS2:
- Если команда выиграет (неважно какое количество раз подряд), то каждый получает по 3 500$.
- Если команда проиграет в первый раз, то каждый получает по 1 400$
- Если команда проиграет 2 раза подряд или проиграет в первом раунде, то каждый получает по 1 900$
- Если команда проиграет 3 раза подряд, то каждый получает по 2 400$
- Если команда проиграет 4 раза подряд, то каждый получает по 2 900$
- Если команда проиграет 5 или более раз подряд, то каждый получает по 3 400$

Скрипт будет случайно выбирать, победила ли команда или нет, и от этого значения будет выдаваться то или иного количество денег

```py

import gspread
import numpy as np
gc = gspread.service_account(filename='da-in-gamedev-lab2-403203-2f9ff29797c9.json')
sh = gc.open("DA-in-GameDev-lab2")

money_for_win = 3500
money_for_first_loss = 1400
money_for_second_loss = 1900
money_for_third_loss = 2400
money_for_fourth_loss = 2900
money_for_fifth_or_more_loss = 3400

loss_count = 0
rounds_result = np.random.randint(0, 2, 11)
i = 0
while i < len(rounds_result) - 1:
    i += 1
    if i == 0:
        continue
    else:
        money = 0
        if rounds_result[i] == 1:
            money = money_for_win
            lose_count = 0
        elif rounds_result[i] == 0:
            loss_count += 1
            if loss_count == 1 and i != 1:
                money = money_for_first_loss
            elif loss_count == 2 or i == 1:
                money = money_for_second_loss
            elif loss_count == 3:
                money = money_for_third_loss
            elif loss_count == 4:
                money = money_for_fourth_loss
            elif loss_count >= 5:
                money = money_for_fifth_or_more_loss
        
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(money))
        print(i, rounds_result[i], money)

```
Ссылка на таблицу:
https://docs.google.com/spreadsheets/d/1KvsUmYLaEN9s0z9HYiJUsDshXMo7XJsKhErwZ5JWdvY/edit#gid=0


## Задание 3
### Настройте на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной. Например, если выбрано здоровье главного персонажа вы можете выводить сообщения, связанные с его состоянием.

В начале раунда каждый член команды тратит определённое количество денег на покупку снаряжения.
Пусть у нас будет два варианта покупки снаряжения (считаем наше команду террористами):
1. MAC-10 (1 050) + бронежилет и шлем (1 000) + осколочная граната (300) = 2 350
2. AK-47 (2 700) + Desert Eagle (700) + бронежилет и шлем (1 000) + световая граната (200) + осколочная граната (300) + дымовая граната (300) + коктейль молотова (400) = 5 600

Скрипт для Unity будет посчитывать общее количество денег, и от этого выдавать звуковые сигналы:
- Если команде не хватает на покупки 1-ого варианта снаряжения - badSpeak
- Если команде хватает на покупку 1-ого варианта снаряжения, но не хватает на 2-ой - normalSpeak
- Если команде хватает на покупку 2-ого варианта снаряжения - goodSpeak

Стоит заметить, что в скрипте мы не берём в рассчёт исход ранда (выиграла команда или проиграла)

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class GSheets : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string, int> dataSet = new Dictionary<string, int>();
    private bool statusStart = false;
    private int i = 1;
    private int money = 800;
    private int firstEquipmentPrice = 2350;
    private int secondEquipmentPrice = 5600;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if(dataSet.Count == 0)
            return;

        if(statusStart == false & i != dataSet.Count)
            money += dataSet["Mon_" + i.ToString()];

        if (money >= secondEquipmentPrice & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()] + " " + money);
            money -= secondEquipmentPrice;
        }

        if (money >= firstEquipmentPrice & money < secondEquipmentPrice & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()] + " " + money);
            money -= firstEquipmentPrice;
        }

        if (money < firstEquipmentPrice & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()] + " " + money);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1KvsUmYLaEN9s0z9HYiJUsDshXMo7XJsKhErwZ5JWdvY/values/Лист1?key=AIzaSyCifg2ftK2w_qyjUTKwrJm1a3ZYRpyWKW0");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), int.Parse(selectRow[1]));
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

## Выводы

Я создал свой первый проект в GoogleCloudApi, научился передавать данные из Google Sheets в Unity.

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
