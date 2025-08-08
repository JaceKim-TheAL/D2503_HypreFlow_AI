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
## 3-5.RAG 애플리케이션에서 출처 인용을 지시하는 팁/가이드
- Sub-Index
  - [RAG 기반의 애플리케이션 제작 시, 출처 인용을 하도록 지시하는 팁/가이드](#rag-기반의-애플리케이션-제작-시-출처-인용을-하도록-지시하는-팁가이드)
  - [하이퍼플로우가 지식 출처를 식별하는 방식](#하이퍼플로우가-지식-출처를-식별하는-방식)
  - [RAG 애플리케이션에서 프롬프트 엔지니어링의 중요성](#rag-애플리케이션에서-프롬프트-엔지니어링의-중요성)
  - [HyperFlow로 만드는 RAG 애플리케이션](#hyperflow로-만드는-rag-애플리케이션)
  - [출처 인용이 최종 사용자에게 왜 중요한가요?](#출처-인용이-최종-사용자에게-왜-중요한가요)
  - [마무리](#마무리)

---
## RAG 기반의 애플리케이션 제작 시, 출처 인용을 하도록 지시하는 팁/가이드

## 출처가 포함된 응답 생성을 위한 인스트럭션(지시) 템플릿

하이퍼플로우의 RAG 기능은 출처를 정확히 인용하는 강력한 지식 기반 AI 애플리케이션을 구현할 수 있게 해줍니다. 아래는 실제 프로덕션 환경의 HyperFlow 애플리케이션에서 신뢰할 수 있는 지식 인용을 위해 검증된 인스트럭션 템플릿입니다.

### 핵심 세그먼트를 참조하도록 하는 인스트럭션(지시)

아래 인스트럭션을 Instruction(지시 노드)에 추가하면, 답변에 정확한 출처가 포함되도록 만들 수 있습니다. **영어로 작성된 인스트럭션을 더 잘 이해하는 경향이 있으므로, 가능하면 영어로 입력하는 것을 권장합니다.**

```
- **Use Knowledge Segments:** Use as much helpful detail as possible from the knowledge segments provided below.
- **Segment IDs:** For each knowledge segment you reference in your answer, output their `segment_ids` at the **end** of your answer in JSON format: `{"segment_ids": ["id1", "id2"]}`.
  - Do **not** mention segment IDs or JSON labels in the main answer.
  - Do **not** insert any labels like "json" before the JSON output.
  - You must reference **at least one** knowledge segment in order to provide a positive answer.
- **Written Answer Required:** Do not answer with JSON references alone; always provide a written answer as well.

[해석]
- **지식 세그먼트 활용:** 아래에 제공된 지식 세그먼트를 최대한 활용해 유용한 정보를 포함하세요.  
- **세그먼트 ID 출력:** 답변에서 참조한 각 지식 세그먼트의 `segment_ids`를 **답변 마지막에** JSON 형식으로 출력하세요: `{"segment_ids": ["id1", "id2"]}`
  - 본문 답변 내에는 segment ID나 JSON 라벨을 **언급하지 마세요.**
  - JSON 앞에 `"json"`과 같은 라벨을 **추가하지 마세요.**
  - **적극적인 답변을 하기 위해서는** 적어도 하나 이상의 지식 세그먼트를 반드시 참조해야 합니다.
- **문장형 답변 필수:** JSON만으로 답변하지 마세요. 반드시 문장 형태의 서술형 답변도 함께 제공해야 합니다.
```

### 특수 포맷 처리하기

지식 세그먼트에 수식, 코드 등 특수한 포맷이 포함된 경우에는 아래와 같은 인스트럭션을 추가하세요:

```
- **Knowledge segment format:** The knowledge segments are formatted in markdown in most cases. Please preserve as much of the markdown formatting as possible, particularly embedded latex math equations.
- **Math equations:** You **must** preserve markdown/latex math formulas from the knowledge segments, ensuring the $ math markers are inserted around an equation on its own, but repair any equation markup for inline math segments, with $ immediately surrounding the inline equation form in output markdown.

[해석]
- **지식 세그먼트 포맷:** 대부분의 지식 세그먼트는 마크다운 형식으로 구성되어 있습니다. 특히 포함된 LaTeX 수학 수식은 최대한 원래 마크다운 포맷을 유지해주세요.  
- **수학 수식:** 지식 세그먼트에 포함된 마크다운/LaTeX 수식을 반드시 유지해야 합니다.  
  - 블록 수식의 경우 `$` 기호로 수식 전체를 감싸 단독으로 출력되도록 하세요.  
  - 인라인 수식의 경우, `$` 기호가 수식의 앞뒤에 정확히 붙어 있도록 하여 마크다운 내에서 올바르게 표시되도록 정리해주세요.
```

### 다국어 지원을 위한 인스트럭션

여러 언어를 지원하는 애플리케이션의 경우 다음 사항을 참고하세요:

```
- **Language:** Provide helpful answers in **Korean if asked in Korean** and in **English if asked in English**.
- **Unable to Answer:** If the user's query cannot be answered from the provided information, respond with: "**죄송하지만, 해당 문제에 대해 도와드릴 만한 정보가 충분하지 않습니다.**" (or appropriate message in other languages)

[해석]
- **언어:** 사용자의 질문이 한국어일 경우 **한국어로**, 영어일 경우 **영어로** 도움이 되는 답변을 제공하세요.  
- **답변 불가 시:** 제공된 정보로는 답변이 어려운 경우, 다음과 같이 응답하세요:  
  "**죄송하지만, 해당 문제에 대해 도와드릴 만한 정보가 충분하지 않습니다.**"  
  (또는 해당 언어에 맞는 적절한 메시지를 사용하세요)
```

### 출력 포맷 제어

출력 결과의 형식을 일관되게 유지하려면 다음 사항을 따르세요:

```
- **Numbered Lists:** If you output a numbered list, ensure the numbers are **sequential**, starting from 1.
- **Avoid Unnecessary Labels:** Do **not** include any unnecessary labels or headings in your answer.
- **Detailed Instructions:** Provide detailed instructions and **always** provide the segment_IDs list for the knowledge segments used to form your answer. **Do not include** explanations about the referenced knowledge-segments, just the answer followed by the segment_id list.

[해석]
- **번호 목록:** 번호가 매겨진 목록을 출력할 경우, 반드시 **1부터 시작하는 순차적 번호**를 사용하세요.  
- **불필요한 라벨 지양:** 답변에 **불필요한 라벨이나 제목을 포함하지 마세요.**  
- **상세한 안내:** 가능한 한 자세한 설명을 제공하고, **항상** 답변에 사용된 지식 세그먼트의 `segment_ids` 리스트를 함께 포함하세요.  
  단, 참조한 지식 세그먼트에 대한 **추가 설명은 포함하지 말고**, 답변 본문 뒤에 `segment_ids` 리스트만 출력하세요.
```
<br/>

[[TOP]](#index)

---
## 하이퍼플로우가 지식 출처를 식별하는 방식

하이퍼플로우는 AI 응답에 사용된 지식의 출처를 segment ID로 연결해줍니다. 이 섹션에서는 그 작동 방식을 알아보고 프롬프트를 통해 이를 어떻게 최적화할 수 있는지 설명합니다.

### 지식 세그먼트 메타데이터

HyperFlow의 지식 세그먼트는 다양한 메타데이터를 포함할 수 있으며, 적절히 지시할 경우 사용자에게 함께 표시됩니다:

- PDF 문서의 **페이지 번호**
- 텍스트 세그먼트와 연결된 **추출 이미지**
- 지식이 가져온 **웹사이트 링크**
- **문서 제목** 및 기타 출처 정보
- 정보가 생성되거나 가져온 **시점(Timestamp)**

이러한 메타데이터는 RAG 애플리케이션의 투명성과 활용도를 높여주지만, 정확한 세그먼트 참조 감지가 전제되어야 합니다.

### 답변 출처 표시

AI가 지식 베이스의 내용을 활용해 답변할 때, 어떤 정보를 어디서 가져왔는지 출처를 함께 표시하는 것이 중요합니다. HyperFlow는 응답 내용과 해당 지식 세그먼트를 자동으로 연결해 출처를 명확히 보여줄 수 있도록 도와줍니다.

### 작동 방식

이 시스템은 LLM 응답에 세그먼트 참조가 포함되어 있는지를 휴리스틱 방식으로 감지하여 처리합니다:

1. **패턴 인식**
시스템은 다양한 참조 패턴을 탐지합니다:
    - JSON 형식: `{"segment_ids": ["001", "002"]}`
    - 혼합 괄호 형식: `(segment_ids: ['001', '002'])`
    - 자연어 표현: `(referencing segments 001, 002)`
2. **참조 추출 및 처리**
    - 감지된 패턴에서 segment ID를 추출합니다
    - 출력 응답에서 참조 문구를 제거합니다
    - 해당 ID에 대응하는 지식 세그먼트와 메타데이터를 불러옵니다
    - AI 응답과 함께 출처 정보를 표시합니다
3. **위치 기반 우선 처리**
응답 끝에 위치한 참조는 우선적으로 처리되며, 이는 LLM이 올바른 인스트럭션을 따를 경우 보통 인용 정보를 응답 마지막에 배치하기 때문입니다.
<br/>

[[TOP]](#index)

---
## RAG 애플리케이션에서 프롬프트 엔지니어링의 중요성

### 왜 프롬프트 엔지니어링이 핵심일까요?

AI에게 '출처를 어떻게 인용할지'를 어떻게 지시하느냐에 따라, 사용자가 각 지식 세그먼트에 포함된 **풍부한 메타데이터**를 제대로 확인할 수 있는지가 달라집니다:

**1. 출처 품질 저하 문제**

- 정확한 지시 없이 LLM을 사용할 경우 다음과 같은 문제가 발생할 수 있습니다:
    - 지식 세그먼트 ID나 출처를 아예 누락
    - 인용 형식이 일관되지 않아 후처리 및 파싱이 어려움
    - 일반 텍스트와 출처 ID를 섞어 출력
    - 실제로 사용되지 않은 세그먼트를 인용
1. **메타데이터의 가시성 확보**
    - 올바른 Prompt Engineering을 통해 다음을 보장할 수 있습니다:
        - 실제 사용된 모든 관련 세그먼트가 정확히 인용됨
        - 일관된 형식으로 인용되어 시스템이 자동 인식 가능
        - 이미지, 링크, 페이지 번호 등 부가 정보까지 사용자에게 노출
        - 지식 출처가 명확하게 표시되어 신뢰성 확보
        

### 정리된 형식의 입력 vs. 자유형 입력

HyperFlow에서는 외부 지식을 불러오는 두 가지 방식이 있습니다:

### 정리된 정보 형식으로 입력

Prompt 템플릿 편집기에서 정리된 형식을 선택하면, 외부 정보를 아래와 같이 세그먼트 ID와 함께 구조화된 형태로 입력할 수 있습니다.

```
- **Segment ID:** "001"
  - **Content:** # How do I use the Animation Control group?
- **Segment ID:** "002"
  - **Content:** # How to solve when Contact Force is negative
  
- **세그먼트 ID:** "001"
  - **내용:** # Animation Control 그룹 사용 방법
- **세그먼트 ID:** "002"
  - **내용:** # Contact Force가 음수일 때의 문제 해결  
```

이러한 방식은 LLM이 특정 정보를 명확하게 인용하도록 도와주지만, **그에 맞는 별도의 프롬프트 작성 방식이 필요합니다.**

### 자유형 형식으로 입력

비정형 방식에서는 세그먼트마다 ID가 따로 붙지 않기 때문에, LLM이 어떤 내용을 참조했는지 스스로 파악하고 출처를 정확히 밝혀야 합니다. 따라서 프롬프트를 **더 신중하게 설계해야** 합니다.

### RAG 프롬프트를 잘 만드는 방법

LLM이 정확하고 일관되게 출처를 인용하도록 하려면, 다음과 같은 프롬프트 설계 팁을 참고하세요:

1. 명확한 형식 지정:
    
    ```
    For each knowledge segment you reference, output the segment IDs at the END of your answer in this exact format:
    {"segment_ids": ["001", "002", "003"]}
    Do not use any other format or label for the segment IDs.
    
    [해석]
    답변에서 지식 세그먼트를 참조했다면, 반드시 응답의 마지막 줄에 아래와 같은 JSON 형식으로 해당 세그먼트 ID를 출력해야 합니다:
    {"segment_ids": ["001", "002", "003"]}
    그 외의 형식이나 라벨은 절대 사용하지 마세요.
    ```
    
2. 출력 위치 지정:
    
    ```
    First provide your complete answer. Then, on a new line at the very end of your response, list the segment IDs using the specified JSON format.
    
    [해석]
    먼저 질문에 대한 전체 답변을 작성한 후,
    응답의 맨 마지막 줄에 새 줄을 추가해, 지정된 JSON 형식으로 세그먼트 ID를 출력하세요.
    ```
    
3. 최소 참조 표기 조건:
    
    ```
    You must reference at least one knowledge segment to provide a valid answer. If you cannot find relevant information in the provided knowledge segments, indicate that you don't have sufficient information.
    
    [해석]
    유효한 답변을 제공하려면 반드시 하나 이상의 지식 세그먼트를 참조해야 합니다.
    제공된 세그먼트 내에서 관련 정보를 찾을 수 없는 경우에는, 대답을 내기 위한 정보가 충분하지 않다고 명확히 밝혀야 합니다.
    ```
    
4. 형식 유지 지침:
    
    ```
    Preserve all formatting from the original knowledge segments, including markdown, math equations, and code blocks.
    
    [해석]
    지식 세그먼트에 포함된 마크다운, 수식, 코드 블록 등의 형식을 수정하거나 생략하지 말고 원형 그대로 유지해 주세요.
    ```
    
5. 프롬프트 검증하기:
    
    지침이 일관된 인용 동작을 유지하는지 확인하려면, 항상 **다양한 질의로 테스트를 수행해야 합니다.**
    
<br/>

[[TOP]](#index)

---
## HyperFlow로 만드는 RAG 애플리케이션

HyperFlow에서 일반적인 RAG 기반 챗봇 플로우 그래프는 다음과 같은 단계로 구성됩니다:

1. 사용자의 질의 입력
2. 지식 검색
3. LLM의 지식 기반 응답 생성
4. 출처(참조) 처리 및 표시

이 중 **Instructions 노드**는 세그먼트 참조가 정확하게 이뤄지도록 하는 데 핵심적인 역할을 합니다.

이 노드의 프롬프트에는 다음 항목이 포함되어야 합니다:

1. AI의 역할과 기능 정의
2. 지식 세그먼트 사용 방식에 대한 명확한 안내
3. 세그먼트 참조 형식에 대한 구체적인 명세
4. 마크다운, 수식, 코드 등의 특수 형식 처리 지침
5. 관련 정보가 부족할 때의 대응 방식
<br/>

[[TOP]](#index)

---
## 출처 인용이 최종 사용자에게 왜 중요한가요?

출처가 신뢰성 있게 인용되면 사용자에게 다음과 같은 중요한 이점을 제공합니다:

1. **풍부한 문맥 정보**
    
    단순한 텍스트뿐 아니라 이미지와 메타데이터 등 다양한 배경 정보를 함께 확인할 수 있습니다.
    
2. **탐색 가능성**
    
    원본 출처로 연결되는 링크를 통해 더 깊이 있는 내용을 쉽게 찾아볼 수 있습니다.
    
3. **투명성 확보**
    
    AI가 어떤 정보를 기반으로 답변했는지 명확히 드러나, 신뢰를 높입니다.
    
4. **검증 가능성**
    
    사용자가 AI의 응답 내용을 실제 자료와 비교해 직접 확인할 수 있습니다.
    
5. **규제 준수**
    
    출처 추적이 필수인 산업(예: 금융, 의료 등)에서는 특히 중요한 기능입니다.
    
<br/>

[[TOP]](#index)

---
## 마무리

RAG 애플리케이션에서 **정확한 지식 인용**을 구현하려면 다음 요소가 필요합니다:

1. **Instructions 노드에 대한 정밀한 프롬프트 설계**
2. **세그먼트 ID 파서의 작동 방식에 대한 이해**
3. **LLM 모델별 인용 지침 반응 차이에 대한 인식**
4. **다양한 질문 유형에 대한 테스트 수행**

이러한 베스트 프랙티스를 HyperFlow의 RAG 플로우에 적용하면,

단순히 정보 제공을 넘어 **더 투명하고 신뢰할 수 있는 AI 솔루션**을 구축할 수 있습니다.

<br/>

[[TOP]](#index)

---
