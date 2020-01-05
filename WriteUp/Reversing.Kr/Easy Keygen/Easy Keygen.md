---
typora-root-url: img
---

# [WriteUp]Reversing.Kr - Easy Keygen

:black_nib:ChyKor12(sjjo0225@gmail.com)

---

OllyDbg로 까 봤는데, "Correct!"와 "Wrong"이 있음. 아마도 Correct를 띄우는 방향으로 가야 하는 듯. 그러려면 프로그램이 gen하는 Serial과 내가 입력한 Serial이 일치해야 함.

처음에 이름에 "ChyKor12"를 입력하고 보니 도저히 감이 안 왔음.

ReadMe.txt를 보면 Serial이 5B134977135E7D13일 때 무슨 이름을 입력해야 Correct가 뜨는가를 물어보는 것 같음.

한 줄씩 디버깅하다가 너무 복잡해서 그냥 이름에 "AAAAAAAAAAAA"를 입력해 봤음. 그 결과.

![input 12 A](https://user-images.githubusercontent.com/57470479/71783006-2ff89e80-3024-11ea-90cc-5ea186226b65.png)

"516171"이 반복되는 Serial이 gen됨.

40109A에서 호출하는 함수는 입력한 이름의 첫 번째 문자의 아스키 코드에 10(hex), 두 번째 문자에 20, 세 번째 문자에 30, 네 번째 문자에 10, ... 이런 식으로 더해서 출력해 주는 함수라는 추측.

그런데 'z'(7A)처럼 10을 더하면 아스키 코드로 표현할 수 없는 경우는 어떻게 되나 궁금해서 "~}|{zyx"를 입력으로 줘 보았음.(7E7D7C7B7A7978) 그 결과.

![when ascii code is big](https://user-images.githubusercontent.com/57470479/71783003-2f600800-3024-11ea-9a7d-a751f266e371.png)

작으면 더하고 크면 빼는 건가?라기엔 주어진 시리얼에 13이 세 개나 있음. 원래대로라면 20이 더해지거나 빼져야 하는 위치인데, 20이 더해져서 13이 되는 건 말이 안 되고, 20이 빼지려면 원래 33('3')이었어야 함. 그래서 그냥 일단 더하거나 빼서 키보드로 입력할 수 있는 문자가 나오는 방향으로 계산해 봤음. 그렇게 해서 나온 입력값은 "K3yg3.m3"

입력해 봤는데 여섯 번째의 2E('.')가 원하는 대로 5E가 되지 않고 1E가 되었음. 이건 왜 10을 빼지 하다가 6E('n')로 바꿔서 넣어 봤는데 이유는 알 수 없지만 성공.

![Correct](https://user-images.githubusercontent.com/57470479/71783005-2ff89e80-3024-11ea-8450-2c83bf5f78ed.png)

그냥 프로그램을 실행하면 Correct가 뜨고 바로 닫혀서 못 보니까 OllyDbg에서 해 보면 된다.

![auth](https://user-images.githubusercontent.com/57470479/71783004-2f600800-3024-11ea-9c45-476ec33cfca7.png)

---

풀고 나서 찾아보니 10, 20, 30을 더하거나 빼는 것이 아니라 xor 연산을 하는 과정이라고 한다. 그래서 2E는 1E가 되고 6E는 5E가 되었던 것.
