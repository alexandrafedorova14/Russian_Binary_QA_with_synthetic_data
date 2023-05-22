# P-tuning ruBERT под задачу генерации ответа на общий вопрос


Дополнительно обучение подводки или p-tuning было впервые предложенное в работе (Liu et al., 2021). В статье было показано, что метод позволяет достичь более высоких результатов для задачи генерации текста и задач на понимание текста, чем привычная тонкая настройка. Ниже приведены результаты имплементации p-tuning языковых моделей ruBERT и ruRoBERTa для решения задачи генерации ответа на общий вопрос.  

Каждая модель обучалась на трех разных обучающих выборках: 
* исходном DaNetQA (далее DaNetQA)
* DaNetQA с синтетической выборкой, полученной правиловым подходом (далее DaNetQA+RB)
* DaNetQA с синтетической выборкой, полученной после обучения подводки для ruGPT-3 (далее DaNetQA+PT)


Для реализации p-tuning для классификации была использвана библиотека OpenPrompt, которая позволяет самостоятельно задать шаблон подводки, вербализатор (англ. verbalizer), т.е. задать те выражения естественного языка, которым соответствует каждый класс, выбрать гиперпараметры обучения. 

Шаблон подводки для генерации ответа на общий вопрос в русском выглядит как: 

`{"placeholder": "text_a"} Вопрос: {"placeholder": "text_b"} {"soft"} {"soft"} {"soft"} Ответ: {"mask"} .`

где text_a – текстовый фрагмент, к которому задан вопрос, text_b – общий вопрос, soft – обучаемый токен, в данном случае было выборано три обучаемых токена, mask – метка класса (0 или 1)
    
Вербализатор выглядел следующим образом: 
`0 -> нет`, `1 -> да`

<ins>Гиперпараметры обучения:</ins> 
* скорость обучения – 1e-5
* оптимизатор – AdamW
* размер батча на обучении – 8 (для ruBERT) / 4 (для ruRoBERTa)
* размер батчка на валидации – (для ruBERT) / 4 (для ruRoBERTa)
* функция потерь (англ. loss function) – кросс-энтропия (torch.nn.CrossEntropyLoss())
* L2-регуляризация (aka weight decay) – 0.01, но 0 для смещения (англ. bias) и нормализации слоев (англ. layer normalization)

---

<ins>Результаты p-tuning ruBERT на DaNetQA:</ins>
* **Accuracy на валидации** = 0.673
* **Accuracy на тесте** = 0.643

|   | **Positive questions**  | **Negative Questions** |
|:-------------:|:-------------:|:-------------:
|Precision| 0.62 |  0.82 |
|Recall| 0.9  | 0.44 |
|F-1| 0.74 |  0.57 |

---

<ins>Результаты p-tuning ruBERT на DaNetQA+RB:</ins>
* **Accuracy на валидации** = 0.705
* **Accuracy на тесте** = 0.671

|   | **Positive questions**  | **Negative Questions** |
|:-------------:|:-------------:|:-------------:
|Precision| 0.65  | 0.81  |
|Recall| 0.88  | 0.53 |
|F-1|0.75  | 0.64  |

---

<ins>Результаты p-tuning ruBERT на DaNetQA+PT:</ins>
* **Accuracy на валидации** = 0.73
* **Accuracy на тесте** = 0.714

|   | **Positive questions**  | **Negative Questions** |
|:-------------:|:-------------:|:-------------:
|Precision| 0.67  | 0.84  |
|Recall| 0.9  | 0.56 |
|F-1| 0.77  | 0.67  |

---

<ins>Результаты p-tuning ruRoBERTa на DaNetQA:</ins>
* **Accuracy на валидации** = 0.79
* **Accuracy на тесте** = 0.78

|   | **Positive questions**  | **Negative Questions** |
|:-------------:|:-------------:|:-------------:
|Precision| 0.76  | 0.85  |
|Recall| 0.87  | 0.72 |
|F-1| 0.81  | 0.78  |

---

<ins>Результаты p-tuning ruRoBERTa на DaNetQA+RB:</ins>
* **Accuracy на валидации** = 0.8
* **Accuracy на тесте** = 0.783

|   | **Positive questions**  | **Negative Questions** |
|:-------------:|:-------------:|:-------------:
|Precision| 0.74  | 0.89  |
|Recall| 0.92  | 0.68 |
|F-1| 0.82  | 0.77  |

---

<ins>Результаты p-tuning ruRoBERTa на DaNetQA+PT:</ins>
* **Accuracy на валидации** = 0.82
* **Accuracy на тесте** = 0.817

|   | **Positive questions**  | **Negative Questions** |
|:-------------:|:-------------:|:-------------:
|Precision| 0.84  | 0.8  |
|Recall| 0.79  | 0.85 |
|F-1| 0.82  | 0.83  |

---
--- 
Поскольку модели довольно много весят, они не помещаются в репозиторий. Все веса моделей после p-tuning-а можно найти на гугл-диске: 
* p-tuning на исходном DaNetQA – https://drive.google.com/drive/folders/1WLydIM1FqCsK5WH2sL4h8vKLxZDSrq6V?usp=share_link
* p-tuning на DaNetQA с синтетическими данными, полученными с помощью p-tuning-а ruGPT-3  https://drive.google.com/drive/folders/1S4-fRQoSWRa7mDb1WZznlzsTyD_B68ws?usp=share_link
* p-tuning на DaNetQA с синтетическими данными, полученными с помощью rule-based генерации и перефразирования – https://drive.google.com/drive/folders/1rgz9LOla8iWkIJtOVZMl-BJhmbG3dm8b?usp=share_link
