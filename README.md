## 카드 뒤집기 게임 강화학습

### 1. 규칙
양의 정수 또는 'X'가 적힌 카드들이 있다. 플레이어는 전체 카드의 구성을 알고 있지만, 카드가 뒤집혀 있어서 각 카드의 내용을 알 수 없다. 플레이어는 무작위로 한 장씩 카드를 뒤집을 수 있다. 양의 정수가 적힌 카드를 뒤집으면 해당 숫자만큼 점수를 얻으며, 플레이어는 원하는 시점에 게임을 중단할 수 있다. 그러나 'X'가 적힌 카드를 뒤집으면 지금까지 얻은 모든 점수를 잃고 게임이 즉시 종료된다. 게임은 플레이어가 'X' 카드를 뒤집거나 스스로 중단을 선언할 때까지 진행되며, 목표는 가능한 많은 점수를 획득하는 것이다.

### 2. 게임 학습의 의의
이 카드 뒤집기 게임은 간단한 구조를 가지고 있음에도 불구하고, 강화학습 관점에서 매우 흥미로운 주제를 제공한다. 이 게임은 위험 회피와 보상 극대화 사이에서 균형을 학습하는 문제를 다루며, AI가 학습을 통해 예측하기 어려운 행동을 보일 가능성을 탐구할 수 있다. 특히, AI의 학습 과정에서 의사결정을 최적화하는 방식을 연구하기에 적합한 사례로 꼽힌다. 금융 의사결정(리스크 관리), 마케팅 전략, 안전 시스템 등에서 최대 이익을 추구하면서도 리스크를 최소화하려는 최적의 중단 시점을 모색하는 데 유용하다. 이를 통해 "최적의 의사결정은 무엇인가?"라는 강화학습의 근본적인 질문을 탐구할 수 있다.

### 3. 카드 상황별 최초 예상 결과
#### 3.1. $n$장의 '1'카드와 1장의 'X' 카드 조합인 상황
가장 먼저 다음 전략을 생각해볼 수 있다.
>고정된 $k$에 대해 처음 k장째까지는 무조건 카드를 계속해서 뒤집는다. 만약 $k$장을 모두 뒤집는 동안 'X' 카드가 한 장도 나오지 않았다면 $k+1$장째에서 무조건 게임을 중단한다.

이 경우 기댓값을 계산해 보면 다음과 같다.

처음 $k$장째를 뒤집는 동안 'X'카드가 한 장도 나오지 않을 확률

$$
\frac{n}{n+1} \times \frac{n-1}{n} \times \frac{n-2}{n-1} \times ... \times \frac{n-k+1}{n-k+2} = \frac{n-k+1}{n+1}
$$

처음 $k$장째를 뒤집는 동안 'X'카드가 한 장도 나오지 않으면 $k$점을 받고, 그 외에는 모두 0점을 받으므로 기댓값은

$$
\frac{n-k+1}{n+1} \times k = \frac{(n+1)k-k^2}{n+1}
$$

이다. 이때

$$
\frac{(n+1)k-k^2}{n+1} = \frac{-~(k- \frac{n+1}{2})^2 ~ + ~ \frac{(n+1)^2}{4}}{n+1}
$$

이므로 $n$이 홀수라면, $k= \frac{n+1}{2}$일 때, 기댓값이 $\frac{(n+1)^2}{4n+4}$으로 최대가 된다. $n$이 짝수라면, 기댓값은 $\frac{n}{2}$일 때와 $\frac{n}{2} + 1$일 때, 각각 기댓값이 $\frac{n^2+2n}{4n+4}$으로 최대이다.

이제 각 라운드에서 카드를 뒤집을지, 아니면 게임을 중단할지를 실시간으로 결정하기 위해, 매 순간 뒤집는 경우와 중단하는 경우의 점수 득실 기댓값을 비교해 보자. 여기서 $i$번째 카드를 뒤집는 상황을 $i$라운드라고 정의한다. 

$i$라운드에서는 이미 $i-1$점을 획득한 상태이다. 이때 게임을 중단하면, $i-1$점을 그대로 유지하며 게임을 종료한다. 반면, 카드를 뒤집는다면 다음과 같은 결과가 발생한다:
- '1' 카드를 뒤집을 경우, 1점을 추가로 얻는다.
- 'X' 카드를 뒤집을 경우, 지금까지 획득한 $i-1$점을 모두 잃는다.

$i$라운드에서 남은 카드의 장수는 $n+2-i$장이므로 $i$라운드에서 카드를 뒤집을 때 얻는 기댓값은 아래와 같이 계산된다.

$$
\frac{n+1-i}{n+2-i} \times 1 + \frac{1}{n+2-i} \times (-i+1) = \frac{n+2-2i}{n+2-i}
$$

따라서 $n+2-2i>0$일 때, 즉 $i<\frac{n+2}{2}$일 때에만 카드를 뒤집는 것이 유리하다. 따라서 최적의 진행 라운드 수는 위의 결과와 동일하게 나온다.

#### 3.2. 카드 조합의 일반화
숫자 카드 조합이 $a_1, a_2, ..., a_m$으로 $m$장 (이때, $a_1 + a_2 + ... + a_m = A$), 'X' 카드가 $n$장이라고 가정하자.
