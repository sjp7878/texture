---
layout: post
title:  "맥에 이클립스, 톰캣, 플렉스, SVN 까지..."
description: 맥에 이클립스, 톰캣, 플렉스, SVN 까지...
date:   2012-12-11 08:16:44 +0530
categories: Mac OS
---

이클립스를 먼저 다운받는다.

<p style="color:red;">플렉스를 이클립스 플러그인으로 넣을건데 중요한게 Juno는 안된다.(이클립스 3.5 이상에서 된다고 나와있으니 Juno는 Indigo보다 더 높으므로 되야하는데 안된다. 플렉스 버그)</p>

따라서 Indigo를 다운 받는다.
![스크린샷 2020-02-01 오후 9 13 56](https://user-images.githubusercontent.com/12682121/73591906-c773e300-4537-11ea-8ff0-b79df9c86606.png)

보면 저기에서 오른쪽 Related Links에서 Eclipse Indigo를 클릭한다.
![스크린샷 2020-02-01 오후 9 14 38](https://user-images.githubusercontent.com/12682121/73591914-df4b6700-4537-11ea-80d1-e750e23c6de5.png)

<p style="color:red;">저기서 역시 중요한게 자기 맥이 64비트를 지원하더라도 32비트를 다운 받아야한다.</p> 이클립스 플러그인 깔때 64

비트면 안깔아진다.

따라서 Mac Cocoa 32-bit를 다운받자.

다운후 적당한곳에 압축을 풀어준다.

다음으로 톰캣을 다운받는다.(<p style="color:red;">참고로 7.0은 java가 1.6이상이 깔려있어야한다.</p>)
![스크린샷 2020-02-01 오후 9 16 32](https://user-images.githubusercontent.com/12682121/73591941-233e6c00-4538-11ea-8f48-954e45fa3f46.png)
저기서 Core의 tar.gz 파일을 다운.

다운후 적당한곳에 압축을 풀어준다.
![스크린샷 2020-02-01 오후 9 17 25](https://user-images.githubusercontent.com/12682121/73591953-436e2b00-4538-11ea-83f1-c8f5d05a8b03.png)

이름이 길어서 Tomcat7으로 바꾸었다.

다음으로 플렉스를 설치 해야한다.
![스크린샷 2020-02-01 오후 9 18 06](https://user-images.githubusercontent.com/12682121/73591964-5bde4580-4538-11ea-8b09-00d2a99b16db.png)
자동으로 맥으로 인식되어 다운받을 수 있게되있다.

다운받기전에 어도비 아이디와 패스워드를 물어본다.(없으면 하나 만든다)

다운후 dmg 파일을 클릭하면 창이하나 뜨는데 Adobe Flash Builder 4.7을 선택하면 폴더가 열린다.
![스크린샷 2020-02-01 오후 9 18 47](https://user-images.githubusercontent.com/12682121/73591979-744e6000-4538-11ea-825d-5d212520837c.png)

저기서 Install 을 클릭하여 플렉스를 설치한다.

이클립스 플러그인으로 설치하기 위해서는 플렉스가 깔린 폴더의 utilities폴더에 가면 다음과 같이 Adobe Flash

Builder 4.7 plug-in Utility 가 보인다.
![스크린샷 2020-02-01 오후 9 19 21](https://user-images.githubusercontent.com/12682121/73591984-88925d00-4538-11ea-87f4-afe7c25d2318.png)

저걸 클릭하여 설치한다.

설치중 다음과 같이 이클립스의 설치폴더를 물어보면 이클립스를 설치한 폴더를 찾아 선택해준다.
![스크린샷 2020-02-01 오후 9 19 54](https://user-images.githubusercontent.com/12682121/73591991-9cd65a00-4538-11ea-8468-1fa9e1200ce1.png)

다음으론 안내 메시지에서도 나왔듯이 플렉스를 원할하게 돌리려면 eclipse.ini 파일을 수정하여야 한다.

이클립스 설치폴더의 eclipse.app 파일을 마.오 를 눌러 패키지 내용보기를 선택하면 폴더들이 보인다.
![스크린샷 2020-02-01 오후 9 20 28](https://user-images.githubusercontent.com/12682121/73591997-afe92a00-4538-11ea-9b95-6e5e41276567.png)

저기서 Contents/MacOS 로가면 eclipse.ini 파일이 보인다.

파일을 열어 다음 하이라이트된 부분을 추가한다.
![스크린샷 2020-02-01 오후 9 20 57](https://user-images.githubusercontent.com/12682121/73591999-c1323680-4538-11ea-82ab-62f6d094fba8.png)

저장하고 닫는다.

모든 설치가 완료되었으므로 이클립스를 연다.
![스크린샷 2020-02-01 오후 9 21 36](https://user-images.githubusercontent.com/12682121/73592006-d909ba80-4538-11ea-8ff1-efb48ef44b57.png)

워크스페이스를 맞춰주고 OK를 누른다.

보면 맥 이클립스는 윈도우와 다르게 Server에 톰캣이 있지않다. 따라서 톰캣을 설정해줘야한다.
![스크린샷 2020-02-01 오후 9 22 09](https://user-images.githubusercontent.com/12682121/73592013-ec1c8a80-4538-11ea-9327-a01aff6eabd6.png)

Server의 Runtime Environment를 선택해 오른쪽의 Add 버튼을 눌러 자신의 버젼에 맞는 톰캣(여긴 Tomcat 7.0)을 선택후 Next.
![스크린샷 2020-02-01 오후 9 22 44](https://user-images.githubusercontent.com/12682121/73592019-00f91e00-4539-11ea-8b94-f5dbd2acc9fe.png)

Tomcat installation directory에서 Browse버튼을 눌러 톰캣이 설치된 폴더를 선택한다.

다음으로 svn을 설치하여야한다.

Help 메뉴에서 Install New Software를 선택하면 다음과 같은 화면이 보인다.
![스크린샷 2020-02-01 오후 9 23 21](https://user-images.githubusercontent.com/12682121/73592028-18d0a200-4539-11ea-9372-f56b589ce2fe.png)

Add 버튼을 눌러 위와같이 입력한다.
![스크린샷 2020-02-01 오후 9 24 01](https://user-images.githubusercontent.com/12682121/73592036-2e45cc00-4539-11ea-9cc2-bf341fea105d.png)

저기 두개를 선택후 Next.

Window 메뉴의 Show View를 선택하면 SVN이 설치된걸 볼 수 있다.
![스크린샷 2020-02-01 오후 9 24 36](https://user-images.githubusercontent.com/12682121/73592045-43baf600-4539-11ea-955a-d4060e3f0140.png)

