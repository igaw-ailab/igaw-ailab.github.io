---
layout: post
title:  "User Response Learning for Directly Optimizing Campaign Performance in Display Advertising"
author: "Neo"
tags: PaperReview
---

### Introduction

> RTB(Real-Time Bidding) 기반의 디스플레이 광고는 2009년에 등장한 이후로 굉장한 인기를 얻어왔습니다. RTB란 실시간으로 각 노출 기회에 대한 입찰 가격을 계산하는 것으로 광고주들은 입찰 가격을 계산하기 위해서, 광고를 내보냈을 때 얼마나 클릭 혹은 전환이 일어날 것인지를 예측해야 합니다. 그래야 높은 CTR 혹은 CVR이 예상되는 Ad inventory에 더 많은 예산을 할당해 높은 가격으로 입찰할 수 있기 때문입니다. 그러나 경매에서 승리해 노출 기회를 얻었다고 하더라도 클릭을 하지 않고 전환이 일어나지 않는다면, 이는 고스란히 비용으로 남게 됩니다. 그러므로 사용자 반응에 대한 예측 정확성은 최종 효용 결정에 영향을 미친다고 볼 수 있습니다. 

### The Proposed Solution and Problem Setup

> 이 논문에서는 두 번째 가격 경매 상황에서 독립적으로 CTR 추정기를 학습시키는 방법 대신에 CTR 예측이 직접적으로 경매의 결과와 캠페인의 성과를 결정하도록 하였습니다. 또한 CTR 모델 학습을 위한 그래디언트 함수는 경매의 시장 경쟁 정도를 반영합니다. 이는 경쟁이 심한 경우에 대해서 학습 초점을 맞추어 시장 가격에 가깝게 입찰할 수 있게 해주어, 결과적으로 비용 효율적인 입찰이 되도록 합니다. 

> 광고주는 일반적으로 DSP를 고용하고, 이들은 광고주들의 광고를 사용자에게 전달하는 역할을 하며 Ad Exchange들과 연결되어 있습니다. 주어진 광고 캠페인에서, Ad Exchange로부터의 입찰 요청(Bid request)이 미리 설정한 Target rule과 일치하면, DSP Bidding engine은 이 광고 노출 기회에 대해서 CTR 혹은 CVR을 예측하고, 이 예측 결과를 입력으로 사용해서 입찰 가격을 계산합니다. 이를 공식으로 설명하기 위해 Table 1의 표기법을 따릅니다.

![image](https://user-images.githubusercontent.com/49015329/55148243-2edc3c00-518b-11e9-8c25-6bc4f446ddbd.png)

### Method

![image](https://user-images.githubusercontent.com/49015329/55148286-3d2a5800-518b-11e9-929f-1b5fe2404be2.png)


> 논문에서는 위험과 수익을 반영한 Risk-Return(RR) 모델을 제안합니다. 식 (8) 에서 RR 함수는 유저 반응이 Positive(y=1)이면 수익이 되고(return), 반응이 Negative(y=0)이면 반대로 리스크(risk)가 됩니다. 

![image](https://user-images.githubusercontent.com/49015329/55148301-474c5680-518b-11e9-90ff-91d4dfa7f021.png)


> 새로운 최적화 함수는 위와 같습니다.

![image](https://user-images.githubusercontent.com/49015329/55148318-4e736480-518b-11e9-8936-1f93508f9402.png)

>θ 에 관해서 수식 (8)의 그래디언트는 위와 같습니다. 

![image](https://user-images.githubusercontent.com/49015329/55148333-5a5f2680-518b-11e9-84a3-36e1616423c6.png)


>또한 논문에서는 CTR 추정 모델과 입찰 함수 중 하나를 최적화 하는 것이 아니라, 한 쪽의 변화가 다른 한 쪽의 변화를 이끌기 때문에 둘을 함께 최적화 하는 Algorithm1을 제안합니다. E-step에서는 CTR 추정 함수의 가중치인 θ를 최적화하고, M-step에서는 입찰 함수인 b(*)를 최적화합니다. 

### Experiments

> 논문에서는 CTR 추정에 널리 사용되는 Logistic Regression을 기본 모델로 사용하였으며, 손실 함수로 크로스 엔트로피를 사용하는 CE 모델을 베이스 라인으로 잡았습니다. 

![image](https://user-images.githubusercontent.com/49015329/55148351-63e88e80-518b-11e9-8edc-fe303a7d9eb2.png)

> 논문에서 제안한 RR 모델은 베이스 라인 CE 모델보다 Profit에서 평균적으로 78.2% 높은 것으로 나타났습니다. ROI에서는 평균적으로 217% 높은 것으로 나타나 CE 모델보다 훨씬 비용 효율적이라는 결론을 내릴 수 있습니다. 

![image](https://user-images.githubusercontent.com/49015329/55148372-6b0f9c80-518b-11e9-9a4a-9ac775185c82.png)

> 최종적으로 4개의 CTR 추정 모델들을 전체적인 캠페인 성과에 대해서 비교해보면 다음과 같습니다. (1) CTR은 경매에서 승리해 얻은 광고 노출 기회의 질을 나타내기 때문에, 모델이 CTR이 높은 광고 노출 기회를 타겟팅 할 수 있는지 여부를 나타낸다고 볼 수 있으며 RR 모델이 가장 뛰어난 것으로 나타납니다. (2) CPM 모델이 경제적으로 광고 노출 기회를 얻는지를 나타낸다고 볼 수 있습니다. RR 모델은 베이스 라인 CE 모델과 비슷한 성능을 달성했습니다. (3) eCPC는 Profit 그리고 ROI와 관련이 있으며, 논문에서 제안한 RR 모델은 대부분의 캠페인에서 비용을 적게 지불합니다. 이를 통해 높은 Profit과 ROI를 달성한 이유도 설명할 수 있습니다. (4) RR 모델은 수익이 나지 않을 광고 인벤토리에 대해서 과지출을 피하기 때문에, 비교적 승률이 낮다고 할 수 있습니다. 결과적으로는 승률은 낮지만 더 나은 Profit과 ROI를 달성하기 때문에 비용 효율적이라고 할 수 있습니다. 

### Conclusion

> 논문에서 RTB 디스플레이 광고에서 직접적으로 캠페인 Profit을 최적화하기 위해 새로운 유저 반응 모델 학습 방법을 제안했습니다. 시장 가격의 분포와 입찰 효용이 추가적인 재가중 요소로써 그래디언트의 업데이트에 기여한다는 점을 확인했고, 테스트 결과 논문에서 제안한 모델이 비용 효율적이라는 것을 확인할 수 있었습니다.
