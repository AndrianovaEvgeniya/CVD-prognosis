# **Прогнозирование сердечно-сосудистых заболеваний (Heart Disease Prediction)**

---

## **📌 Оглавление**
1. [Цель проекта](#-цель-проекта)
2. [Описание данных](#-описание-данных)
3. [Этапы работы](#-этапы-работы)
4. [Установка и запуск](#-установка-и-запуск)
5. [Структура проекта](#-структура-проекта)
6. [Результаты моделей](#-результаты-моделей)
7. [Графики обучения](#-графики-обучения)
8. [Инференс](#-инференс)
9. [Требования](#-требования)
10. [Автор](#-автор)

---

## **🎯 Цель проекта**
Разработка модели машинного обучения для прогнозирования сердечно-сосудистых заболеваний на основе медицинских данных пациентов. В проекте сравниваются три модели:
- **Логистическая регрессия** (базовый подход)
- **Random Forest** (ансамблевый метод)
- **Нейронная сеть** (глубокое обучение)

**Лучшая модель:** Нейронная сеть показала наивысшую точность (**89.79%**) и AUC-ROC (**0.9622**).

---

## **📊 Описание данных**
Исходный датасет содержит **600 000 записей** с 14 признаками, включая:
- **Демографические данные:** возраст (`age`), пол (`sex`)
- **Медицинские показатели:** 
  - Артериальное давление (`resting_blood_pressure`)
  - Уровень холестерина (`serum_cholesterol`)
  - Максимальный пульс (`maximum_heart_rate_achieved`)
  - Депрессия сегмента ST (`oldpeak`)
  - Результаты таллиевого теста (`thal`)
- **Целевая переменная:** Наличие заболевания (`class`: 0 — нет, 1 — есть).

**Пример данных:**
| age  | sex | resting_blood_pressure | ... | class |
|------|-----|------------------------|-----|-------|
| 54.4 | 1   | 131                    | ... | 0     |

---

## **🔧 Этапы работы**
1. **Предобработка данных:**
   - Удаление дубликатов и обработка выбросов.
   - Замена отрицательных значений `oldpeak` на 0.
   - Группировка редких категорий (например, `chest`).
   - Стандартизация числовых признаков.

2. **Анализ данных:**
   - Построение корреляционной матрицы.
   - Визуализация распределений и выбросов.

3. **Обучение моделей:**
   - Логистическая регрессия (`sklearn`).
   - Random Forest с подбором гиперпараметров.
   - Нейронная сеть (`TensorFlow/Keras`).

4. **Оценка результатов:**
   - Метрики: Accuracy, Precision, Recall, F1-Score, ROC-AUC.
   - Анализ важности признаков.

---

## **⚙️ Установка и запуск**
1. **Клонирование репозитория:**
   ```bash
   git clone https://github.com/your-repo/heart-disease-prediction.git
   cd heart-disease-prediction
   ```

2. **Установка зависимостей:**
   ```bash
   pip install -r requirements.txt
   ```

3. **Запуск обучения:**
   - Для нейронной сети:
     ```bash
     python train.py
     ```
   - Для Random Forest:
     ```bash
     python train_rf.py
     ```

---

## **📂 Структура проекта**
```
heart-disease-prediction/
├── data/                    # Исходные данные
│   ├── train.csv            # Обучающая выборка
│   └── test.csv             # Тестовая выборка
├── models/                  # Сохранённые модели
│   ├── best_model.h5        # Нейронная сеть (TensorFlow)
│   └── random_forest.pkl    # Random Forest (Pickle)
├── notebooks/               # Анализ в Jupyter
│   ├── EDA.ipynb            # Разведочный анализ
│   └── Model_Training.ipynb # Обучение моделей
├── scripts/                 # Скрипты
│   ├── inference.py         # Прогнозирование
│   ├── preprocess.py        # Предобработка
│   └── train.py             # Обучение нейронной сети
├── results/                 # Результаты
│   ├── metrics.csv          # Сравнение моделей
│   ├── accuracy_plot.png    # График точности
│   └── feature_importance.png # Важность признаков
└── README.md
```

---

## **📊 Результаты моделей**
| Модель               | Accuracy | Precision | Recall | F1-Score | ROC-AUC |
|----------------------|----------|-----------|--------|----------|---------|
| Logistic Regression  | 0.880    | 0.880     | 0.860  | 0.870    | 0.9516  |
| Random Forest        | 0.897    | 0.891     | 0.875  | 0.883    | 0.9604  |
| **Neural Network**   | **0.8979** | **0.8812** | **0.8900** | **0.8856** | **0.9622** |

---

## **📈 Графики обучения**
### **Нейронная сеть**
- **Точность (Accuracy)**  
  ![Accuracy](results/accuracy_plot.png)  
- **Потери (Loss)**  
  ![Loss](results/loss_plot.png)  
- **ROC-AUC**  
  ![AUC](results/auc_plot.png)  

### **Важность признаков (Random Forest)**
![Feature Importance](results/feature_importance.png)  

---

## **🤖 Инференс**
### **1. Предсказание на новых данных**
```bash
python scripts/inference.py --input data/test.csv --output predictions.csv
```

### **2. Пример скрипта (`inference.py`)**
```python
import pandas as pd
import tensorflow as tf
from preprocess import preprocess_data

def load_model(model_path="models/best_model.h5"):
    return tf.keras.models.load_model(model_path)

def predict(model, data):
    return (model.predict(data) > 0.5).astype(int).flatten()

if __name__ == "__main__":
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument("--input", type=str, required=True)
    parser.add_argument("--output", type=str, default="predictions.csv")
    args = parser.parse_args()

    # Загрузка данных
    df = pd.read_csv(args.input)
    X_processed = preprocess_data(df)

    # Прогнозирование
    model = load_model()
    predictions = predict(model, X_processed)
    
    # Сохранение результатов
    pd.DataFrame({"ID": df["ID"], "Prediction": predictions}).to_csv(args.output, index=False)
    print(f"Результаты сохранены в {args.output}")
```

---

## **📋 Требования**
- Python 3.8+
- Библиотеки:
  ```bash
  tensorflow>=2.5.0
  scikit-learn>=1.0.0
  pandas>=1.3.0
  matplotlib>=3.4.0
  ```

---

## **👨💻 Автор**
- **Имя:** [Ваше имя]
- **GitHub:** [ссылка на профиль]
- **Контакты:** [email или телеграм]

---

**🔹 Примечание:**  
Для воспроизведения результатов используйте [Jupyter-ноутбук](notebooks/Model_Training.ipynb).  
Все модели сохранены в папке `models/`.