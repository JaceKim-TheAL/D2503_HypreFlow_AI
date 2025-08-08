![하이퍼플로우TOP](./images/hyperflow_top.png)

### INDEX

<table>
  <tr>
    <td><a href="./HF릴리스1.0_D01.md">1.릴리스노트</a></td>
    <td><a href="./HF릴리스1.0_D02.md">2.구축및실행</a></td>
    <td><b href="./HF릴리스1.0_D03.md">3.사용가이드</b></td>
    <td><a href="./HF릴리스1.0_D04.md">4.참고용문서</a></td>
    <td><a href="./HF릴리스1.0_D05.md">5.관련리소스</a></td>
  </tr>
</table>

## 3. 사용가이드

- [[3-1.하이퍼플로우 슈퍼 노드 및 서비스 어댑터 사용자 가이드]](#3-1하이퍼플로우-슈퍼-노드-및-서비스-어댑터-사용자-가이드)
- [[3-2.FAISS 벡터 DB 활용을 위한 실전 가이드]](#3-2faiss-벡터-db-활용을-위한-실전-가이드)
- [[3-3.챗봇 스타일링 고급 설정 가이드]](#3-3챗봇-스타일링-고급-설정-가이드)
- [[3-4.프롬프트 템플릿 설정 가이드]](#3-4프롬프트-템플릿-설정-가이드)
- [[3-5.RAG 애플리케이션에서 출처 인용을 지시하는 팁/가이드]](#3-5rag-애플리케이션에서-출처-인용을-지시하는-팁가이드)

---
## 3-3.챗봇 스타일링 고급 설정 가이드

# EmbeddableBot 스타일링 가이드

## 소개

이 가이드는 **EmbeddableBot**의 **외형과 동작을 고급 스타일 옵션으로 커스터마이징하는 방법**을 설명합니다. 이러한 설정을 통해 챗봇의 **디자인과 사용자 경험을 브랜드 정체성** 또는 **특정 활용 목적**에 맞게 조정할 수 있습니다.

## 설정 구조

**EmbeddableBot**의 스타일 커스터마이징은 JavaScript 객체를 기반으로 하며, 구성은 다음과 같은 최상위 섹션들로 이루어져 있습니다:

```jsx
{
  size: { /* 크기 설정 */ },
  title: { /* 상단 타이틀 바 설정 */ },
  body: { /* 본문 영역 설정 */ },
  header: { /* 헤더 영역 설정 */ },
  response: { /* 챗봇 응답 메시지 설정 */ },
  prompt: { /* 사용자 입력창 설정 */ },
  references: { /* 지식 기반 출처 표시 설정 */ },
  enableRefresh: true/false, // 새로고침 버튼 사용 여부
  enableStartNewChat: true/false, // 새 대화 시작 버튼 사용 여부
  css: /* 커스텀 CSS 스타일 지정 */
}
```

## 크기 설정

챗봇 창의 가로, 세로 크기를 직접 지정할 수 있습니다:

```jsx
size: {
  width: 520,  // 픽셀 너비 
  height: 964, // 픽셀 높이
}
```

값을 `null`로 설정하면, EmbeddableBot은 현재 포함된 컨테이너의 크기에 맞춰 **자동으로 크기를 조절**합니다.

## 타이틀 바 설정

챗봇 상단의 타이틀 바를 원하는 스타일로 커스터마이징 할 수 있습니다:

```jsx
title: {
  label: "My Chatbot", // 타이틀 텍스트 (예: "@flowGraphName" 사용 시 플로우그래프 이름이 자동 표시됨)
  iconSrc: "/my-icon.svg", // 타이틀 바 왼쪽에 표시될 아이콘 이미지 경로
  backgroundSrc: "/path/to/background.png" // 타이틀 바 배경 이미지 경로
}
```

## 본문 영역 설정

챗봇의 대화 본문 영역 스타일과 동작을 설정할 수 있습니다:

```jsx
body: {
  backgroundSrc: "/path/to/background.png" // 대화 영역의 배경 이미지 경로
}
```

## 헤더 설정

타이틀과 서브타이틀을 포함한 선택적 헤더 영역을 추가할 수 있습니다:

```jsx
header: {
  iconSrc: "/path/to/icon.svg", // 헤더 영역에 표시될 아이콘 이미지
  title: "Welcome to my chatbot", // 헤더 제목 텍스트
  subtitle: "Ask me anything about our products" // 헤더 서브타이틀 (설명 문구)
}
```

## 응답 메시지 설정

챗봇의 **응답 메시지 스타일과 표시 방식**을 원하는 대로 커스터마이징할 수 있습니다:

```json
response: {
  iconSrc: "/path/to/icon.svg", // 챗봇 응답 메시지 옆에 표시될 기본 아이콘
  exceptionIconSrc: "/path/to/error-icon.svg", // 오류 발생 시 표시할 아이콘
  busySpinnerImgSrc: "/path/to/spinner.png", // 로딩 중 표시할 스피너 이미지
  busySpinnerVideoSrc: "/path/to/spinner.mp4", // 로딩 중 표시할 애니메이션 스피너 영상
  spinBusySpinner: true, // 스피너에 회전 애니메이션 적용 여부
  appEndedMessage: "Chat session ended" // 챗봇 세션 종료 시 표시할 메시지
}
```

## 입력 영역 설정

사용자가 메시지를 입력하는 입력창의 스타일과 동작을 설정할 수 있습니다:

```json
prompt: {
  iconSrc: "/path/to/icon.svg", // 입력창 왼쪽에 표시될 아이콘
  sendIconSrc: "/path/to/send-button.svg", // 전송 버튼 아이콘
  uploadIconSrc: "/path/to/upload-icon.svg", // 파일 업로드 버튼 아이콘
  backgroundSrc: "/path/to/bg.png", // 입력창 배경 이미지
  defaultPlaceHolder: "Ask me anything...", // 입력창 기본 안내 문구 (placeholder)
  autofocus: true, // 페이지 로드시 자동으로 입력창에 커서 포커싱 여부
  rows: 1 // 입력창에 기본으로 표시할 텍스트 줄 수
}
```

## 출처 표시 설정

지식 기반(Knowledge base)의 참조 정보가 챗봇 응답에 어떻게 표시될지를 설정할 수 있습니다.

```jsx
references: {
  maxRefs: 2, // 화면에 표시할 최대 참조 개수
  maxSegs: 5, // 처음 N개의 세그먼트에서만 참조를 표시
  disable: ["pages", "documents", "links"] // 비활성화할 참조 유형 (예: 페이지, 문서, 링크)
}
```

## 전반 설정

챗봇의 기본 동작이나 인터페이스 기능 활성화 여부를 제어하는 전역 옵션입니다.

```jsx
enableRefresh: true,         // 새로고침 버튼 표시 여부
enableStartNewChat: true,    // 새 대화 시작 버튼 표시 여부
```

## CSS 스타일링 커스터마이징

가장 유연한 커스터마이징 방식은 CSS를 직접 사용하는 것입니다. `css` 속성에 템플릿 리터럴(template literal) 형식으로 스타일을 지정할 수 있습니다:

```css
css: css`
  /* 루트 컨테이너 스타일 */
  border: 1px solid #dcdce0;
  font-family: "Noto Sans", sans-serif;

  /* 타이틀 바 */
  & .bot-title-bar { }
  & .bot-title { }
  & .bot-title-icon { }

  /* 본문 영역 */
  & .bot-body { }

  /* 헤더 */
  & .bot-header { }
  & .bot-header-icon { }
  & .bot-header-title { }
  & .bot-header-subtitle { }

  /* 응답 메시지 */
  & .bot-gen-text { } /* AI 응답 텍스트 컨테이너 */
  & .bot-gen-text-markdown { } /* AI 응답의 마크다운 내용 */

  /* 시스템 메시지 */
  & .bot-message { } /* 시스템 메시지 컨테이너 */
  & .bot-message-text { } /* 시스템 메시지 텍스트 */
  & .bot-message-text-markdown { } /* 시스템 메시지 내 마크다운 */
  & .bot-prior-prompt { } /* 사용자 메시지 버블 */

  /* 출처 영역 */
  & .bot-references { } /* 전체 출처 영역 */
  & .bot-reference { }
  & .bot-reference-doc { }
  & .bot-reference-page { }
  & .bot-reference-link { }
  & .bot-reference-image { }

  /* 피드백 버튼 */
  & .bot-feedback-bar { } /* 피드백 버튼 영역 */
  & .bot-feedback-button { } /* 피드백 버튼 기본 스타일 */
  & .bot-thumbs-up { }
  & .bot-thumbs-down { }
  & .bot-copy-button { }

  /* 로딩 표시 */
  & .bot-busy-message-container { }
  & .bot-busy-message { }
  & .bot-busy-spinner { }

  /* 입력 영역 */
  & .bot-prompt-bar { }
  & .bot-prompt { }
  & .bot-prompt-editor { }
  & .bot-prompt-icon { }
  & .bot-prompt-button { }
`
```

### CSS 애니메이션 예시

```jsx
& .bot-busy-spinner {
  width: 18px;  // 너비 설정
  height: auto;  // 높이는 자동 조정
  animation: rotate-center 1s linear infinite;  // 1초 주기로 무한 회전 애니메이션 적용
  transform-origin: center center;  // 회전 기준점을 정중앙으로 설정

  @keyframes rotate-center {
    from { transform: rotate(0deg); }  // 0도에서 시작
    to { transform: rotate(360deg); }  // 360도까지 회전
  }
}
```

### 커스텀 폰트 예시

```jsx
@font-face {
  font-family: "MyCustomFont";  // 사용할 사용자 정의 폰트 이름
  src: url("https://example.com/fonts/MyFont.woff") format("woff");  // 폰트 파일 경로 및 형식
  font-weight: 400;  // 기본 두께
  font-style: normal;  // 기본 스타일 (기울임 없음)
}

& .bot-body {
  font-family: "MyCustomFont", sans-serif;  // 본문 영역에 사용자 정의 폰트 적용, 없을 경우 기본 sans-serif 사용
}
```

## 라벨 커스터마이징

다양한 UI 구성 요소에 사용되는 텍스트 라벨은 필요에 맞게 커스터마이징 할 수 있습니다:

```jsx
branchSelectedMessage: "You selected: ", // 선택한 분기(선택지) 앞에 붙는 텍스트
attachedFileMessage: "File attached: "    // 업로드된 파일 이름 앞에 붙는 텍스트
```

## 스타일링 팁

1. **구조를 유지하세요**
    
    챗봇 요소들의 계층 구조를 유지해야 **기능이 올바르게 작동**합니다.
    
2. **색상은 변수로 관리하세요**
    
    색상 값을 변수로 설정하면 **일관성 있는 테마 구성과 유지보수**가 쉬워집니다.
    
3. **다양한 기기에서 테스트하세요**
    
    커스터마이징한 스타일이 **모든 화면 크기에서 제대로 동작하는지** 확인하세요.
    
4. **접근성을 고려하세요**
    
    **충분한 대비와 가독성 있는 글꼴 크기**를 유지해 사용자 모두가 쉽게 이용할 수 있도록 합니다.
    

## 자주 사용하는 커스터마이징 예시

### 메시지 말풍선 색상 변경

```css
css: css`
  & .bot-gen-text {    
    background: #f0f7f0;    // 챗봇 응답 말풍선 배경색
    border: 1px solid #e0e0e0;  // 챗봇 응답 말풍선 테두리
  }

  & .bot-prior-prompt {    
    background: #4285f4;  // 사용자 메시지 말풍선 배경색
    color: white;         // 사용자 메시지 텍스트 색상
  }
`
```

### 커스텀 폰트와 글꼴 스타일

```css
css: css`
  & .bot-body {
    font-family: 'Roboto', sans-serif;  // 전체 대화 영역에 적용될 기본 폰트
  }

  & .bot-gen-text-markdown {
    font-size: 15px;       // 챗봇 응답 텍스트의 글자 크기
    line-height: 1.5;      // 줄 간격
  }

  & .bot-prior-prompt {
    font-size: 14px;       // 사용자 입력 메시지의 글자 크기
    font-weight: 500;      // 사용자 메시지의 글자 굵기
  }
`
```

### 다크 모드

```css
css: css`
  & .bot-body {
    background-color: #222;  // 전체 배경 색상 (어두운 테마)
  }

  & .bot-gen-text {
    background: #333;        // 챗봇 응답 말풍선 배경색
    color: #eee;             // 챗봇 응답 텍스트 색상
  }

  & .bot-prior-prompt {
    background: #4a5a78;     // 사용자 메시지 말풍선 배경색
    color: #fff;             // 사용자 메시지 텍스트 색상
  }

  & .bot-prompt-bar {
    background: #333;        // 입력창 하단 바 배경색
    border-top: 1px solid #444;  // 상단 테두리 (구분선)
  }

  & .bot-prompt-editor {
    color: #eee;             // 입력 텍스트 색상
    background: #444;        // 입력창 배경색
  }
`
```

### 피드백 버튼 스타일링

```css
css: css`
  & .bot-feedback-bar {
    margin-top: -8px;        // 상단 여백 조정 (약간 겹치게)
    padding: 4px 10px;       // 안쪽 여백 설정
  }

  & .bot-feedback-button {
    background-color: #f5f5f5;    // 버튼 기본 배경색
    border: 1px solid #e0e0e0;    // 테두리 색상
    border-radius: 4px;          // 모서리 둥글게
  }

  & .bot-feedback-button.active {
    background-color: #e8f4ff;   // 활성화된 버튼 배경색
    border-color: #4285f4;       // 활성화된 버튼 테두리 색
    color: #4285f4;              // 텍스트 색상
  }

  & .bot-thumbs-up:hover {
    color: green;                // 좋아요 아이콘에 호버 시 색상
  }

  & .bot-thumbs-down:hover {
    color: red;                  // 싫어요 아이콘에 호버 시 색상
  }
`
```

## 전체 커스터마이징 예시

다음은 사용자 정의 테마를 적용한 전체 스타일링 예시입니다:

```jsx
{
  size: { 
    width: 400,         // 챗봇 너비 (px)
    height: 700         // 챗봇 높이 (px)
  },

  title: { 
    label: "Support Assistant",               // 타이틀 바에 표시할 텍스트
    iconSrc: "/assets/images/support-icon.svg" // 타이틀 바 왼쪽 아이콘
  },

  header: {
    title: "Customer Support",                      // 헤더 제목
    subtitle: "I'm here to help with your questions" // 헤더 설명 텍스트
  },

  response: {
    iconSrc: "/assets/images/bot-icon.svg",           // 챗봇 응답 아이콘
    busySpinnerImgSrc: "/assets/images/loading.gif"   // 로딩 시 스피너 이미지
  },

  prompt: {
    sendIconSrc: "/assets/images/send-button.svg", // 전송 버튼 아이콘
    defaultPlaceHolder: "Ask for help...",         // 입력창 기본 문구
    rows: 2                                         // 기본 입력 줄 수
  },

  references: {
    maxRefs: 3,      // 최대 표시할 참조 수
    disable: []      // 비활성화할 참조 유형 없음
  },

  enableRefresh: true,         // 새로고침 버튼 활성화
  enableStartNewChat: true,    // 새 대화 시작 버튼 활성화

  css: css`
    border: 1px solid #e0e0e0;         // 전체 테두리
    border-radius: 12px;               // 챗봇 외곽 둥근 모서리
    overflow: hidden;                  // 내용 넘침 숨김

    & .bot-title-bar {
      background: linear-gradient(90deg, #4285f4, #34a853); // 타이틀 바 배경 그라디언트
      padding: 12px 20px;            // 타이틀 바 안쪽 여백
    }

    & .bot-title {
      color: white;                  // 타이틀 텍스트 색상
      font-weight: 600;             // 타이틀 텍스트 굵기
    }

    & .bot-body {
      background-color: #f9f9f9;     // 챗봇 본문 배경
    }

    & .bot-gen-text {
      background: white;             // 챗봇 응답 배경
      border-radius: 12px;           // 말풍선 둥근 모서리
      box-shadow: 0 1px 3px rgba(0,0,0,0.1); // 약한 그림자 효과
    }

    & .bot-prior-prompt {
      background: #4285f4;           // 사용자 메시지 배경
      color: white;                  // 사용자 메시지 텍스트 색상
      border-radius: 12px;           // 말풍선 둥근 모서리
    }

    & .bot-prompt-bar {
      background: white;             // 입력창 배경
      border-top: 1px solid #eee;    // 상단 구분선
    }

    & .bot-feedback-button.active {
      background-color: #e8f4ff;     // 활성화된 피드백 버튼 배경
      border-color: #4285f4;         // 테두리 색
    }
  `
}
```

이 가이드를 활용하면, EmbeddableBot의 기능을 그대로 활용하면서도 브랜드에 맞는 개성 있는 챗봇 인터페이스를 만들 수 있습니다.

<br/>

[[TOP]](#index)

---
