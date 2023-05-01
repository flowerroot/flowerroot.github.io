---
layout: single
title: "[minimal-mistakes] 프로필 사진 및 모양 변경하기."
categories: Development_Note
tag: []
toc: true
toc_sticky: true
---

오늘은 내 블로그 오른쪽에 있는 프로필 사진을 바꿔보려고 한다.  

사진을 바꾸는 방법과, 모양 및 크기를 바꾸는 방법에 대해 이야기해보자!  

시작하기 전, 이 글은 minimal-mistakes를 기준으로 작성되었다.  

### 프로필 사진 등록하기  

우선 너무 당연하지만, 프로필 사진이 없다면 모양을 바꾸는 것은 당연히 불가능하다.  
프로필 사진부터 원하는 사진을 골라/assets/에 업로드하자.  
사진 파일을 올린 후, 사진 파일 경로를 미리 복사해두자.  
나 같은 경우는 사진 파일의 경로가 /assets/avatar.jpg이다.  
등록했다면, 기본 경로에 존재하는/_config.yml 파일을 검색하여 수정해주자.  
**Site Author**을 검색하면 쉽게 찾을 수 있다.  

```md
# Site Author
author:
name : "Danggai"
avatar : 
bio : "1년차 뉴우비 개발자!"
location : "Uiwang, South Korea"
email : "donggi9313@gmail.com"
```

이 부분을 찾았다면 됐다.  

avatar에, 내가 사진 파일을 올린 경로를 복사해두자.  

```md
avatar : "/assets/avatar.jpg"
```

이런 모양새가 됐다면 됐다.  
파일을 저장해주고 잠시 기다리면, 정상적으로 블로그에 사진이 등록됐을 것이다!  
나중에 프로필 사진을 바꿀 때도, 같은 경로에 같은 이름으로 넣으면 금방 바꿀 수 있다.  

### 사진 파일 크기 및 곡률 바꾸기  

변경을 위해 _sass/minimal-mistakes/_sidebar.scss을 검색해서 수정해주자.  

```md
/*
   Author profile and links
   ========================================================================== */

.author__avatar {
  display: table-cell;
  vertical-align: top;
  width: 36px;
  height: 36px;

  @include breakpoint($large) {
    display: block;
    width: auto;
    height: auto;
  }

  img {
    max-width: 110px;
    border-radius: 50%;

    @include breakpoint($large) {
      padding: 5px;
      border: 1px solid $border-color;
    }
  }
}
```

Author profile and links`을 검색하면 쉽게 이 부분을 찾을 수 있다.  

- max-width를 바꾸면 내 프로필 사진의 최대 크기가 바뀐다. 내 sidebar보다 더 커지지는 않는다.  
- border-radius가 낮아질수록 사각형에 가까워진다. 50%면 완전한 원형이 된다.  
- breakpoint의 padding, border를 바꿔주면 프로필 사진 주변의 테두리를 바꿀 수 있다.  



출처 : https://danggai.github.io/github.io/Github.io-%ED%94%84%EB%A1%9C%ED%95%84-%EC%82%AC%EC%A7%84-%EB%B0%8F-%EB%AA%A8%EC%96%91-%EB%B3%80%EA%B2%BD%ED%95%98%EA%B8%B0!/ (2023.05.01)