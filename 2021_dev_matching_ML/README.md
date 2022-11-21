# [대회 링크](https://career.programmers.co.kr/competitions/1109)
## Image Classification
7개의 class(dog, elephant, giraffe, guitar, horse, house, person) 로 구분 가능한 (3, 227, 227) shape의 이미지를 분류하는 Task</br></br>
__Public LB Score 97.143__ </br>
__Public LB Ranking 25__ </br>
__Private LB Score 97.143__ </br>
__Private LB Ranking 10__ </br>
![스크린샷 2022-11-21 오전 10 30 14](https://user-images.githubusercontent.com/33628588/202941968-0ced9808-dcf2-40aa-84e6-3262e5bb008c.png)


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
