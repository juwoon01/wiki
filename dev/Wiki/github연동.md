---
title: github연동
description: 
published: true
date: 2024-05-31T08:20:49.207Z
tags: 
editor: markdown
dateCreated: 2024-05-20T15:08:28.252Z
---

# Wiki.js github 연동 방법

<aside>
💡 Wiki.js 공식 문서를 참고하여 연동 했던 경험 정리

</aside>

## 서버 환경

- Oracle cloud의 Linux 환경에 Docker Compose를 이용해 Wiki.js를 설치했습니다.

## Wiki.js(Linux) 서버에서

`ssh-keygen -b 2048 -t rsa`  명령어를 통해 ssh 공개키, 개인키를 생성합니다.

## github 에서

1. 새 GitHub 저장소를 만듭니다.
2. **저장소에서 Settings** 탭을 클릭합니다 .
3. 왼쪽 탐색 메뉴에서 Deploy Keys**를** 클릭합니다 .
4. **Add deploy Key** 버튼을 클릭합니다 .

![deploykeys1.png](/deploykeys1.png)

5. 이 키에 대해 원하는 이름(예: Wiki)을 입력하고 이전에 생성된 공개 키의 내용을 `cat`명령어로 확인하여 붙여넣습니다. 
6. **쓰기 액세스 허용**이 선택되어 있는지 확인하세요 .
7. **키 추가** 버튼을 클릭하세요 .

![deploykeys2.png](/deploykeys2.png)

## Wiki.js 관리자 페이지에서

![스크린샷_2024-05-21_오전_12.11.47.png](/스크린샷_2024-05-21_오전_12.11.47.png)

1. 관리 영역의 왼쪽 탐색 메뉴에서 **보관소를 클릭합니다.**
2. **Git** 탭을 클릭하십시오 .
3. 다음 설정을 입력합니다.
    - Authentication Type : `ssh`
    - Repository URI: *GitHub 리포지토리 페이지의 **Code 탭에서** 녹색 버튼을 클릭 하고 **SSH 탭** 아래에 표시된 URI를 복사합니다 .*
    
    ![스크린샷_2024-05-21_오전_12.11.07.png](/스크린샷_2024-05-21_오전_12.11.07.png)
    
    - Branch:`main`
    - SSH Private Key Mode: contents (경로지정 path로 시도했을 때 오류가 발생해 contents로 진행했습니다.)
    - *A-SSH Private Key Path : 이전에 생성된 개인 키의 경로입니다. (contents로 진행했기 때문에 빈값입니다.)*
    - B-SSH Private Key Contents : 생성했던 개인 키의 내용을 `cat` 명령어로 확인하여 붙여놓습니다. (**이때, 맨 위와 아래의 -----BEGIN RSA PRIVATE KEY----- -----END RSA PRIVATE KEY----- 모두 포함하지 않으면 오류가 발생합니다.**)
    - Verify SSL Cretificate : 켜짐
    - Username : 빈값
    - Password / PAT : 빈값
    - Default Author Email : *GitHub 계정 이메일과 일치해야 합니다.*
    - Default Author Name : *GitHub 계정 이름과 일치해야 합니다.*
    - Local Repository Path : *저장소가 로컬로 복제될 위치를 선택하거나 기본값을 그대로 둡니다 `./data/repo`.*
    - Git Binary Path : 빈값
4. 바꾼 내용만 보관소에다가 저장하기로 설정합니다 .
5. **동기화 일정을 5분** 으로 설정합니다 .
6. 페이지 상단에 있는 **변경사항 적용** 버튼을 클릭하세요 .
7. **현재 상태** 패널이 업데이트될 때 까지 기다립니다. Git에 대한 새 항목이 녹색으로 표시되어야 합니다. 빨간색이면 구성에 오류가 있음을 의미합니다. 오류 메시지를 확인하고 다시 시도해보세요.

![스크린샷_2024-05-21_오전_12.20.23.png](/스크린샷_2024-05-21_오전_12.20.23.png)

## 참조

Wiki.js 공식문서 : https://docs.requarks.io/storage/git

Wiki.js github discussions : https://github.com/requarks/wiki/discussions