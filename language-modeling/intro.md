# Language Model

## Introduction

이전 챕터까지 우리는 단어 또는 문장을 입력으로 받아서, 어떤 값으로 맵핑(mapping)해주는 방법에 대해서 다루었습니다. 그 값을 통해 해당 단어 또는 문장을 분류하기도 하고 군집을 형성 할 수도 있었습니다. 이러한 방법들도 매우 쓰임새가 많고, 정말 중요합니다. 하지만, 우리는 여기서 더 나아가 컴퓨터로 하여금 필요에 따라 자연스러운 문장을 만들어내도록 하는 방법에 대해 다루어 보도록 하겠습니다.

언어 모델(Language Model)은 문장의 확률을 나타내는 모델 입니다. 즉, 우리는 언어 모델을 통해 문장 자체의 출현 확률을 예측 하거나, 이전 단어들이 주어졌을 때 다음 단어를 예측할 수 있으며, 결과적으로 우리는 주어진 문장이 얼마나 자연스러운 유창한(fluent) 표현인지 계산 할 수 있게 됩니다. 예를 들어 우리는 아래와 같은 문장이 주어졌을 때, 빈칸을 어렵지 않게 메꿀 수 있습니다.

* 버스 정류장에서 방금 버스를 ㅇㅇㅇ.
  1. 사랑해
  2. 고양이
  3. 놓쳤다
  4. 사고남

우리는 정답이 3번 '놓쳤다'라고 쉽게 맞출 수 있습니다. 4번 '사고남'의 경우에는 앞 단어가 '버스를' 대신에 '버스가' 였다면 정답이 될 수도 있었겠지요. 4번을 정답이라고 할 경우에는 뜻은 이해할 수 있지만 뭔가 어색함이 느껴지는 문장이 될 겁니다. 

|번호|문장|
|-|-|
|1|저는 어제 점심을 먹었습니다.|
|2|저는 2018년 4월 26일 점심을 먹었습니다.|

위의 두 문장 중 1번 문장을 접할 기회는, 2번 문장을 접할 기회보다 훨씬 많을 겁니다. 수많은 단어들이 있고, 그 단어들간의 조합은 더욱 많이 존재 합니다. 그러한 조합들은 동등한 확률을 갖기보다는 자주 나타나는 단어나 표현(단어의 조합)이 훨씬 높은 확률로 나타날 겁니다.

이렇게 우리는 살아오면서 수많은 문장을 접하였고, 우리의 머릿속에는 단어와 단어 사이의 확률이 우리도 모르게 학습되어 있습니다. 덕분에 누군가와 대화를 하다가 한 단어 정도는 정확하게 알아듣지 못하여도, 애매한 경우를 제외하고는, 대화에 지장이 없을 겁니다. (물론 여기에는 문맥 정보를 이용 하는 것도 큰 도움이 됩니다.) 이와 같이 꼭 자연어처리 분야가 아니더라도, 음성인식(Speech Recognition)이나 문자인식(Optical Character Recognition, OCR)에 있어서도 언어모델은 큰 역할을 수행합니다. 우리는 인터넷이나 도서에서 많은 문장들을 (대개 수십만에서 수억 까지) 수집하여 단어와 단어 사이의 출현 빈도를 세어 확률을 계산하고, 언어모델을 구성합니다. 궁극적인 목표는 우리가 일상 생활에서 사용하는 문장의 분포를 정확하게 파악하는데에 있습니다. 또한, 만약 특정한 목표를 가지고 있다면 (예를 들어 의료 분야의 음성인식기 제작) 해당 분야(domain)의 문장의 분포를 파악하기 위해서 해당 분야의 말뭉치(corpus)를 수집하기도 합니다.

## Again, Korean is Hell

우리는 언어들의 구조적 특성에 따라서 여러 갈래로 언어를 분류합니다. 이전([Why Korean NLP is Hell](nlp-with-deeplearning/korean-is-hell.md))에 다루었듯이 한국어는 대표적인 교착어입니다. 그리고 영어는 고립어(+굴절어)의 특성을 띄고, 중국어는 고립어로 분류합니다. 교착어의 특성상, 단어의 의미 또는 역할은 어순에 의해 결정되기 보단, 단어에 부착되는 어미와 같은 접사에 의해 그 역할이 결정됩니다. 따라서 같은 의미의 단어라 할지라도 붙는 접사에 따라 단어의 형태가 달라지게 되어 단어의 수가 늘어나게 됩니다. (버스가, 버스를, 버스에, 버스로 등 같은 버스라도 뒤에 붙는 어미에 따라 다른 단어가 됩니다.) 다시 말해, 단어의 어순이 중요하지 않기 때문에 (또는 생략 가능하기 때문에), 단어와 단어 사이의 확률을 계산하는데 불리하게 작용할 수 있습니다.

|번호|문장|
|-|-|
|1|나는 학교에 갑니다 버스를 타고 .|
|2|나는 버스를 타고 학교에 갑니다 .|
|3|버스를 타고 나는 학교에 갑니다 .|
|4|(나는) 버스를 타고 학교에 갑니다 .|

위의 세문장 모두 같은 의미의 표현이고 사용 된 단어들도 같지만, 어순이 다르기 때문에, 단어와 단어 사이의 확률을 정의하는데 있어서 혼란이 가중됩니다. 같은 의미의 문장을 표현하기 위해서 '타고' 다음에 나타날 수 있는 단어들은 '.', '학교에', '나는' 3개이기 때문에, 확률이 쉽게 말해 퍼지는 현상이 생기게 됩니다. 이에 반해 영어나 기타 라틴어 기반 언어들은 좀 더 어순에 있어서 규칙적이므로 좀 더 유리한 면이 있습니다.

더군다나, 한국어는 교착어의 특성상 어미가 부착되어 단어를 형성하기 때문에 같은 '학교'라는 단어가 '학교+에', '학교+로', '학교+를', '학교+가', ... 등 수많은 단어로 파생되어 만들어질 수 있습니다. 따라서 사실 어미를 따로 분리해주지 않으면 어휘의 수가 기하급수적으로 늘어나게 되어 sparse함이 더욱 높아질 수 있어 더욱 문제 해결이 어려워질 수 있습니다.

## Probability of Sentence

먼저 문장의 확률을 표현 해 보도록 하겠습니다. $w_1$, $w_2$라는 2개의 단어가 한 문장 안에 순서대로 나타났을 때, 이 문장의 확률로 표현하면 다음과 같습니다.

$$
P(w_1, w_2)
$$

우리는 이 수식을 베이즈 정리(Bayes Theorem)에 따라 조건부 확률(Conditional Probability)로 표현할 수 있습니다.

$$
\begin{aligned}
P(w_1, w_2) &= P(w_1)P(w_2|w_1) \\
&\text{because} \\
P(w_2|w_1)&=\frac{P(w_1,w_2)}{P(w_1)}.
\end{aligned}
$$

좀 더 나아가서 Chain Rule을 통해, $W = \{ w_1, w_2, w_3, w_4 \}$, 4개의 단어가 한 문장 안에 있을 때를 표현 해 보면 아래와 같습니다.

$$
P(W)=P(w_1, w_2, w_3, w_4)=P(w_1)P(w_2|w_1)P(w_3|w_1, w_2)P(w_4|w_1, w_2, w_3)
$$

여기서 Chain Rule(연쇄 법칙)이란, 조건부 확률(conditional probability)을 사용하여 결합 확률(joint probability)를 계산 하는 방법으로, 아래와 같이 유도 할 수 있습니다.

$$
\begin{aligned}
P(A,B,C,D)&=P(D|A,B,C)P(A,B,C) \\
&=P(D|A,B,C)P(C|A,B)P(A,B) \\
&=P(D|A,B,C)P(C|A,B)P(B|A)P(A)
\end{aligned}
$$ 

우항을 해석해보면, $w_1$가 나타날 확률과 $w_1$가 주어졌을 때 $w_2$가 나타날 확률, $w_1, w_2$가 주어졌을 때 $w_3$가 주어졌을 확률, $w_1, w_2, w_3$가 주어졌을 때 $w_4$가 나타날 확률을 곱하는 것을 알 수 있습니다. 이로써 우리는 language model을 통해서 문장에 대한 확률 뿐만 아니라, 단어와 단어 사이의 확률도 정의 할 수 있습니다. 우리는 이를 일반화하여 아래와 같이 표현할 수 있습니다.

$$
P(W)=\prod_{i=1}^{n}{P(w_i|w_{<i})}
$$

또는 log확률로 표현하여 곱셈 대신 덧셈으로 표현할 수 있습니다. 참고로, 문장이 길어지게 된다면 당연히 확률에 대한 곱셈이 거듭되면서 확률이 매우 작아지게 되어 정확한 계산 또는 표현이 힘들어지게 됩니다. (또한, 곱셈 연산보다 덧셈 연산이 더 빠릅니다.) 따라서 우리는 log를 취하여 덧셈으로 바꾸어 더 나은 조건을 취할 수 있습니다. 

$$
\log{P(W)}=\sum_{i=1}^{n}{\log{P(w_i|w_{<i})}}
$$

이제 우리는 실제 예제를 가지고 표현 해 보도록 하겠습니다. Corpus $\mathcal{C}=\{W_1,W_2,\cdots\}$에서 $i$번째 문장 $W_i = \{BOS, \text{나는}, \text{학교에}, \text{갑니다}, EOS\}$에 대한 확률을 Chain rule을 통해 표현하면 아래와 같습니다.

$$
P(BOS, \text{나는}, \text{학교에}, \text{갑니다}, EOS) = P(BOS)P(\text{나는}|BOS)P(\text{학교에}|BOS,\text{나는})P(\text{갑니다}|BOS,\text{나는},\text{학교에})P(EOS|BOS,...,\text{갑니다})
$$

여기서 BOS는 Beginning of Sentence라는 의미의 토큰이고, EOS는 End of sentence라는 의미의 토큰 입니다. $P(BOS)$ 의 경우에는 항상 문장의 시작에 오게 되므로 상수가 될 것 입니다. $P(\text{나는}|BOS)$ 경우에는 문장의 첫 단어로 *나는*이 올 확률을 나타내게 됩니다.

이제 문장을 어떻게 확률로 나타내는지 알았으니, 확률을 직접 구하는 방법에 대해서 알아보겠습니다. 우리는 앞 챕터에서 문장을 수집하는 방법에 대해서 논의 했습니다. 수집한 말뭉치 내에서 직접 단어들을 카운트 함으로써 우리가 원하는 확률을 구할 수 있습니다. 예를 들어 아래의 확률은 다음과 같이 구할 수 있습니다.

$$
P(\text{갑니다}|BOS,\text{나는},\text{학교에})=\frac{\text{Count}(BOS,\text{나는},\text{학교에},\text{갑니다})}{\text{Count}(BOS,\text{나는},\text{학교에})}
$$
