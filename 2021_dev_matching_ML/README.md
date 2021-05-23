# Image Classification
7개의 class(dog, elephant, giraffe, guitar, horse, house, person) 로 구분 가능한 (3, 227, 227) shape의 이미지를 분류하는 Task</br></br>
Public LB Score __97.143__ </br>
Public LB Ranking __25__ </br>
Private LB Score __97.143__ </br>
Private LB Ranking __16__ </br>

### Hyper-parameter

Seed: 0

epochs: 100

early stop count: 10

Learning rate: 1e-3

batch size: 32

num workers: 4

### Architecture

Model: swsl_resnext50_32x4d (pretrained)

Optimizer: AdamW

Loss function: LabelSmoothingLoss(), smoothing=0.5

Scheduler: ReduceLROnPlateau(T_max=3)
