# [[DACON] 저해상도 조류 이미지 분류 AI 경진대회](https://dacon.io/competitions/official/236251/overview/description)

## 대회 설명
- 입력으로 들어오는 저해상도의 64x64 크기의 조류 이미지로부터 종을 분류하는 AI 알고리즘 개발

&nbsp;

## 평가 산식
- Macro F1 Score

&nbsp;

## 주의 사항
- PyTorch <= 1.13 (ML-Decoder 에러)

&nbsp;

## Hyper-parameter
- `Seed`: 42

- `epochs`: 50

- `early stop count`: 5

- `Learning rate`: 1e-5

- `batch size`: 8

- `num workers`: 8

- `image size`: (448, 448)

&nbsp;

## Architecture

- `Model`: eva02_large + MLDecoder

- `Optimizer`: AdamW

- `Loss function`: LabelSmoothing(0.75), DiceLoss(0.25)

- `Scheduler`: None

- `Ansemble`: StratifiedKFold, StratifiedShuffleSplit

&nbsp;

## 결과
- Public LB - Score 0.98044, Rankings 6/385
![public](https://github.com/ssun-g/machine_learning/assets/33628588/9d5c84e2-5e06-4f52-a0d8-cc8a2f3ff526)

&nbsp;

- **Private LB - Score 0.98049, Rankings 8/385**
![private](https://github.com/ssun-g/machine_learning/assets/33628588/351f6bae-b3ce-44da-bb35-c97ca4fbe8bb)
