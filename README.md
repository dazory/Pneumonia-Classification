# Pneumonia-Classification

* Report : 이메일(12181851@inha.edu)로 문의 바람

## 📋 Description


| 항목         | 내용                                                         |
| ------------ | ------------------------------------------------------------ |
| 프로젝트명   | 폐렴 classification                                      |
| 기간         | 2021.05. ~ 2021.06.                                      |


<br>


## 💁‍♂️ 배경

**폐렴**은 폐의 염증이 생긴 상태의 중증 호흡기 감염병이다. 항생제 개발 이후 폐렴에 의한 사망률이 많이 줄었지만 호흡기 질환 중 여전히 사망자 수 1위에 해당하는 질병이고, 현재 covid 유행과 더불어 폐질병에 대한 관심과 연구가 커지고 있다.

이미지 분류를 위해서 정상적인 폐와 그렇지 않은 폐의 사진에 **흐릿하게 보이는 병변 유무**에 주목해볼 필요가 있다. 이 뿌연 병변을 의학용어로 일명 **GGO**라 불린다. GGO는 모든 폐질환에서 관찰되며 병변에 따른 위치 특징들이 있다.

* `박테리아성 폐렴`: GGO가 침을 뱉은 것처럼 병변이 폐의 중심, 아래쪽에 뭉쳐 있음
* `바이러스성 폐렴`: 폐 전체영역으로 GGO들이 펴져 있어 전체적으로 뿌옇게 보임<sup>[[2]](#footnote_2)</sup>.

<img src="https://user-images.githubusercontent.com/81455416/126033593-f9d1c559-4555-40a6-815a-121569cae89b.png" alt="normal-vs-pneumonia" style="width:100px;" />

### ● 데이터 특성

CXR은 나이, 인종, 성별, 가지고 있는 병변 등에 따라 매우 다양한 형태를 띠므로 CXR 이미지를 딥러닝 모델에 사용하기 위해서는 아주 많은 양의 데이터가 필수적이다. 하지만 현재 공개된 의료영상은 환자의 프라이버시 문제로 인해 **양이 적다**. 또한 질환을 갖지 않은 사람이 CXR 촬영을 하게 될 확률이 적어 **데이터의 불균형**도 심하다. 

<br>


## ⚙ 실험 환경


| 항목         | 내용                                                         |
| ------------ | ------------------------------------------------------------ |
| 프로그래밍 언어   | 파이썬(Python3)                                      |
| 주요 프레임워크         | keras |
| 개발 환경 | Google Colab Pro, RAM 26GB |
| Dataset | Kaggle-Pneumonia & COVID-19 Image Dataset(License: CC BY-SA 4.0): (총 5,684장의 흉부 X-ray jpeg파일) |


<img src="https://user-images.githubusercontent.com/81455416/126033667-ae2e8409-8a77-4e95-a00c-7573cc21c89c.png" alt="dataset" style="width:600px;" />

**데이터 세트 분류**

5,684장의 전체 CXR(Chest X-ray) 데이터를 섞은 뒤(shuffling), 8:1:1 비율로 쪼개 주었다. 박테리아성 폐렴은 0, 바이러스성 폐렴은 1, 정상은 2의 값을 의미하도록 one-hot encoding하여 라벨링해주었다. 아래의 표는 데이터세트 분류의 결과를 보여준다.

![image](https://user-images.githubusercontent.com/81455416/126034424-a96cf94c-7e07-42b9-976b-85dcd217c81d.png)


<br>


## 🎲 실험 및 결과

### 🎲실험1: 데이터 전처리🎲

#### 실험 환경

![image](https://user-images.githubusercontent.com/81455416/126034468-d7f0fc9a-8e16-4f28-904e-940c4f95ccec.png)

144번째 layer까지만 freeze하고, 나머지 layers는 학습 가능하게 설정하였다. 

<br>

데이터의 양이 적으므로 Validation dataset에 모델의 성능이 의존할 확률이 높다. 이러한 경향을 살펴보기 위해 아래의 표와 같이 5가지의 경우에 training dataset과 validation dataset을 8대 1 비율로 **Cross-validation**을 수행하여 그 결과를 확인했다.

<img src="https://user-images.githubusercontent.com/81455416/126033859-964f71fc-d024-48ec-a93c-32e94b7a125e.png" alt="cross-validation-result" style="width:600px;" />

#### 결과

**① 데이터가 불균형한 case1~3에 대한 결과**

![image](https://user-images.githubusercontent.com/81455416/126034595-433e4da9-6034-474b-aad4-c45ddd58a51d.png)

* case1에 비해 case2와 case3의 성능이 더 좋다.
  
  ⇒ 데이터를 증강하는 것이 모델의 성능 개선에 효과가 있음
    
* case2와 case3에 대해서는 유의미한 차이가 없다.

**② 균형적인 데이터를 갖는 case4~5에 대한 결과**

![image](https://user-images.githubusercontent.com/81455416/126034609-6ff427ce-7a10-451a-82b2-cd7b9a927dbc.png)

* case4보다 case5의 그래프가 더 앞선다.
    
  ⇒ case5의 성능이 더 향상됨

**③ 결론**

* case2, case3, case5를 비교했을 때 데이터가 일정 범위 이상으로 증강된 경우 모델 성능 측면에서 유의미한 차이가 없다.
    
  ⇒ 현실에 있음 직한 유의미한 데이터로 증강하기에는 사용한 증강 기법이 표현할 수 있는 결과 범위가 한정되었기 때문으로 해석할 수 있다.
따라서 추후 보다 다양한 증강 기법을 적용하거나 GAN등의 새로운 증강 기법을 적용한다면 보다 성능이 향상될 것으로 기대된다.


<br>

### 🎲실험2: Optimization🎲

#### 배경

batch size와 learning rate는 trade off관계를 갖는다. 이를 유의하여 batch size와 learning rate를 조절하였다.

![image](https://user-images.githubusercontent.com/81455416/126034214-a92c96e0-17b4-4ab9-bb0e-26d7153e7197.png)

#### 결과

ResNet50의 batch size와 learning rate를 바꿔가며 실험한 결과는 아래 표와 같다. 표에 의하면 batch size가 8~16 사이일 때 가장 좋은 성능을 내는 것이라 판단된다.

![image](https://user-images.githubusercontent.com/81455416/126034231-a09f1884-b782-45e1-93e1-86d198f2ce5b.png)

batch size와 learning rate는 trade off관계가 있다. 따라서 batch size를 줄이는 대신 learning rate를 키우면 같은 효과를 얻을 수 있다. 따라서 이 프로젝트에서는 batch size=32, learning rate=0.0001로 설정하였다.


<br>


### 🎲실험3: training 영역에 따른 비교🎲

#### 배경

fine-tuning을 위한 적당한 양의 layer를 찾기 위하여 ResNet50의 학습 가능한 layer를 변화시키며 실험을 해보았다. 이때 사용한 데이터세트는 아래 그림과 같이 구성되어 있다. augmentation을 적용하여 original dataset에 비해 약 4배 증강시켰으며 클래스별로 균형을 맞추었다.

![image](https://user-images.githubusercontent.com/81455416/126034261-8cff63eb-2759-4a54-b677-71a1479defd4.png)


#### 환경

기존의 144번째 layer까지 freeze됐던 마지막 컨볼루션 레이어를 114번째 layer까지로 freeze영역을 감소시켜 실험을 진행하였다. Trainable parameter는 18,894,595개로 144번째 layer부터 학습시켰던 경우보다 3,338,240개 증가하였다. transfer learning의 경우 base model의 learning rate의 10분의 1배를 보통 사용하므로 0.001을 기준으로 learning rate를 변화시켜가며 그래프를 관찰하였다. 


#### 결과

learning rate에 따른 결과는 아래와 같다.

![image](https://user-images.githubusercontent.com/81455416/126034994-4f07ff4d-f336-43a4-8bf5-991440709e54.png)

train영역을 증가시켰을 때 오버피팅이 발생하는 등 더 나은 성능을 내지 못하였다. 따라서 우리의 dataset에서는 144 layer부터 학습시켰을 때가 적절한 결과를 낼 수 있음을 알 수 있었다.


<br>


### 🎲실험4: best model 고안🎲

#### 배경

기존 모델에서 FC layer를 하나 더 추가하고, dense layer의 input feature vector의 차원을 수정하여 아래와 같이 모델을 설계하였다.

![image](https://user-images.githubusercontent.com/81455416/126035053-4f9aa768-3d0a-4f60-abce-07d8bac83053.png)

### 결과

![image](https://user-images.githubusercontent.com/81455416/126035089-5eebeb20-724f-4df6-82bb-b25da6ed6397.png)


<br>


## 🎈 Test

### confusion matrix

best model의 confusion matrix의 결과는 다음과 같다.	

![image](https://user-images.githubusercontent.com/81455416/126035115-7d63900c-9331-4d5e-a9fd-f1cb40ed24fd.png)


![image](https://user-images.githubusercontent.com/81455416/126035133-878b0980-8417-4b3a-920e-ed36fca508ac.png)


영상 판독 전문가의 판독률(69%)과 비슷하거나 더 나은 성능을 확인할 수 있었다. 그러나 박테리아성 폐렴과 바이러스성 폐렴의 특징을 구분하여 학습하는 것이 어려워 normal class에 비해 Precision과 Recall값이 낮은 경향을 보였다는 것을 알 수 있었다.

<br>

### Convolutional Activation Map

![image](https://user-images.githubusercontent.com/81455416/126035158-f495e546-4df0-46f0-a19d-366ca0535c86.png)


<br>


## 🤩 결론

CXR 데이터는 공개된 데이터의 양이 적고 불균형하다는 특징이 있다. 이러한 데이터에 확률적으로 다양한 증강기법을 동시에 적용한 결과 모델의 성능이 향상되었다. 하지만 데이터의 양이 일정수준 이상으로 증강된 경우 유의미한 효과는 없었다. 이는 현재의 증강 기법이 유의미한 데이터를 만들어내기에 한계가 있음을 의미한다. 따라서 추후 보다 다양한 증강기법을 사용한다면 모델의 성능을 개선시킬 수 있을 것이라 예상된다. 또한 ImageNet등의 natural image에서 general하게 학습된 pre-trained weights를 활용하는 것이 의료영상 이미지와의 유사도가 크지 않음에도 불구하고 높은 효과를 보이는 것을 알 수 있었다. 따라서 의료영상 분야에서 전이학습을 활용하여 정보량 부족한 조기 검진 분야에서 인간의 시각적 인지능력을 보조하고, 진단에 필요한 시간과 비용을 절약할 수 있을 것이라 기대된다.


<br>


## 😏 References

1. “흉부영상의학 용어사전”, 2016년 대한흉부영상의학회 정책과제, 흉부영상의학 용어위원회, https://kstr.radiology.or.kr/image/popup/2017/%C8%E4%BA%CE%BF%B5%BB%F3%C0%C7%C7%D0%C8%B8_%C8%E4%BA%CE%BF%B5%BB%F3%C0%C7%C7%D0_%BF%EB%BE%EE%BB%E7%C0%FCPPT.pdf
2. “Tuberculosis and Mycobacterial Pneumonia”, Cancer Therapy Advisor, Katarzyna Mastalerz, 2017, https://www.cancertherapyadvisor.com/home/decision-support-in-medicine/hospital-medicine/tuberculosis-and-mycobacterial-pneumonia/
3. “The effect of batch size on the generalizability of the convolutional neural networks on a histopathology dataset”, IbrahemKandel, 2020.04
4. “Chest X-Ray Analysis of Tuberculosis by Deep”, Sergii Stirenko et al.
5. “Weakly Labeled Data Augmentation for Deep Learning A Study on COVID-19 Detection in Chest X-Rays”, Sivaramakrishnan Rajaraman and Sameer Antani, 30 May 2020
6. “Efficient GAN-based Chest Radiographs (CXR) augmentation to diagnose coronavirus disease pneumonia”, Saleh Albahli, 2020
7. Identifying Medical Diagnoses and Treatable Diseases by Image-Based Deep Learning, Daniel S.Kermany
8. “CNN-visualization-Grad-CAM “, github, https://tyami.github.io/deep%20learning/CNN-visualization-Grad-CAM/
9. 김민규; 배현진. 딥러닝 기반 의료영상 분석을 위한 데이터 증강 기법. Journal of the Korean Society of Radiology, 2020, 81.6: 1290-1304.
10. “The effect of batch size on the generalizability of the convolutional neural networks on a histopathology dataset”, Ibrahem KandelMauro Castelli
11. 영상 판독 전문가의 판독률 출처: 한국과학기술원(KAIST) 예종철 바이오및 뇌공학과 교수 연구팀 (2020)


<br>


## 📝 Contact
> If you have any questions, please email below. <br>
> 
> * dazory: 12181851@inha.edu
