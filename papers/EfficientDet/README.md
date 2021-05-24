# EfficientDet

### `EfficientDet`을 알기 위해서는 우선 `EfficientNet`을 알아야 한다.

## ✔️EfficientNet

---

[](https://arxiv.org/pdf/1905.11946.pdf)

CNN에서 성능(accuracy)을 올리기 위해 기존 모델을 확장시키는 방법을 주로 사용해 왔다. 유명한 모델 중 하나인 resnet으로 예를 들 수 있다. (resnet18, resnet50, resnet101...)

![EfficientDet%208233963be159434cbee48287a9c77310/Untitled.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled.png)

위의 그림과 같이 model을 확장시키는 다양한 방법들이 있다.

- **width scaling** : filter의 개수(channel 수)를 늘려줌
- **depth scaling** : layer를 더 깊게 쌓는다.
- **resolution scaling** : input image의 해상도를 높여준다. (ex: (512, 512) → (640, 640))
- **compound scaling** : 위 3가지 방법을 모두 사용하는 것. 기존 방식들은 이를 잘 사용하지 않음.

본 논문에서는 **width($w$), depth($d$), resolution($r$)** 세 가지를 나누어 하나의 scale factor만 변화시키며 정확도를 측정함. 아래의 결과를 보면 **resolution을 키웠을 때, 나머지 factor보다 꾸준히 성능이 증가**하는 것을 볼 수 있다.

![EfficientDet%208233963be159434cbee48287a9c77310/Untitled%201.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled%201.png)

아래는 위 그림과 비슷하게 **depth, resolution를 고정**시키고 **width의 변화**만 주면서 정확도가 어떻게 변하는지 확인한 실험. depth보다는 resolution을 키우는 것이 정확도 향상에 더욱 효과적인 것을 알 수 있다. 또한 1개 혹은 2개의 factor보다 3가지(width, depth, resolution) 모두 키워주는 것**(이하 Compound Scaling)**이 가장 성능 향상 효과를 많이 보았다.

![EfficientDet%208233963be159434cbee48287a9c77310/Untitled%202.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled%202.png)

해당 논문에서는 새로운 Compound Scaling을 제안하고, 이는 아래의 식을 만족시켜야 한다는 것.

**(위에서 다양한 실험을 통해 가장 효과적인 Compound Scaling를 찾은 것 같다... 아마도)**

![EfficientDet%208233963be159434cbee48287a9c77310/Untitled%203.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled%203.png)

여기서 width, resolution값에 제곱을 해주는 이유는 depth가 깊어질수록 flops가 비례해서 증가하지만 width, resolution은 (가로, 세로) 값을 곱해주기 때문이다.

- **ex) width, resolution은 2배 증가시 FLOPs는 4배가 증가한다.**

EfficientNet의 구조는 다음과 같고

![EfficientDet%208233963be159434cbee48287a9c77310/Untitled%204.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled%204.png)

모델간의 성능 비교는 다음과 같다. 

![EfficientDet%208233963be159434cbee48287a9c77310/Untitled%205.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled%205.png)

참고로 **EfficientNet-B0 (base)** 는 MnasNet과 같은 search sapce를 사용했다고 한다. AutoML로 찾은 것 같음..? 또한 EfficientNet-B0에서 $\alpha=1.2, \beta=1.1, \gamma=1.15$가 최적값이라고 한다. 이를 고정하고 $\phi$를 키우면서 model의 size를 키울 수 있다.

**표를 보면 알 수 있듯 성능 대비 parameter수, FLOPs가 압도적으로 적은 것을 볼 수 있다.**

## ✔️EfficientDet

---

[](https://arxiv.org/pdf/1911.09070.pdf)

학습 속도와 정확도는 Trade-off 관계를 가지기 때문에 높은 정확도로 빠른 시간안에 학습하는 것은 굉장히 어렵다. 본 논문에서는 이러한 모델을 설계하기 위해 고려해야 할 2가지를 제시한다.

1. **Efficient multi-scale feature fusion**
    - FPN은 multi-scale feature fusion에서 널리 사용됨. 논문에서는 아래와 같이 다른 scale을 fusion할 때, 단순히 이전 feature(?) 값들을 더하는 것을 지적하였음.

        ![EfficientDet%208233963be159434cbee48287a9c77310/Untitled%206.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled%206.png)

        서로 다른 input feature는 다른 resolution을 가지기 때문에 다른 가중치를 두고 더하는 방법을 제시함. 그것이 **weighted bi-directional FPN (BiFPN)** 이다.

2. **model scaling**
    - EfficientNet에서 연구 했던 model scaling을 Object Detection에도 적용했다는 것을 의미하는 듯 하다.
    - width, depth, resolution 세 가지를 동시에 고려하는 **Compound Scaling.**

본 논문에서 제안하는 내용은 다음과 같다.

1. **BiFPN 구조**
2. **Object Detection에 Compound Scaling적용**
3. **1, 2를 이용한 EfficientDet구조**

### <BiFPN>

![EfficientDet%208233963be159434cbee48287a9c77310/Untitled%207.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled%207.png)

- (a) : 전통적인 FPN 구조
- (b) : FPN에 추가로 bottom-up pathway를 추가
- (c) : AutoML의 Neural Architecture Search (NAS)를 FPN에 적용한 것 (매우 복잡해 보인다..)
- (d) : BiFPN구조

추가로 (c), (d)는 **Cross-scale connection**을 적용 (이름 그대로 **다른 scale에 connection**하는 것)

### **Weighted Feature Fusion**

---

- FPN에서 서로 다른 resolution feature를 합칠 때, 일반적인 방법은 모두 같은 resolution으로 resize한 뒤 더해주는 방법. 본 논문에서는 3가지 방법을 제시했다.

    $**(O$: output, $w$: weights, $I$: input$)$**

**<기존 방식>**

$$O=\sum_iI_i$$

**<Unbounded fusion>**

가중치를 주는 방식은 scalar, vector, multi-dimensional tensor 3가지가 있다. 

**- scalar는 feature마다 가중치를 부여하는 방식.
- vector는 channel마다 가중치를 부여하는 방식.
- multi-dimensional tensor는 pixel마다 가중치를 부여하는 방식.**

논문에서는 scalar 방식이 정확도, 연산량 측면에서 더 효율적인 것을 밝혔다. 하지만 **unbounded fusion는 불안정할 수 있기 때문에 weight nomalization을 사용**했다고 한다.

$$O=\sum_iw_i \cdot I_i$$

**<Softmax-based fusion>**

각 weight에 softmax를 적용시킨 것. **GPU에서 slowdown이 발생하는 문제점이 존재**함. 아래 그림(Soft vs Fast)을 보면 다음에 설명할 Fast fusion을 사용하는 것이 비슷한 성능에서 큰 속도 향상을 볼 수 있는 것을 알 수 있음.

$$O=\sum_i \frac{e^{w_i}}{\sum_je^{w_j}} \cdot I_i$$

**<Fast normalized fusion>**

해당 논문에서 최종으로 제안한 fusion 방법.

ReLU를 통과하기 때문에 모든 weight는 0이상인 값을 가지고 분모가 0이 되는 것을 방지하기 위해 분모에 $\epsilon=0.0001$ 을 더해 주었다. weight값이 0~1 사이의 값을 가지는 것은 softmax와 비슷하지만 연구를 통해 softmax보다 좋은 성능을 보인다고 밝혔다.

$$O=\sum_i\frac{w_i}{\epsilon+\sum_jw_j}\cdot I_i$$

![EfficientDet%208233963be159434cbee48287a9c77310/Untitled%208.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled%208.png)

Softmax vs Fast

---

### <EfficientDet>

![EfficientDet%208233963be159434cbee48287a9c77310/Untitled%209.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled%209.png)

위는 EfficientDet의 구조이다. 그림에서 보듯 EfficientNet을 backbone으로 사용하였고 BiFPN 구조를 P3 ~ P7 feature에 적용하였다. 

특히 눈에 띄는 점은 BiFPN구조를 반복 사용했다는 점이다. 이를 통해 더 높은 level의 feature fusion이 가능하다고 한다. 

### Compound Scaling

---

![EfficientDet%208233963be159434cbee48287a9c77310/Untitled%2010.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled%2010.png)

위의 표를 보면 **input size (resolution), backbone network, BiFPN, Box/class를 키워**주는 방식으로 **Compound Scaling**해주는 것을 알 수 있다. 키우는 정도는 아래와 같이 표현할 수 있다.

![EfficientDet%208233963be159434cbee48287a9c77310/Untitled%2011.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled%2011.png)

![EfficientDet%208233963be159434cbee48287a9c77310/Untitled%2012.png](EfficientDet%208233963be159434cbee48287a9c77310/Untitled%2012.png)

위는 다양한 모델 간의 성능 비교표입니다. EfficientNet과 비슷하게 더 좋은 성능, 더 적은 parameter, FLOPs로 구성된 것을 볼 수 있습니다.