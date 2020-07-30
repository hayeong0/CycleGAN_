
> [ Unpaired Image-to-Image Translation using Cycle-Consistent Adversarial Networks](https://arxiv.org/abs/1703.10593) (2017) 


논문에서 다루는 수식이 어렵지 않아 GAN에 대한 기본적인 이해가 있다면 쉽게 이해할 수 있을 것이고, 비전공자들에게 쉽게 설명하기 위해 최대한 쉬운 용어로 적었다. 

---



### 🔍 What is possible with CycleGAN?

다음은 논문에 소개된 이미지이다. 
CycleGAN을 이용하였을 때 Input image에 대해 얻을 수 있는 Output은 다음과 같다. Input 이미지를 모네, 반 고흐와 같은 아티스트들의 그림 스타일로 바꾸거나, 말을 얼룩말로, 사과를 오렌지로, 오렌지를 사과로, 아이폰 사진을 dslr로 초점을 변경한 결과물을 얻어낼 수 있다. 

![](https://images.velog.io/images/chy0428/post/d56f52f9-6a3c-4683-be1a-ec4147bc8290/image.png)
![](https://images.velog.io/images/chy0428/post/e59d9843-b96f-4070-8299-6aed5c492cb0/image.png)

---

### ❓How dose it work?
cycleGAN을 연구한 연구실에서 먼저 `pix2pix`를 만들었다. 따라서 cycleGAN을 이해하기 앞서 `pix2pix`에 대해 알아야 한다.  
![](https://images.velog.io/images/chy0428/post/0e99bf84-2d0b-40fc-8c0d-0add0baa426e/image.png)

---

### 📗 Pix2pix
pixel to pixel은 말 그대로 하나의 픽셀을 다른 픽셀로 바꿔준다는 의미이다. 아래 이미지와 같이 흑백사진을 컬러사진으로 바꾸거나, label만 부여한 input에 대해 실제 image로 generate하거나, edge만 있는 input에서 완전한 output을 만들어내는데 사용한다. 
![](https://images.velog.io/images/chy0428/post/4e80fd70-dba3-4f10-8e09-d38eeec4fb49/image.png)

---

### Pix2pix - loss function
Pix2pix의 loss function은 다음과 같다. G(x)와 y의 차이, 즉 pixel level의 차이를 최소화한 것을 loss로 두고 학습을 시키면 아래와 같은 결과가 나온다. Ground Truth가 정답 이미지인데, network가 생성한 output은 상대적으로 뿌옇게 보이는 것을 확인할 수 있다. 이런 현상이 발생하는 이유는 network는 어떤 색으로 칠해지는 것이 정답인지를 모르니 크게 손해보지 않는 중간 값을 채택하기 때문이라고 한다. 
![](https://images.velog.io/images/chy0428/post/8eb89621-26ea-49f1-9415-f6db1024a47e/image.png)

이 정도면 괜찮지않나 싶을 수 있지만, 사람이 보았을 때는 두 이미지 간에 차이가 명확하며, 정답 이미지를 구분해낼 수 있다. 여기서 중요한 intuition은 "사람이 할 수 있으면 다른 deep learning network가 할 수 있지 않을까?"이다. 사람 대신 다른 neural network가 이 역할을 하게끔 하자는 생각이다. pix2pix는 그 neural net으로 GAN을 도입하였다. 

![](https://images.velog.io/images/chy0428/post/1571a52b-90a3-4f3c-98fd-a8afd3529973/image.png)

---

### ❓ What is GAN?
GAN에 대하여는 간단한 개념만 짚고 넘어가도록 하겠다. GAN을 아주 쉽고 직관적으로 설명하는 글이 있어 사진을 참고하였다. [출처](https://post.naver.com/viewer/postView.nhn?volumeNo=16566740&memberNo=36733075)

![](https://images.velog.io/images/chy0428/post/705bd477-4435-44e9-a145-515fc5592fd9/image.png)
> GAN은 `Generative Adversarial Networks`의 약자로, 적대적 생성 신경망을 의미한다. 
처음 GAN을 제안한 Goodfellowsms GAN을 경찰과 위조지폐범으로 비유하였다. 위조지폐범은 최대한 진짜와 비슷한 위조지폐를 만들어 경찰을 속이기 위해 노력하고, 경찰은 진짜와 위조지폐를 정확히 판별하여 위조지폐범을 검거하는 것을 목표로 한다. 이러한 위조지폐범과 경찰의 경쟁적인 학습이 지속되다보면 어느 순간 위조지폐범은 진짜에 가까운 위조지폐를 만들어내게 되고, 경찰은 진짜와 위조지폐를 구별할 수 있는 확률이 50%으로 수렴하게 되어, 경찰은 실제 화폐와 위조지폐를 구분하기 어려운 상태가 된다. 
이처럼 **GAN은 위조지폐범에 해당하는 생성자 (Generator)와 경찰에 해당하는 구분자 (Discriminator)를 경쟁적으로 학습**시켜, 진짜같은 가짜를 만들어 내는데, 이를 `적대적 학습`이라 한다. 

![](https://images.velog.io/images/chy0428/post/2cb60b86-3706-4865-b23a-c8ee11f15c9a/image.png)
> 위 사진에서 GAN의 목적은 흑백 사진을 컬러 사진으로 만드는 것입니다. Generator 네트워크는 fake image로 Discriminator를 속이려하며, Discriminator 네트워크는 fake image를 구별해내려고 합니다.

![](https://images.velog.io/images/chy0428/post/7d4050b9-dd27-4e89-b526-51fa1a56a8a7/image.png)
> discriminator의 목적 함수이다. D net에 입장에서는 0에 가까우면 real, 1에 가까우면 fake 이미지로 구별한다. 먼저 G net가 만들어낸 결과는 fake(=1)에 가깝게 만들고, 실제 정답 이미지는 real(=0)에 가깝게 만드는 것이다. 
D는 어떤 이미지가 input으로 들어왔을 때, fake인지 real인지의 확률을 output으로 내보낸다. 0.9를 Output으로 낸 위의 사진 같은 경우, 90%의 확률로 fake라는 의미이다. `D(G(x))` 확률에 log를 씌워 optimize 한다고 할 때, log도 증가함수이기 때문에 이 확률 값을 maximize한다면, D는 G(x)가 fake 이미지일 경우 높은 확률 값을 output하는 제대로 된 훈련이 이루어질 것이다. 동시에 실제 이미지인 y를 보여주어, 실제 사진이 fake일 확률도 output하도록 한다. 이 경우, D가 output하는 확률이 0에 가까워야 하기 때문에 반대의 objective를 씌운다. 

![](https://images.velog.io/images/chy0428/post/d8bfd5c2-db47-4acd-a6f9-5b0841da3344/image.png)
> generator의 목적 함수이다. 앞에서 설명했던 D의 손실 함수는 두 개의 term의 합을 maximize해야 했으나, generator는 discriminator와 정반대의 목적을 가지므로, minimize한다. 

---

### GAN loss
![](https://images.velog.io/images/chy0428/post/80c269be-8cd5-45d3-a896-cadd4c473d90/image.png)
> 앞에서 살펴봤던 2개의 목적 함수를 합친 전체 수식이다. 여기서 가장 중요한 것은 G와 D를 경쟁관계로 만드는 것이다.

논문저자는 G의 관점에서의 loss function을 언급한다. 앞서 적었던 **G의 loss function이 언제나 fix된 수식이 아니라, D라는 moving target(동시에 같이 훈련되는)을 이겨내야 한다는 loss function이 중요**하다고 설명한다. 

---


### Pixel level loss + GAN loss
![](https://images.velog.io/images/chy0428/post/7d86e3af-2083-4a86-94cb-5852fbdeafbb/image.png)
> pix2pix의 loss는 pixel level loss와 GAN loss를 합쳐서 정의한다. 두 개를 합쳤을 때의 Output이 이전보다 퍼포먼스가 증가했음을 확인할 수 있다. 

---

### pix2pix 한계점
Pix2pix는 GAN loss를 합친 목적 함수를 이용하여 성능을 높였지만, training data가 pair로 존재해야만 한다는 한계점이 존재한다. 흑백 사진을 컬러 사진으로 바꾸기 위한 training dataset을 구성하기는 쉽지만, 아티스트의 그림을 실물 이미지로 바꾸기 위한 데이터셋을 구성하기는 현실적으로 불가능하다. 이런 한계점을 극복하기 위해 cycle GAN이 나오게 되었다고 한다. 
![](https://images.velog.io/images/chy0428/post/240b9ab3-5ef2-4dda-94a2-8f58d264ef38/image.png)

![](https://images.velog.io/images/chy0428/post/2c36ce0d-d0b9-452f-ba7e-7beb0d151f58/image.png)
> 좌측의 사진은 사이클 갠의 구조를 단순화한 모습이다. X와 Y는 우리가 서로 연결하고자 하는 두 Domain의 image이다. X도메인에서 Y로 맵핑하는 G function이 존재하고, 이것이 Y에 가까운가를 Y의 Discriminator인 Dy를 통해 검사 받게 된다. 반대의 경우도 마찬가지이다. 
이렇게 될 경우 문제점이 발생한다. 
X에서 Y로 맵핑할 경우 X의 성질을 유지하면서 Y로 style만 바꾸는 것이 주목적이 되는 것이 아니라, 그냥 Y도메인의 진짜 이미지처럼만 보이면 되기 때문에 input의 특성을 무시하게 되어 결국은 의미없는 맵핑이 된다. 그 결과로 input과 괴리감있는 결과가 나온다. 

---
### 📗 Cycle GAN

위에서 언급한 문제들을 해결하기 위해 추가적인 loss를 사용한다. Cycle GAN의 핵심은 **사진의 스타일을 바꾸되, 다시 원본 이미지로 복구 가능한 정도로만 바꾸는 것**이다. 즉, x도메인에서 y도메인으로의 단순 맵핑이 아니라, 다시 돌아오는 부분을 고려하여 input이미지로 잘 돌아올 수 있도록 제약조건을 거는 것이다. 
![](https://images.velog.io/images/chy0428/post/824a9919-739d-4cd7-8d95-55ab72a5dd10/image.png)

![](https://images.velog.io/images/chy0428/post/78731d67-a437-49ee-9caa-a73a75b4ab23/image.png)

> 그림을 사진으로 바꾸려고 한다고 할 때, 바꾼 사진을 다시 그림으로 바꿀 수 있어야 한다는 의미이다. 이를 위해, G net을 훈련시키는 동시에, 사진을 그림으로 바꿔주는 F net을 훈련시켜야 한다. x도메인에 G를 적용하고 (G(x)), F를 적용하면(F(G(x))) input이 사진으로 갔다가 다시 그림으로 돌아온다. 돌아온 결과 값이 원래 그림과 같아야 한다는 것이 제약조건이 된다. 

loss function을 살펴보자. 
![](https://images.velog.io/images/chy0428/post/0fb23f86-f57a-4692-909a-3229619b938c/image.png)
> 기존 GAN loss는 유지하고, 생성한 fake 이미지와 원본 이미지 X의 loss가 최소화 되어야 한다. 이것은 pixel level difference를 추가한 것과 비슷한 개념이다. 


> 반대 방향도 마찬가지이다. 
![](https://images.velog.io/images/chy0428/post/82b0cd88-5d37-409e-8f17-a00acc400e2d/image.png)

### CycleGAN loss
사이클 갠은 이 두 방향의 loss를 합친 목적 함수를 이용한다. 
![](https://images.velog.io/images/chy0428/post/e655fe37-838f-432a-add0-ee3b57c0ad94/image.png)

#### Full objective
![](https://images.velog.io/images/chy0428/post/3751098d-5d59-4910-aaec-c248512ed777/image.png)
λLcyc는 hyper-parameter이다. λ는 두 objective의 relative importance를 컨트롤한다. 
![](https://images.velog.io/images/chy0428/post/8aeee469-b766-4fec-8d6f-44229849c0c4/image.png)
논문 저자는 위의 수식을 푸는 것을 목표로 하였다. 논문에서 소개된 사이클 갠 모델은 2개의 'autoencoder'를 훈련시키는 것으로 볼 수 있다. 하나의 오토 인코더 F ◦ G : X → X와 G ◦ F : Y → Y 가 함께 학습한다. 인코더 내부에서는 input 이미지를 다른 도메인으로 변환하는 중간 representation을 통해 이미지를 자체적으로 매핑한다. 이런 설정은 'adversarial auto-encoders'의 특별한 경우이다. 이는 임의의 target분포와 일치하도록 오토인코더의 bottleneck layer를 훈련시키기 위해 adversarial loss를 사용한다. 
여기서 X → X 자동 인코더의 대상 분포는 도메인 Y의 분포이다. 
저자는 이 논문의 방법을 전체 목표와 비교하였을 때 adversarial loss LGAN과 cycle consistency loss Lcyc 단독을 포함하여, 두 개의 objective가 고품질의 결과를 얻는데 중요한 역할을 했음을 결과를 통해 얻어낼 수 있다고 설명한다. 
또한 only cycle loss (한 방향으로만의 사이클 로스)로 고안한 모델의 성능을 테스트했을 때, 이는 훈련에 충분하지 않았음을 언급한다. 

다음은 해당 손실 함수를 이용했을 때 얻어낸 output이다. 
![](https://images.velog.io/images/chy0428/post/479ad5aa-9230-4194-acad-7e0413831e69/image.png)


![](https://images.velog.io/images/chy0428/post/2ef172ab-ff92-413a-889d-d22e383e4f78/image.png)

---
Reference
https://youtu.be/Fkqf3dS9Cqw
https://arxiv.org/abs/1703.10593


---

관심가거나 연구에 필요한 논문들을 읽기는 하는데, 시간이 없다는 이유로 정리를 소홀히 했다. 내가 직접 글로 적으면서 정확한 이해를 하는 과정을 중요하게 여겨야겠다. 
> 간단하게 설명할 수 없으면 제대로 이해하지 못하는 것이다. 
-- 알버트 아인슈타인 
