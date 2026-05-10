# ⚡🚗 전기차 주행거리 예측

머신러닝기반데이터분석 중간과제 
| 설계 스펙만으로 전기차 주행거리를 사전 예측하는 선형 회귀 모델

## 👋 프로젝트 소개

전기차의 배터리 용량, 전비, 차체 크기 등 **설계 스펙만으로 주행거리를 예측**하는 프로젝트입니다.
기존 연구가 실주행 데이터 기반 예측에 집중된 반면, 본 프로젝트는 설계 단계에서 활용 가능한 스펙 기반 예측을 목표로 합니다.
변수별 영향을 계수로 직접 해석할 수 있는 선형 회귀(Linear Regression)를 채택하였습니다.

## 🧠 문제 정의 & 해결 전략

**문제**
* 제조사: 설계 스펙에 따라 주행거리가 어떻게 달라질지 사전 파악이 어려움
* 소비자: 신차 공개 단계에서 실주행 데이터 없이 스펙만 제시되는 경우가 많음
* 정부/정책기관: 출시 전 보조금·효율성 평가 기준 마련이 필요함

**해결**
* 설계 스펙(원인) → 주행거리(결과) 인과관계를 선형 회귀로 모델링
* 회귀계수를 통해 어떤 스펙이 주행거리에 큰 영향을 주는지 직관적으로 해석 가능

## 🧭 분석 플로우

1. 문제 정의 → `y = f(x)` 설정
2. 데이터 수집 (Kaggle)
3. EDA (분포, 상관관계, 범주형 변수 탐색)
4. 전처리 (컬럼 정리, 매핑, 원-핫 인코딩, 표준화)
5. 모델링 (선형 회귀, VIF, p-value 기반 변수 제거)
6. 해석 및 인사이트 도출

## 📊 데이터셋

* **출처**: [Kaggle - Electric Vehicle Specifications Dataset 2025](https://www.kaggle.com/datasets/urvishahir/electric-vehicle-specifications-dataset-2025) (원본: [ev-database.org](https://ev-database.org/))
* 442개 전기차 모델, 22개 변수
* **y**: `range_km` (완전 충전 시 주행 가능 거리)
* **X**: `battery_capacity_kWh`, `efficiency_wh_per_km`, `top_speed_kmh`, `height_mm`, `drivetrain`, `segment` 등

## 🔧 전처리

* 결측치 많은 `number_of_cells`, 불필요한 `source_url` 제거 후 결측 행 삭제 처리
* `segment` 12개 등급 → Small / Medium / Large 3개 범주로 재분류했음
* `drivetrain`, `segment` 원-핫 인코딩 (`drop_first=True`) 처리
* 과적합 위험 또는 무관 컬럼 제거 (`brand`, `model`, `battery_type`, `fast_charge_port`, `cargo_volume_l`, `car_body_type`)
* 수치형 변수 StandardScaler 표준화 처리

## 📈 모델링 & 결과

* Train/Test 8:2 분할 (`random_state=2`)
* VIF 검사 → 모든 변수 VIF < 10 확인했음
* OLS 회귀 후 p-value > 0.05인 변수 4개 제거했음 (`torque_nm`, `fast_charging_power_kw_dc`, `seats`, `segment_Medium`)
* AIC/BIC 감소 확인 → 변수 제거 후 모델을 최종 모델로 선정했음
* 10-Fold Cross Validation 수행했음

| 평가 | R² | MSE | RMSE |
|------|-----|-----|------|
| Training Set | 0.9591 | 435.64 | 21.30 |
| 10-Fold CV | 0.9540 | 491.63 | 22.03 |
| Test Set | **0.9358** | 549.31 | 23.44 |

## 📌 주요 변수 해석

**양의 영향 (➕)**
| 변수 | 회귀계수 | 해석 |
|------|---------|------|
| `battery_capacity_kWh` | +93.26 | 배터리 용량 클수록 주행거리 증가 |
| `drivetrain_RWD` | +28.60 | AWD 대비 구동계 단순화로 에너지 절약 |
| `drivetrain_FWD` | +14.22 | AWD 대비 모터 1개 사용 → 주행거리 유리 |

**음의 영향 (➖)**
| 변수 | 회귀계수 | 해석 |
|------|---------|------|
| `height_mm` | -36.00 | 차량 높이 증가 → 공기저항 증가 |
| `efficiency_wh_per_km` | -19.64 | 전력 소비 클수록 주행거리 감소 |
| `segment_Small` | -16.45 | 소형차는 배터리 탑재 용량 제한 |
| `top_speed_kmh` | -12.38 | 고성능 설계일수록 에너지 소비 증가 |

## 🧩 한계 & 향후 과제

* 제조사 공인 스펙 기반이므로 실제 도로 환경(경사도, 에어컨 등)은 반영되지 않았음
* 기온, 교통상황, 운전 습관 등 외부 환경에 따라 실제 주행거리와 차이 발생 가능
* 향후 실주행 데이터 + 외부 환경 변수 추가, Ridge/Lasso 등 정규화 모델 비교 필요

## 🛠 기술 스택

Python 3 / pandas / numpy / matplotlib / seaborn / scikit-learn / statsmodels

## 📁 파일 구조

```
├── README.md
├── source_code.ipynb          # 전체 분석 코드
├── presentation.pptx          # 발표 자료
└── data/
    └── electric_vehicles_spec_2025.csv
```

## ▶️ 실행 방법

```bash
pip install pandas numpy matplotlib seaborn scikit-learn statsmodels
jupyter notebook source_code.ipynb
```

`data/` 폴더에 [Kaggle 데이터셋](https://www.kaggle.com/datasets/urvishahir/electric-vehicle-specifications-dataset-2025) 다운로드 후 실행하면 됨.
