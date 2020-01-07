---
typora-root-url: img
---

# [WriteUp]Reversing.Kr - Easy Unpack

:black_nib:ChyKor12(sjjo0225@gmail.com)

---

프로그램 이름에서부터 Unpack Me라고 시키고 있는 것을 보니 패킹된 프로그램인 것 같다.

> 패킹(Packing)
>
> 패킹(실행 압축)은 일반적으로 통용되는 압축(일반 압축)과는 살짝 다른 의미를 가진다. 일반적으로 압축 파일이라 하면 가장 먼저 떠오르는 것이 .zip 파일이다. 우리는 .zip 파일을 알집 등의 프로그램으로 압축을 풀어서 그 안에 있는 파일을 사용한다. 하지만 패킹된 파일은 그냥 실행하면 프로그램 내부의 패킹 해제 루틴이 있기 때문에 문제없이 실행된다.

ReadMe.txt에는 "Find the OEP"라고 써 있다.

> :question: OEP(Original Entry Point)
>
> 패킹된 파일은 프로그램의 시작 주소, 즉 Entry Point가 패킹되기 전과 달라진다. OEP는 패킹되기 전의 Entry Point,  즉 실제 시작 주소를 뜻한다.

즉, 이 프로그램을 디버깅하면 실제와 다른 Entry Point가 잡힐 것인데, 어찌어찌 잘 해서 실제 Entry Point를 찾아내라는 말이다.

---

![Entry Point Alert](/Entry Point Alert.png)

OllyDbg로 파일을 열면 이런 경고 메시지가 뜬다. entry point가 코드의 바깥쪽에 있음을 감지하고, 코드가 변조되었음을 경고하는 메시지이다. 확인을 누르자.

![Entry Point](/Entry Point.png)

Entry Point로 표시되어 있는 부분이다. 스크롤을 내리다 보면 다음과 같은 코드가 나온다.

![jmp 401150](/jmp 401150.png)

401150으로 점프하는 코드가 있고 그 뒤는 모두 NULL로 채워져 있다. 오른쪽 마우스 클릭 - Go to - Expression에서 401150을 입력해서 코드를 보면 다음과 같다.

![401150](/401150.png)

실행은 되어야 하는데 어셈블리 코드의 형태가 아니다.

40A1FB에 break point를 걸어 보자.

![Suspicious breakpoint](/Suspicious breakpoint.png)

code section의 바깥쪽에 break point를 걸려고 한다는 경고 메시지가 뜬다. 예를 누르자.

실행시키고, break point에서 멈추면 F8을 눌러서 401150으로 점프시키자. 그리고 나서 오른쪽 마우스 클릭 - Analysis - Analyse code(Ctrl+a)를 클릭해 보자.

![Decode code](/Decode code.png)

실행 가능한 정상적인 코드로 바뀌었음을 확인할 수 있다. 실제 Entry Point는 401150이 된다.![auth](/auth.png)

---

> https://m.blog.naver.com/PostView.nhn?blogId=wsi5555&logNo=221261191211&proxyReferer=https%3A%2F%2Fwww.google.com%2F - 관련 내용(UPX 패킹된 파일에서 OEP 확인)