## Seoul Transit Weather Forecast

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ssssooho/seoul-transit-weather-forecast/blob/main/seoul_transport_prediction.ipynb)

서울시 행정동별 **대중교통 승객 수**를 기상 데이터와 결합해 예측하는 머신러닝 프로젝트입니다.

---

## 프로젝트 개요

| 항목 | 내용 |
|------|------|
| **목표** | 날씨 조건에 따른 행정동별 대중교통 일별 승객 수 예측 |
| **데이터** | 서울시 행정동별 대중교통 승차 승객수 + 기상청 기상 관측 데이터 (2023년) |
| **모델** | Linear Regression / Decision Tree / Random Forest |
| **튜닝** | Optuna를 활용한 하이퍼파라미터 최적화 |

---

## 데이터셋

| 파일명 | 출처 | 설명 |
|--------|------|------|
| `transport_sample.csv` | 서울 열린데이터광장 | 행정동별 일별 승차 승객 수 샘플 (10,000건) / [전체 데이터 다운로드](https://data.seoul.go.kr) |
| `weather_2023.csv` | 기상청 기상자료개방포털 | 서울 일별 기온, 강수량, 풍속 등 (365건) |

---

## 사용 기술

```
Python 3.13
pandas / scikit-learn / optuna / matplotlib / joblib
```

---

## 모델 성능 비교

| 모델 | RMSE | R² |
|------|------|-----|
| 선형 회귀 | 24,762 | 0.00 |
| 의사결정 트리 | 11,147 | 0.80 |
| **랜덤 포레스트** | **8,340** | **0.89** |
| 랜덤 포레스트 (Optuna 튜닝) | 13,618 | 0.70 |

> 선형 회귀의 R²≈0은 승객 수와 기상 변수 간 선형 관계가 거의 없음을 의미합니다.  
> Optuna 튜닝 후 성능 하락은 탐색 범위(`max_depth` 3~10)가 과도하게 제한되어 발생한 것으로, 기본 랜덤 포레스트(R²=0.89)를 최종 모델로 선택했습니다.

---

## 프로젝트 구조

```
seoul-transit-weather-forecast/
├── seoul_transport_prediction.ipynb   # 메인 분석 노트북
├── data/
│   ├── 서울시_대중교통_승객수.csv
│   └── 기상_관측_서울_2023.csv
├── best_rf_model.pkl                  # 저장된 최종 모델
└── README.md
```

---

## 실행 방법

```bash
# 1. 패키지 설치
pip install pandas scikit-learn optuna matplotlib joblib

# 2. 노트북 실행
jupyter notebook seoul_transport_prediction.ipynb
```

---

## 분석 흐름

1. **데이터 로드 및 탐색** — 대중교통 데이터(44만 건)와 기상 데이터(365일) 구조 확인
2. **전처리** — 시간대별 컬럼 제거, 결측치 처리, 날짜 기준 병합
3. **피처 엔지니어링** — 행정동 ID, 평균기온, 강수량, 풍속, 증기압, 적설량, 전운량
4. **모델 학습 및 비교** — 선형 회귀 / 의사결정 트리 / 랜덤 포레스트
5. **하이퍼파라미터 튜닝** — Optuna (50 trials)
6. **모델 저장** — joblib으로 최종 모델 직렬화

---

## 한계 및 개선 방향

- 행정동 ID를 범주형이 아닌 수치형으로 사용 → 향후 Label Encoding 또는 임베딩 적용 필요
- 공휴일, 요일 등 시계열 특성 미반영 → Feature 추가 시 성능 향상 기대
- 단일 연도(2023년) 데이터만 사용 → 다년도 데이터로 일반화 필요
