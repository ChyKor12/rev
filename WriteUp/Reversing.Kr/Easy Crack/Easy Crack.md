---
typora-root-url: img
---

# [WriteUp]Reversing.Kr - Easy Crack

:black_nib:ChyKor12(sjjo0225@gmail.com)

---

Easy_CrackMe.exe를 실행하면 다음과 같은 창이 뜬다.

![Easy CrackMe](/Easy CrackMe.png)

아무거나 입력하고 확인을 눌러 보자.

![Incorrect Password](/Incorrect Password.png)

"Incorrect Password"라는 메시지가 뜬다. 아마도 이 패스워드를 맞추는 문제인 것 같다.

---

OllyDbg에서 어셈블리 코드 영역에서 오른쪽 마우스 클릭을 하고, Search for → All referenced text strings를 선택해서 "Incorrect Password"라는 문자열이 함수의 인자로 쓰이는 부분이 어디인지 찾아보자.

![Search for referenced strings](/Search for referenced strings.png)

저 줄을 더블클릭해서 해당 주소로 가 보면, 다음과 같은 상태이다.

![Jumps from](/Jumps from.png)

"Incorrect Password"라는 문자열이 사용되는 코드의 두 줄 위를 보면 "Jumps from 004010B5, 004010CD, 0040110B, 00401112"라고 되어 있다. 즉, 현재 커서가 가리키고 있는 00401135라는 주소로 점프하는 코드가 저렇게 네 군데에 있다는 것이다. 그리고 살짝 위로 올려 보면 다음과 같은 코드도 있다.

![Congratulation code](/Congratulation code.png)

점프하는 코드가 있으면 그 앞에는 비교하는 코드, 즉 조건문도 있을 것이다. 입력값을 잘 조정해서 네 개의 조건문들을 모두 통과하면 "Congratulation !!"이라는 메시지를 볼 수 있을 것이다.

---

위에서 확인한 네 개의 주소에 break point를 걸자. 주소를 일일이 찾아갈 필요 없이 00401135에 커서가 위치한 상태에서 오른쪽 마우스 클릭을 하고 Go to → jnz from ~~ 를 선택하면 된다. Ctrl+F2와 F9을 차례로 눌러서 프로세스를 시작하고, 입력 창에 아무거나 입력하고 확인을 누르면 첫 번째 break point에서 멈출 것이다.

![](/firstBP.png)

조건문을 보면 ESP+5에 저장되어 있는 1바이트와 61(hex)을 비교하여 같지 않으면 점프한다. 현재는 입력으로 abcdefgh를 준 상태라서 ESP+5에는 'b'가 들어가 있다. 입력 값의 두 번째 문자는 'a'여야 함을 알 수 있다.

첫 번째 break point를 지우고, 프로세스를 다시 시작해서 이번에는 두 번째 문자가 'b'가 되도록 입력을 주자. 그러면 두 번째 break point에서 멈출 것이다.

![](/secondBP.png)

커서가 위치한 004010C3에서 호출하는 함수의 반환값이 0이 아니면 점프한다. break point를 함수가 호출되는 부분에 걸고 다시 실행시켜보자.

![](/secondBPfunction.png)

입력값에서 앞의 2바이트를 뺀 나머지와 "5y"라는 문자열을 비교하는 함수인 것 같다. 입력값의 3번째, 4번째 문자는 각각 '5'와 'y'여야 함을 알 수 있다.

이전과 같은 방식으로 세 번째 break point에 도달해 보자.

![](/thirdBP.png)

004010D1부터 시작인 것 같다. 코드가 긴데, 대충 보면 "R3versing"이라는 문자열과, 입력값의 4번째 문자까지를 제외한 문자열을 스택에 푸시해서 둘을 한 글자씩 비교하여 다르면 점프한다. 그러면 입력값의 5번째 바이트부터가 "R3versing"이어야 함을 알 수 있다.

네 번째 break point도 같이 보면, ESP+4에 저장된 1바이트와 45('E')를 비교하여 다르면 점프한다. ESP+4에는 입력값의 첫 번째 문자가 위치한다. 즉, 입력값의 첫 번째 문자는 'E'여야 한다.

결과적으로 패스워드는 'Ea5yR3versing'이 된다.

---

![](/Congratulation.png)

![](/Auth.png)