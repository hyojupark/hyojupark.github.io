---
title: precision, recall, f1-score 계산(sklearn confusion_matrix, classification_report)
toc: true
categories:
  - Deep Learning, AI
tags:
  - classification_report
  - confusion matrix
  - confusion_matrix
  - f1-score
  - Precision
  - Recall
  - sklearn
---

`sklearn`의 `confusion_matrix`와 `classification_report`를 사용하면 `confusion matrix` 출력과 함께 `precision`, `recall`, `f1-score`를 쉽게 계산할 수 있습니다.

## **sklearn 설치**

```bash
pip install sklearn
```

## **코드 예시**

```python
from sklearn.metrics import confusion_matrix, classification_report 

true_label = [1, 1, 0, 0, 1, 1, 2, 2, 0, 1, 2, 1, 2, 1, 0, 2, 1, 1, 1, 0, 0, 0, 1]
pred_label = [1, 1, 1, 0, 1, 0, 2, 2, 0, 1, 2, 0, 2, 1, 0, 2, 1, 1, 1, 0, 0, 1, 1]

labels = [0, 1, 2]

cm = confusion_matrix(true_label, pred_label, labels=labels)
metric = classification_report(y_true=true_label, y_pred=pred_label, labels=labels)
print(cm)
print(metric)


# 출력
[[5 2 0]
 [2 9 0]
 [0 0 5]]
              precision    recall  f1-score   support

           0       0.71      0.71      0.71         7
           1       0.82      0.82      0.82        11
           2       1.00      1.00      1.00         5

    accuracy                           0.83        23
   macro avg       0.84      0.84      0.84        23
weighted avg       0.83      0.83      0.83        23
```

`true_label`과 `pred_label`같이 모델의 Inference 결과가 나왔다고 가정합니다. label이 0, 1, 2 3개일 때 각각 label의 `precision`, `recall`, `f1-score`, `support`가 정리돼서 출력되는 것을 볼 수 있습니다. `support`는 `true_label`의 각 label 별 수량이고, 나머지 metric은 아래 수식을 참고 바랍니다.

## **Metric**

![](/assets/images/posts/2022-7-1-tistory-post-69/img-1.png){: .align-center}
**< Metric 별 수식 >**
{: .text-center}

![](/assets/images/posts/2022-7-1-tistory-post-69/img-2.png){: .align-center}
**< Precision 예시 >**
{: .text-center}

![](/assets/images/posts/2022-7-1-tistory-post-69/img-3.png){: .align-center}
**< Recall 예시 >**
{: .text-center}

<br>

## **classification_report 활용 (dictionary 변환)**

위 코드를 그대로 사용하면 문자열로 반환되기 때문에 활용이 어렵습니다. 이를 `output_dict=True` 파라미터를 추가해서 dictionary로 변환하면 활용이 가능합니다.

```python
from pprint import pprint

from sklearn.metrics import classification_report

true_label = [1, 1, 0, 0, 1, 1, 2, 2, 0, 1, 2, 1, 2, 1, 0, 2, 1, 1, 1, 0, 0, 0, 1]
pred_label = [1, 1, 1, 0, 1, 0, 2, 2, 0, 1, 2, 0, 2, 1, 0, 2, 1, 1, 1, 0, 0, 1, 1]

labels = [0, 1, 2]

metric = classification_report(y_true=true_label, y_pred=pred_label, labels=labels, output_dict=True)
pprint(metric)


# 출력
{'0': {'f1-score': 0.7142857142857143,
       'precision': 0.7142857142857143,
       'recall': 0.7142857142857143,
       'support': 7},
 '1': {'f1-score': 0.8181818181818182,
       'precision': 0.8181818181818182,
       'recall': 0.8181818181818182,
       'support': 11},
 '2': {'f1-score': 1.0, 'precision': 1.0, 'recall': 1.0, 'support': 5},
 'accuracy': 0.8260869565217391,
 'macro avg': {'f1-score': 0.8441558441558442,
               'precision': 0.8441558441558442,
               'recall': 0.8441558441558442,
               'support': 23},
 'weighted avg': {'f1-score': 0.8260869565217391,
                  'precision': 0.8260869565217391,
                  'recall': 0.8260869565217391,
                  'support': 23}}
```
