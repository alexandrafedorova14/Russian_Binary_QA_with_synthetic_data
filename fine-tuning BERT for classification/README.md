# Тонкая настройка ruBERT и ruRoBERTa на задачу генерации бинарных ответов

Задача генерации ответа на общий вопрос сводится к бинарной классификации: у нас есть два класса (1 и 0), и входная последовательность (соединенный вопрос и текстовый фрагмент) может быть отнесена либо к одному, либо ко второму классу. Тонкая настройка осуществлялась с помощью класса Trainer из библиотеки huggingface.

Каждая модель обучалась на трех разных обучающих выборках: 
* исходном DaNetQA (далее DaNetQA)
* DaNetQA с синтетической выборкой, полученной правиловым подходом (далее DaNetQA+RB)
* DaNetQA с синтетической выборкой, полученной после обучения подводки для ruGPT-3 (далее DaNetQA+PT)

<ins>Гиперпараметры обучения:</ins>
* скорость обучения – 1e-5
* оптимизатор - AdamW
* warmup на первых 100 шагах обучения, далее последовательное уменьшение скорости с помощью линейного планировщика (англ. linear scheduler)
* количество эпох обучения – 5
* размер батча на обучении – 8 (для ruBERT) / 4 (для ruRoBERTa)
* размер батча на валидации – 8 (для ruBERT) / 4 (для ruRoBERTa)

Каждую эпоху вычислялась Accuracy, а также Precision, Recall и F1-мера для каждого класса. 

<ins>Результаты тонкой настройки ruBERT на DaNetQA:</ins>
* **Accuracy на валидации** = 0.52
* **Accuracy на тесте** = 0.503

|   | **Positive questions**  | **Negative Questions** |
|:-------------:|:-------------:|:-------------:
|Precision| 0.51  | 0.67  |
|Recall| 0.96  | 0.08 |
|F-1|0.67  | 0.14  |

---

<ins>Результаты тонкой настройки ruBERT на DaNetQA+RB:</ins>

* **Accuracy на валидации** = 0.6
* **Accuracy на тесте** = 0.522

|   | **Positive questions**  | **Negative Questions** |
|:-------------:|:-------------:|:-------------:
|Precision| 0.57  | 0.71  |
|Recall| 0.85  | 0.35 |
|F-1|0.68  | 0.47  |

---

<ins>Результаты тонкой настройки ruBERT на DaNetQA+PT:</ins>

* **Accuracy на валидации** = 0.63 
* **Accuracy на тесте** = 0.523

|   | **Positive questions**  | **Negative Questions** |
|:-------------:|:-------------:|:-------------:
|Precision| 0.59  | 0.73  |
|Recall| 0.9  | 0.5 |
|F-1|75  | 62  |

--- 

<ins>Результаты тонкой настройки ruRoBERTa на DaNetQA:</ins>
* **Accuracy на валидации** = 0.75
* **Accuracy на тесте** = 0.645

|   | **Positive questions**  | **Negative Questions** |
|:-------------:|:-------------:|:-------------:
|Precision| 0.68  | 0.91  |
|Recall| 0.94  | 0.56 |
|F-1| 0.79  | 0.69  |

---

<ins>Результаты тонкой настройки ruRoBERTa на DaNetQA+RB:</ins>

* **Accuracy на валидации** = 0.7 
* **Accuracy на тесте** = 0.57

|   | **Positive questions**  | **Negative Questions** |
|:-------------:|:-------------:|:-------------:
|Precision| 0.63  | 0.95  |
|Recall| 0.98  | 0.43 |
|F-1| 0.77  | 0.59  |

---

<ins>Результаты тонкой настройки ruRoBERTa на DaNetQA+PT:</ins>

* **Accuracy на валидации** = 0.79
* **Accuracy на тесте** = 0.67

|   | **Positive questions**  | **Negative Questions** |
|:-------------:|:-------------:|:-------------:
|Precision| 0.8  | 0.79  |
|Recall| 0.78  | 0.8 |
|F-1| 0.79  | 0.79  |

--- 

Поскольку модели довольно много весят, они не помещаются в репозиторий. Все веса моделей после p-tuning-а можно найти на гугл-диске: 
1. ruBERT:
    * fine-tuning на исходном DaNetQA – https://drive.google.com/drive/folders/1fScAc4ikTFAnud6S-AJZyxMa5JvCCfVR?usp=share_link
    * fine-tuning на DaNetQA+RB – https://drive.google.com/drive/folders/1FzXTyTUT8pUlepd32i9mTyiLnXvtVHjP?usp=share_link
    * fine-tuning на DaNetQA+PT –  https://drive.google.com/drive/folders/1-ZTrCkJK9wky7iV_mxK0kHCon0qJu__S?usp=share_link
--- 
2. ruRoBERTa: 
    * fine-tuning на исходном DaNetQA – 
    * fine-tuning на DaNetQA+RB – 
    * fine-tuning на DaNetQA+PT – 

