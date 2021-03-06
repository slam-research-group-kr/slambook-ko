![carrot](/resources/Cover.png)

---

# Preface

이 문서는 중국어 원서인 “입문 Visual SLAM 이론에서 연습까지 14 강(视觉SLAM十四讲 从理论到实践)” 책의  원저자로부터 한글 번역 허가를 받고 구글 번역기를 이용하여 작성된 문서입니다. 본 문서는 아래의 Contribution을 특징으로 합니다.

- 중국어 전공 서적을 구글 번역기를 이용해 한글로 초벌 번역했습니다.
- 초벌 번역 후 매끄럽지 않은 문장은 문맥에 맞게 수정되었습니다. 
- 문서 내용 중 참고할만한 웹문서를 코멘트로 추가했습니다.  
- SLAM 연구에서 주로 사용되는 용어는 한글로 번역된 용어보다 주로 사용되는 영어로 된 용어 그대로 표시하였습니다.

그럼에도 불구하고 부정확하거나 매끄럽지 않은 부분이 있을수 있습니다. 그런 부분은 코멘트로 제안해주시면 반영하도록 노력하겠습니다. 또한 읽으시다가 잘 이해가 가지 않는 부분도 코멘트로 질문해주시면 답변해드리도록 하겠습니다.

번역 참가자: 
- 신동원 (광주과학기술원 박사과정)
- 김선호 (VIRNECT 선임연구원)
- 조원재 (일본국립농업기술혁신공학센터 연구원)
- 장형기 (Imperial College London 석사)
- 박준영 (광주과학기술원 석사과정)

2018년 10월 1일
신동원 드림

---
# 제1장. 선행지식

# 1.1 이 책의 내용

이 책은 시각적 SLAM을 소개하는 책이며, 시각적 SLAM을 주제로 한 최초의 중국어 서적 일 것입니다.  
그렇다면 SLAM은 무엇일까요?  
SLAM은 Simultaneous Localization and Mapping의 약자로 "동시 배치 및지도 제작"[1]로 번역됩니다. 이는 주변 환경에 대한 사전 정보가 없는 상황에서 특정한 센서를 이용해, 이동 중에 환경의 모델을 확립하고 자신의 움직임을 추정한다 [2]. 여기 센서가 주로 카메라 인 경우 이를 "Visual SLAM"이라고합니다.  
이 책의 주제는 Visual SLAM입니다. 여기에서는 의도적으로 많은 정의를 한 문장에 넣었으며 독자가 더 명확한 개념을 갖기를 바랍니다. 우선, SLAM의 목적은 "localization"과 "mapping"의 두 가지 문제를 해결하는 것입니다. 다시 말하면, 센서 자체의 위치를 추정하는 동안 주변 환경의 모델을 수립하는 것이 필요합니다. 어떻게 해결할 수 있을까요? 센서 정보를 사용해야합니다. 센서는 특정 형태로 외부 세계를 관측 할 수 있지만, 다른 센서를 보는 방식은 다릅니다. 이 문제에 관한 책을 쓰고 싶은 이유는 매우 어렵기 때문입니다. 특히 사전 지식없이 SLAM을 실시간으로 수행하려는 경우에 그렇습니다. 카메라를 센서로 사용할 때, 우리가 해야 할 일은 연속 동작의 이미지를 기반으로 카메라와 주변 환경을 추출하는 것입니다 (비디오를 형성 함).  
이것은 매우 직관적 인 질문 인 것 같습니다.  사람이 어떤 처음보는 곳에 들어갔을때 일반적으로 사람이 하는 일과 비슷하지 않을까요?  
초기의 컴퓨터 비전 연구에서 사람들은 언젠가 컴퓨터가 인간처럼 보고 눈으로 세상을 관찰하고 주변의 물건을 이해하며 미지의 세계를 탐험 할 것이라고 상상했습니다. 이것은 멋지고 낭만적인 꿈입니다. 그것은 밤낮으로 그것을 위해 싸우는 무수한 연구자들을 끌어 모았습니다[3]. 우리는 이것이 어렵지 않다고 생각하곤 했지만, 그 진전은 기대했던 것만큼 부드럽지 않았습니다. 우리의 눈에 보이는 꽃과 나무, 곤충, 새와 짐승은 컴퓨터에서 처리하는 방식이 매우 다릅니다. 컴퓨터가 이미지의 내용을 이해하도록하는 것은 우리가 숫자를 이해하게 하는 것만 큼 어렵습니다.  우리는 이미지를 이해하는 방법이나 컴퓨터가 세상을 이해하고 탐구하는 방법을 알지 못합니다. 그래서 우리는 오랫동안 혼란스러워 했습니다. 수십 년 후, 인공 지능과 기계 학습 기술을 통해 컴퓨터는 점차 객체, 얼굴, 소리 등을 식별 할 수 있었습니다. 텍스트 하나는 우리가 사용하는 방식 (확률 모델링)과 매우 다릅니다. 다른 한편, SLAM 개발이 거의 30 년이 지나고 카메라는 서서히 위치를 인식하고 움직였습니다.하지만 이 방식은 여전히 우리와 매우 다릅니다. 그러나 적어도 연구원은 다양한 실시간 SLAM 시스템을 성공적으로 구축했으며 일부는 신속하게 위치를 추적 할 수 있으며 일부는 실시간 3D 재구성을 수행 할 수도 있습니다.  
이것은 정말로 어렵지만 우리는 큰 진전을 이루었습니다. 더 흥미 진진한 점은 최근 몇 년 사이에 과학 기술 발전에 따라 SLAM과 관련된 수많은 응용 지점이 등장했습니다. 많은 곳에서 우리는 우리가 어디에 위치해 있는지 알고 싶습니다. 실내 청소 로봇과 모바일 로봇을 배치해야하고 현장의 자율 차량을 배치해야하며 공중 무인 항공기를 배치해야하며 가상 현실과 증강 현실 장치를 배치해야합니다. SLAM은 매우 중요합니다. 그것 없이는 청소 로봇이 방에서 자율적으로 움직일 수 없으며, 맹목적으로 돌아 다니는 것만으로 홈 로봇은 지침에 따라 특정 방에 정확하게 도달 할 수 없으며 가상 현실도 좌석에 영원히 고정 될 것입니다. 실생활에서 나타날 수 없다는 것은 불행한 일입니다.  
오늘날의 연구자 및 응용 프로그램 개발자는 SLAM 기술의 중요성을 인식하고 있습니다. 국제적으로 SLAM은 거의 30 년의 연구 역사를 지니고 있으며 로봇 및 컴퓨터 비전 분야의 연구 핫스팟입니다. 21 세기 이래로 시각 센서를 중심으로 한 Visual SLAM 기술은 이론과 실습 모두에서 큰 변화와 혁신을 겪었으며 실험실 연구에서 시장 응용으로 점차 옮겨 가고 있습니다. 동시에 중국에서 최소한 SLAM과 관련된 서적은 여전히 부족하고 SLAM 기술에 관심이 있는 많은 초보자는 갈 길이 없다는 사실을 알고 유감스럽게 생각합니다. SLAM의 이론적인 틀은 기본적으로 안정적이지만 프로그래밍 구현은 여전히 복잡하고 기술적 인 한계점이 높습니다. 단계별로 SLAM 분야의 연구원은 많은 지식을 배우는 데 많은 시간을 할애해야 했으며, 종종 많은 우회로를 통과하여 SLAM 기술의 핵심에 다가섰습니다.  
1986 년에 소개 된 이래로 SLAM은 로봇 분야에서 뜨거운 이슈로 떠올랐다. 수천 개의 문헌이 있으며, SLAM 개발의 역사에서 모든 알고리즘과 변종을 완벽하게 기술하는 것은 매우 어렵고 불필요합니다. 이 책은 투영 기하학, 컴퓨터 시각, 상태 추정 이론, Lie 군, Lie 대수학 등과 같은 SLAM에 관련된 배경 지식을 소개하고 이러한 배경 지식보다 SLAM의 중추를 제공하고 불필요한 부분은 생략합니다. 우리는 이 접근법이 효과적이라고 믿습니다. 독자가 주요 흐름의 본질을 마스터 할 수 있다면 자연스럽고 세밀하고 복잡한 최첨단 지식을 탐색 할 수 있는 능력이 자연스럽게 있습니다. 따라서 우리의 목표는 SLAM 초심자가 이 책을 읽음으로써 이 분야의 최첨단을 탐구 할 수 있는 연구자로 빠르게 성장할 수 있도록 하는 것입니다. 반면에 SLAM 분야의 연구원 일지라도 이 책에는 여전히 이상한 점이 있어 새로운 통찰력을 줄 수 있습니다.  
현재 SLAM 관련 서적은 주로 Probabilistic robotics [5], Multiple View Geometry in Computer Vision [3], State Estimation for Robotics: A Matrix-Lie-Group Approach [6]이 있습니다. 이 책은 컨텐츠가 풍부하고 표현력이 풍부하며 파생 된 부분이 엄격하며 SLAM 연구원을위한 고전 교과서입니다. 그러나 당분간 두 가지 중요한 쟁점이 있습니다. 첫째,이 책의 목적은 기본 이론을 소개하는 것이며 SLAM은 그 응용 프로그램 중 하나 일뿐입니다. 따라서 이 책들은 SLAM 전용 책이 아닙니다. 둘째, 그들의 내용은 수학 이론에 편향되어 있으며 기본적으로 프로그래밍 구현과 관련이 없기 때문에 독자는 종종 "책은 이해할 수 있지만 프로그래밍은 아닙니다." 우리는 독자 만이 알고리즘을 직접 구현하고 다양한 매개 변수를 디버그하여 문제 자체를 진정으로 이해할 수 있다고 믿습니다.
역사, 이론, 알고리즘 및 SLAM의 상태를 언급하고 완전한 SLAM 시스템을 Odometry, backend optimization, 매핑 및 루프백 탐지와 같은 여러 모듈로 나눕니다. 우리는 독자들과 함께이 모듈의 핵심 부분을 조금씩 구현하고, 어떤 상황에서 효과적인지, 무언가가 잘못되었을 때를 탐색하고, 모든 사람들이 자신의 컴퓨터에서 코드를 실행하도록 유도 할 것입니다. 필요한 수학적 이론과 많은 프로그래밍 지식에 노출 될 것이며 Eigen, OpenCV, PCL, g2o, Ceres 및 기타 라이브러리를 사용하여 Linux 운영 체제에서의 사용법을 익힐 것입니다.  
글쓰기 스타일에서 우리는 이것을 지루한 이론적 책으로 쓰고 싶지 않습니다. 기술 서적은 엄격하고 신뢰할 만하지만 엄격함은 고정 관념을 의미하지 않습니다. 뛰어난 기술 서적이 생생하고 재미 있고 이해하기 쉬워야 합니다. "이 저자는 너무 비합리적인 사람"이라고 생각한다면, 나는 매우 진지한 사람이 아니기 때문에 저를 용서해주십시오. 어쨌든, 한가지 확실합니다. 당신이 이 새로운 기술에 관심이 있는 한, 당신은 이 책을 공부하고 있습니다. 과정은 확실히 보상받을 것입니다! SLAM과 관련된 이론 지식을 습득하게 되고 프로그래밍 기술도 크게 향상 될 것입니다. 많은 경우, 당신은 내가 당신과 함께 연구하고 있다는 느낌을 갖게 될 것입니다. 그것은 제가 바라는 바입니다. 이 과정에서 연구의 즐거움을 발견하고 "약간의 노력을 통해 원활하게 운영되는 것을 보는"느낌을 좋아하기를 바랍니다.  
좋아, 나는 할말이 많지 않다. 나는 너에게 행복한 여행을 기원한다!  

## 1.2 이 책을 사용하는 방법

### 1.2.1 조직 방법

이 책은 "입문 Visual SLAM 14강"이라고 합니다. 이름에서 알 수 있듯이 수업에서처럼 책의 기본 단위로 구어체를 사용합니다. 각 강의는 고정 된 테마에 해당하며 "이론적 인 부분"과 "실용적인 부분"이 섞여 있습니다. 보통 이론적 인 부분은 앞에 있고 실용적인 부분은 뒤에 있습니다. 이론적인 부분에서는 수학 교과서와 같은 "특정 추론의 정의"방법을 사용하는 대신 알고리즘을 이해하는 데 필요한 수학 지식과 대부분의 시간을 서술 방식으로 소개합니다.이 독서 방식 때로는 덜 엄격 해 보이지만 더 쉽습니다. 연습 부분은 주로 프로그램 구현과 프로그램의 각 부분의 의미와 실험 결과에 대해 논의합니다. 제목에 "연습"이라는 단어가 있는 장을 보게 되면 컴퓨터를 켜고 (코드) 행복하게 코드를 작성해야 합니다.  
문제를 해결하는 것과 관련된 수학 지식을 책에 넣고 가능한 한 얕게 유지하려고 합니다. 우리는 공학을 전공하고 있지만 경험적으로 충분하다면 수학에서 수학을 완전히해야 한다는 것을 인정해야합니다. 이 알고리즘이 작동하고 수학자가 어떤 상황에서 우리가 일하지 않을지를 말해 준다면 우리는 만족스럽지 만 완벽하지만 실제로 복잡하고 긴 증명을 보유하지 않아야 합니다 (물론 그들은 자신의 가치를 지닙니다) . SLAM은 수학적 배경이 너무 많기 때문에 책이 수학 교과서가되는 것을 막기 위해 연습 및 보충 자료로 세부 공제 및 증명을 남기므로 관심있는 독자는 참고 문헌을 읽고 더 깊이 이해할 수 있습니다.   
각 텍스트를 작성한 후 연습문제를 만들었습니다. 그러한 것들 중에서, *를 가진 문제는 어렵습니다. 독자가 연습문제를을 한 번 읽어보는 것이 좋습니다. 이 방법은 이 지식을 습득하는 데 매우 도움이 됩니다.  
이 책의 내용은 크게 두 부분으로 나뉩니다.  

(1) 첫 번째 부분은 수학의 기초입니다. 다음과 같이 시각적 SLAM과 관련된 수학을 쉽게 이해할 수 있습니다.  
- 제 1 강은 이 서적의 기본 정보를 소개하는 서문으로, 문제는 주로 자가 진단 질문을 포함합니다.
- 제 2 강은 SLAM 시스템의 개요이며, SLAM 시스템으로 구성되는 모듈과 각 모듈의 특정 작업이 무엇인지 설명합니다. 실용적인 부분은 프로그래밍 환경의 구축 과정과 IDE의 사용을 소개합니다.
- 제 3강에서는 3차원 모션을 소개하고 회전 행렬, 쿼터니언, 오일러 각에 대한 지식을 접하고 Eigen에서 사용합니다.
- 제 4 강는 Lie 군과 Lie 대수입니다. 비록 당신이 지금 리 대수학을 이해하지 못한다고해도 그것은 중요하지 않습니다. 리 대수학의 정의와 사용법을 배우고 Sophus를 통해 조작합니다.
- 제 5 강에서는 핀홀 카메라 모델과 컴퓨터에서의 이미지 표현을 소개합니다. OpenCV를 사용하여 카메라의 내부 및 외부 매개 변수를 검색합니다.
- 제 6 강에서는 이론적 인 상태 추정, 최소 제곱 문제 및 그래디언트 디센트 방법을 비롯한 비선형 최적화를 소개합니다. 커브 피팅에 Ceres 및 g2o를 사용하는 실험을 완료합니다.
이것들은 모두 우리가 사용해야하는 수학적 지식입니다. 물론, 이전에 배웠던 수학과 선형 대수학을 의미합니다. 우리는 그들이 어려 보이지 않을 것이라고 보장합니다. 물론 탐구하고 싶다면 읽을 거리가 되는 참고 자료를 제공 할 것이고 그 자료는 텍스트의 지식보다 어려울 것입니다.

(2) 두 번째 부분은 SLAM 기술입니다. 첫 번째 부분에서 설명한 이론을 사용하여 시각적 SLAM의 각 모듈이 어떻게 작동하는지 설명합니다.  
- 강의 7은 특징점 방법의 시각적 주행 거리계입니다. 이 강좌에서는 특징점의 추출 및 매칭, 에피폴라 기하학 제약 조건의 계산, PnP 및 ICP를 비롯한 많은 내용이 있습니다. 실제로 이러한 방법을 사용하여 두 이미지 간의 모션을 추정합니다.
- 제 8 강은 직접적인 시각적 주행 거리계입니다. 옵티컬 플로우 및 직접 법칙을 익히고 g2o를 사용하여 간단한 RGB-D 직접 방법을 구현합니다.
- 제 9 강은 시각적 주행 거리계의 실용적인 장으로서, 기본 기능을 달성하기 위해 이전에 습득 한 지식을 결합한 Odometry 프레임 워크를 구축하게 됩니다. 이 과정에서 최적화 필요성, 키 프레임 선택 등의 문제가 발생합니다.
- 제 10 강은 backend optimization이며 주로 기본 Bundle Adjustment를 비롯하여 번들 조정에 대한 심도있는 토론과 솔루션 프로세스 속도를 높이기 위해 희소성을 사용하는 방법에 대해 설명합니다. Ceres와 g2o로 BA 프로그램을 작성할 것입니다.
- 제 11 강은 backend optimization에서 포즈에 중점을 둡니다. 포즈 맵은 키 프레임 사이에 제약 조건을 표현하는보다 컴팩트 한 형식입니다. 포즈를 최적화하려면 g2o와 gtsam을 사용하십시오.
- 제 12 강은 loop closure detection로 주로 bag-of-word 메소드를 기반으로 루프백 탐지를 소개합니다. dbow3을 사용하여 사전 교육 프로그램과 루프백 탐지 프로그램을 작성합니다.
- 제 13강은 지도 제작을위한 것입니다. 우리는 단안을 사용하여 조밀 한 깊이 맵을 추정하는 방법 (그리고 얼마나 신뢰할 수 없는지)을 논의한 다음 RGB-D 조밀도 맵 생성 프로세스에 대해 논의 할 것입니다. 에피폴라 라인 검색과 블록 매칭을위한 프로그램을 작성한 다음 RGB-D로 포인트 클라우드 맵과 옥트리 트리 맵을 생성합니다.
- 제 14 강은 현재 오픈 소스 SLAM 프로젝트와 그 미래 방향에 초점을 두고 있습니다. 나는 이전 지식을 읽은 후에, 당신은 그들의 원리를 보다 쉽게 이해하고 새로운 아이디어를 실현할 것이라고 믿습니다.
마지막으로, 당신이 무슨 말을 하는지 이해하지 못한다면, 축하합니다! 이 책은 당신을 위한 책입니다! 어서!

### 1.2.2 코드
이 책의 모든 소스 코드는 GitHub에서 호스팅됩니다.  
https://github.com/gaoxiang12/slambook  
독자는 언제든지 볼 수 있도록 다운로드 할 것을 강력히 권장합니다. 코드는 챕터로 나누어집니다. 예를 들어, 일곱 번째 강좌의 내용은 ch7 폴더에 배치됩니다. 또한 책에 사용 된 일부 작은 라이브러리는 3rdparty 폴더에 압축 패키지로 저장됩니다. OpenCV와 같은 대형 및 중형 라이브러리의 경우 처음 설치 될 때 설치 방법을 소개합니다. 코드에 대한 질문이 있으면 GitHub의 Issues 버튼을 클릭하여 질문을 제출하십시오. 코드에 문제가 있는 경우 시간이 지나면 변경 될 것이므로 귀하의 이해가 편향된 경우에도 최대한 회신 할 것입니다. 힘내 사용에 익숙하지 않다면 오른쪽에 다운로드라는 단어가 포함 된 버튼을 클릭하여 코드를 로컬에 다운로드하십시오.  

### 1.2.3 예상되는 독자
이 책은 SLAM에 관심이 있는 학생과 연구원을 대상으로 합니다. 책을 읽으려면 특정 토대가 필요하며 다음 지식이 있다고 가정합니다.  
- 고급 수학, 선형 대수학, 확률 이론 이것들은 대부분의 독자들이 학부 수준에서 접촉해야하는 기본적인 수학 지식입니다. 행렬 및 벡터가 무엇인지, 또는 차별화 및 통합을 의미하는지 이해해야 합니다. SLAM에 사용 된 전문 지식을 위해 추가로 소개 할 것입니다.
- C++ 언어 기초. 개발 언어로 C++을 사용하기 때문에 적어도 언어 구문에 익숙해야합니다. 예를 들어 클래스가 무엇인지, C ++ 표준 라이브러리를 사용하는 방법, 템플릿 클래스를 사용하는 방법 등을 알아야합니다. 우리는 너무 많은 기술을 사용하는 것을 피할 것이지만, 어떤 장소는 정말로 피할 수 없는 것입니다. 또한 C ++ 11 표준 컨텐트 중 일부를 사용하지만 사용 된 위치에 대해 설명하겠습니다.
- 리눅스 기반. 우리의 개발 환경은 Windows가 아닌 Linux이며 Linux에서만 소스 프로그램을 제공하며 Windows에서 개발 방법을 소개하지는 않습니다. 우리는 SLAM 연구자들에게 리눅스를 마스터하는 것이 필수적이라고 생각합니다. 초보자들에게는 왜 그런지 묻지 마십시오. 이 책에 대한 지식을 배우고 나면, 당신도 우리와 같은 생각을 가질 것이라고 믿습니다. 리눅스에서의 다양한 라이브러리 설정은 매우 편리하며,이 과정에서 리눅스의 편리함을 깨닫게 될 것입니다. 독자가 전에 리눅스를 사용 해본 적이 없다면 리눅스 교과서를 찾아 조금 배우는 것이 가장 좋습니다 (기본을 배우십시오, 일반적으로 관련 서적의 처음 몇 장). 우리는 독자가 훌륭한 리눅스 운영 기술을 요구하지는 않지만, 독자들이 적어도 "터미널을 열고 코드 디렉토리를 입력하는 방법"을 알기를 바랍니다. 이 강의에는 Linux 지식 자체 테스트 문제가 몇 가지 있습니다. 자체 테스트 질문에 대한 답을 알고 있으면 이 책의 코드를 읽는 데 아무런 문제가 없습니다.

SLAM에 관심이 있지만 위의 지식이 없는 독자는 이 책을 읽지 못할 수도 있습니다. C ++의 기초를 이해하지 못한다면 C ++ Primer Plus와 같은 책을 읽을 수 있습니다. 관련 수학이 없으면 관련된 수학 교과서도 읽을 수 있지만 큰 문제라고 생각합니다. 대부분의 학부 수준의 친구들은 책을 읽는 데 필요한 수학적 배경을 분명히 사용할 수 있습니다. 코드 측면에서는 개인적으로 잃어 버리는 데 약간의 시간이 걸린 다음 내부적으로 매개 변수를 조정하여 효과가 어떻게 변하는 지 확인할 수 있습니다. 이것은 학습에 매우 도움이 될 것입니다.  
이 책은 SLAM 관련 강좌의 교과서 또는 과외 자습 교재로 사용할 수 있습니다.

## 1.3 스타일 관례
이 책에는 수학 이론과 프로그래밍 구현이 모두 포함되어 있으므로 독서의 편의를 위해 다양한 유형의 내용이 서로 다른 조판 방법으로 구분됩니다.  
(1) 수식이 별도로 나열되며 중요한 수식도 오른쪽에 번호가 매겨져 있습니다.  
예 :
![carrot](/resources/ch1/eq(1.1).png)
스칼라는 기울임 꼴 문자 (예 : a)를 사용하고, 벡터 및 행렬은 굵은 기울임 꼴 (예 : a, A)을 사용합니다. 중간이 비어있는 굵은 글자는 실수 집합 , 정수 집합 와 같은 특수 세트를 나타냅니다. 리 대수의 일부는 와 같은 고딕을 사용합니다.  
(2) 프로그램 코드는 왼쪽에 줄 번호가 있는 더 작은 글꼴 크기를 사용하여 박스 처리됩니다. 프로그램이 길면 상자가 다음 페이지로 계속됩니다.  
![carrot](/resources/ch1/code1.png)
(3) 코드 수가 많거나 위에서 열거 한 사본 중 일부가 책에 포함하기에 적합하지 않은 경우 중요한 세그먼트 만 제공하고 '조각'이라는 단어로 표시합니다. 따라서 독자들이 GitHub의 모든 소스 코드를 다운로드하고 책을 더 잘 이해할 수 있는 연습을 완료 할 것을 강력히 권장합니다.  
(4) 조판으로 인해 책에 표시된 코드가 GitHub의 코드와 약간 다를 수 있습니다 .GitHub의 코드를 참조하십시오.  
(5) 우리가 사용하는 각 라이브러리는 처음 나타날 때 더 자세한 설명을 가지지 만 이후 사용에서는 설명하지 않습니다. 따라서 독자는 책의 내용을 장 순서로 읽는 것이 좋습니다.  
(5) 이 강좌의 요약은 각 강의 시작 부분에 나열되며 마지막에는 요약 및 연습 문제가 있습니다. 인용 된 참고 문헌은 이 책의 끝에 나와 있습니다.   
(6) 별표로 시작하는 장은 선택적인 부분이며, 독자는 자신의 관심사에 따라 읽을 수 있습니다. 그 (것)들을 건너 뛰는 것은 연속적인 장의 이해에 영향을 미치지 않을 것이다.  
(7) 본문의 중요한 내용은 굵은 글씨로 표시되어 있으며, 익숙하다고 생각합니다.  
(8) 우리가 설계 한 대부분의 실험은 데모 성격의 것이 었습니다. 이들을 이해한다고해서 전체 라이브러리 사용에 대해 이미 잘 알고있는 것은 아닙니다. 따라서 이 책에서 자주 사용되는 라이브러리에 대해 자세히 알아 보려면 수업 외 시간을 조금 더 사용하는 것이 좋습니다.  
(10)이 책에서 실습 선택적 판독 값을 사용하려면 추가 자료를 직접 검색해야 할 수 있으므로 검색 엔진 사용법을 터득해야 합니다.  

## 1.4 감사 인사와 진술
이 책의 긴 글쓰기 과정에서 다음과 같은 많은 도움을 받았습니다.  
중국 과학 아카데미 박사가 강의 5의 카메라 모델 부분에 대한 자료를 제공했습니다.  
Yan Wei는 일곱 번째 강연의 공식 유도 자료를 제공했습니다.  
Huazhong 과학 기술 대학의 Liu Yi 박사는 이 책의 여섯 번째 및 열 번째 강의 자료를 제공했습니다.  
수많은 교사와 급우가 책에 개정을 제공했다 : Xiao Xiqiu, Xie Xiaojia, Yan Xin, Li Shuaijie, Liu Fuqiang, Yuan Meng, Sun Zhiming, Chen Yusheng, Wang Jing, Zhu Yichen, Ding Wendong, Fan Dijun, Heng Xianfan, Gao Yang, Li Shaopeng 우 보, 연 Xuejiao, 장 Teng, 청 팬, 루 Meiqi, 양 난, 등등. 나는 그들에게 감사하고 싶다.  

또한 강사님의 성원과 도움을 주신 강사님 교수님 께 감사드립니다. Electronic Industry Press Zheng Liujie의 편집자에게 감사드립니다. 그들의 도움이 없으면,이 책은 현재의 얼굴을 가진 독자에게 올 수 없다. 이 책의 책과 출판은 모든 사람들의 공동 노력의 결과입니다. 필자가 저자 목록에 그것들을 모두 열거 할 수는 없지만 출판은 그들의 작업과 불가분의 관계입니다.  
많은 양의 문헌과 논문이 이 책의 글쓰기 과정에서 언급됩니다. 수학 이론 지식의 대부분은 이전 연구 결과이며 내 독창성은 아닙니다. 실험 디자인의 일부는 다양한 오픈 소스 코드의 데모에서도 나온다.하지만 대부분은 나 자신이 쓴 것이다. 또한, 일부 사진은 텍스트에 표시된 출판 된 저널이나 회의 논문에서 가져온 것입니다. 불특정 이미지는 원래 또는 웹에서 나열되지 않습니다. 궁금한 점이 있으면 연락해 주시면되도록 빨리 수정하겠습니다.  
이 책에는 많은 지식이 포함되어 있으며 실수 나 생략이 불가피합니다. 질문이 있으시면 언제든지 이메일을 통해 저에게 연락하십시오. 내 이메일 주소는 gaoxiangl2@mails.tsinghua.edu.cn 입니다. 연인의 덕분에 그녀는 장기간에 걸친 이해와 지원을 받았습니다. 이 책은 그녀에게 헌정 된 책입니다.  
