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
## 3-1.하이퍼플로우 슈퍼 노드 및 서비스 어댑터 사용자 가이드
- Sub-Index
  - [슈퍼 노드란?](#슈퍼-노드란)
  - [슈퍼 노드의 동작 원리](#슈퍼-노드의-동작-원리)
  - [슈퍼 노드의 분류 및 지원 서비스](#슈퍼-노드의-분류-및-지원-서비스)
    - [1. LLM 인터랙션 노드](#1-llm-인터랙션-노드)
    - [2. Content Processing Nodes (콘텐츠 처리 관련 노드)](#2-content-processing-nodes-콘텐츠-처리-관련-노드)
    - [3. Knowledge Base Nodes (지식 베이스 관련 노드)](#3-knowledge-base-nodes-지식-베이스-관련-노드)
    - [4. 웹 관련 노드](#4-웹-관련-노드)
  - [서비스 어댑터 사용하기](#서비스-어댑터-사용하기)
  - [고급 기능 활용 팁](#고급-기능-활용-팁)
  - [맺음말](#맺음말)
  - [부록: 기술 참고 자료](#부록-기술-참고-자료)
    - [서비스 타입 내부 코드](#서비스-타입-내부-코드)
    - [노드 내부 식별자](#노드-내부-식별자)

---  
## 소개

하이퍼플로우 고유의 슈퍼 노드 아키텍쳐는 다양한 서비스 구현을 단일 노드로 처리할 수 있도록 설계되어 있습니다.  이 가이드는 하이퍼플로우만의 독보적인 아키텍처 요소 중 하나인, **하나의 노드 타입으로 다양한 서비스 구현을 활용할 수 있는 구조**를 소개합니다. 이러한 방식은 인터페이스는 단순하고 일관되게 유지하면서도, AI 애플리케이션의**확장성과 유연성**을 획기적으로 향상시켜줍니다.

## 슈퍼 노드란?

하이퍼플로우에서 “슈퍼 노드(Super Node)”는 추상화된 서비스 아키텍처를 기반으로 동작하는 플로우 그래프 노드입니다. 이 노드는 동일한 기능을 가진 여러 서비스 구현체와 연결될 수 있으며, 마치 **하나의 본체에 다양한 도구를 교체 장착할 수 있는 고성능 전동 공구**처럼 활용됩니다.

슈퍼 노드의 주요 특징은 다음과 같습니다:

- 입력과 출력이 일관된 단일 노드 인터페이스
- 다양한 구현체 중 선택할 수 있는 서비스 선택 기능
- 플로우를 변경하지 않고도 서비스 전환이 가능한 구조

즉, 한 번 구성한 플로우 그래프를 그대로 유지한 채, 드롭다운 메뉴만 바꿔가며 다양한 AI 제공자나 데이터베이스 기술을 자유롭게 실험할 수 있습니다.

## 슈퍼 노드의 동작 원리

플로우 그래프에 슈퍼 노드를 추가하면, 해당 노드의 설정 패널에 **Service**(서비스) 파라미터가 표시됩니다.

이 파라미터를 통해 해당 노드가 사용할 구체적인 서비스 구현체를 선택할 수 있습니다. 예를 들어, **Call LLM** 노드는 OpenAI, Anthropic, Google 등 다양한 AI 제공자와 연결할 수 있습니다. 각 서비스는 모델 선택, 온도(temperature) 설정 등 고유의 파라미터를 가질 수 있지만, 노드의 **입력과 출력 인터페이스는 동일하게 유지**됩니다. 이러한 아키텍처는 다음과 같은 이점을 제공합니다:

- **간소화된 인터페이스**
    서비스 제공자별로 각각의 노드를 익힐 필요 없이, 하나의 노드만 학습하면 됩니다.
    
- **유연한 실험 환경**
    다양한 서비스를 손쉽게 전환하며 결과를 비교해볼 수 있습니다.
    
- **미래 확장성**
    HyperFlow에 새로운 서비스가 추가되면, 기존 플로우 그래프를 수정하지 않아도 해당 노드에서 바로 사용할 수 있습니다.
    
<br/>

[[TOP]](#index)

---
## 슈퍼 노드의 분류 및 지원 서비스

HyperFlow는 슈퍼 노드를 여러 기능별 카테고리로 구성하고 있습니다. 이 섹션에서는 주요 노드 유형과 각 노드에서 사용할 수 있는 서비스 구현체를 안내합니다.
<br/>

### 1. LLM 인터랙션 노드

### Call LLM (LLM 호출 노드)

**Call LLM** (LLM 호출 노드)는 텍스트 생성, 이미지 생성 등 다양한 작업을 위해 언어 모델과 상호작용할 수 있는 기본 노드입니다. 선택한 서비스에 따라 다양한 생성 모드를 지원합니다.

**텍스트 생성 서비스:**
- **Anthropic** - Claude 모델
- **OpenAI** - GPT 모델
- **Google AI** - Gemini 모델
- **Mistral AI** - Mistral 모델
- **Cohere** - Command 모델
- **Ollama** - 로컬 오픈소스 모델
- **Groq** - 고속 추론 처리
- **Deepseek** - Deepseek 모델
- **IBM** - Watson 모델
- **MCP Service** - 통합 모델 제어 프로토컬 (Model Control Protocol)

**이미지 생성 서비스:**
- **OpenAI** - DALL-E 모델
- **StabilityAI** - Stable Diffusion 모델
- **RecraftAI** - 이미지 생성 서비스
- **ComfyUI** - 자체 호스팅 이미지 생성

**비디오 생성 서비스s:**
- **StabilityAI** - 비디오 생성
- **RunwayML** - 비디오 제작 플랫폼

**오디오 생성 서비스:**
- **OpenAI** - 텍스트 음성 변환 (STT)
- **ElevenLabs** - 음성 합성
- **Kokoro** - 음성 생성
- **IBM** - Watson 음성 서비스

**사용 팁:**
- 서비스별로 강점이 다르므로 목적에 맞게 실험해보며 최적의 조합을 찾아보세요
- 하나의 플로우 그래프 내에 여러 개의 Call LLM (LLM 호출 노드)를 배치하고, 각기 다른 서비스를 연결할 수 있습니다
- MCP Service 옵션을 사용하면, 다양한 서비스 제공자를 하나의 표준화된 인터페이스로 제어할 수 있습니다

### Call LLM with Tools (도구를 사용한 LLM 호출 노드)

이 노드는 Call LLM (LLM 호출 노드)과 유사하게 동작하지만, 도구를 사용하는 LLM에 특화되어 설계되었습니다. 표준 Call LLM (LLM 호출 노드)과 동일한 서비스 옵션을 지원합니다.
<br/>

---
### 2. Content Processing Nodes (콘텐츠 처리 관련 노드)

### Import Content (콘텐츠 가져오기 노드)

이 노드는 HyperFlow 내에서 콘텐츠를 가져와 후속 처리를 할 수 있도록 합니다.

**지원 서비스:**

- **Built-in Importer** - HyperFlow의 기본 콘텐츠 가져오기 도구  
- **Langchain Importer** - Langchain을 활용한 콘텐츠 가져오기

### Transform Content (콘텐츠 변환 노드)

이 노드는 가져온 콘텐츠에 다양한 변환 작업을 적용합니다.

**지원 서비스:**
- **Langchain Transformer** - Langchain을 활용한 콘텐츠 변환
- **LlamaParse** -  LlamaIndex 기반 문서 파싱
- **Marker PDF to Markdown** - PDF를 Markdown으로 변환
- **Paddle OCR** - 광학 문자 인식(OCR)
- **Mistral AI** - AI 기반 콘텐츠 변환

### Segmentation Nodes (콘텐츠 분할 노드)

이 노드는 콘텐츠를 지식베이스에 적합한 단위로 분할합니다:

**일반 분할 (콘텐츠 분할 노드):**
- **Built-in Segmenter** - HyperFlow의 기본 분할 엔진
- **Langchain Segmenter** - Langchain을 활용한 분할

**특화된 분할:**
- **Segment PDF** - PDF 문서에 최적화된 분할
- **Segment HTML** - 웹 콘텐츠 전용 분할
- **Segment Text** - 일반 텍스트 문서용 분할
- **Segment Multi-modal PDF** - PDF에서 텍스트와 이미지를 함께 추출
<br/>

---
### 3. Knowledge Base Nodes (지식 베이스 관련 노드)

### Create Embedding Vectors (임베딩 생성 노드)

이 노드는 텍스트 조각으로부터 임베딩 벡터를 생성합니다.

**지원 서비스:**
- **OpenAI LLM Embed** - OpenAI의 임베딩 모델
- **Cohere LLM Embed** - Cohere의 임베딩 모델
- **Ollama Embed** - 로컬 임베딩 모델
- **MistralAI LLM Embed** - Mistral의 임베딩 모델

### Create Knowledge DB (지식 DB 생성 노드)

이 노드는 임베딩으로부터 지식베이스를 생성합니다.

**지원 서비스:**
- **FAISS Vector DB** - 로컬 벡터 데이터베이스
- **Pinecone DB** - 클라우드 기반 벡터 데이터베이스
- **ElasticSearch DB** - 벡터 검색 기능을 포함한 전체 텍스트 검색
- **MongoDB Vector DB** - 벡터 검색을 지원하는 문서형 데이터베이스

### Search Knowledge DB (지식 DB 검색 노드)

이 노드는 지식베이스 내에서 유사도 기반 검색을 수행합니다.

**지원 서비스:**
- Create Knowledge DB와 동일한 서비스들을 사용합니다
- 각 데이터베이스 유형마다 고유한 검색 파라미터를 가집니다

### Re-rank (순위 재조정 노드)

이 노드는 검색 결과의 관련도를 기준으로 순서를 재정렬하여 결과의 품질을 향상시킵니다.

**지원 서비스:**
- **Cohere Reranker** - Cohere의 순위 재조정 서비스
- **FlashRank Reranker** - 빠른 로컬 순위 재조정 서비스
<br/>

---
### 4. 웹 관련 노드

### URL Access (URL 접근)

이 노드는 지정된 URL로부터 콘텐츠를 가져옵니다.

**지원 서비스:**
- **HyperFlow URL Accessor** -웹 콘텐츠 수집 도구

### Web Search (웹 검색)

이 노드는 웹 검색을 수행합니다.

**지원 서비스:**
- **Google Web Search** -  검색 엔진 연동

<br/>

[[TOP]](#index)

---
## 서비스 어댑터 사용하기

슈퍼 노드를 사용할 때는 아래 단계를 따라 기능을 최대한 활용해보세요:

1. **노드를 플로우 그래프에 추가합니다** – 일반 노드와 동일하게 추가
2. **서비스 구현체를 선택합니다** – 노드 파라미터 패널에서 “Service” 드롭다운을 찾으세요
3. **서비스별 파라미터를 설정합니다** – 서비스 선택 후 해당 구현체에 특화된 파라미터가 표시됩니다
4. **입력과 출력을 연결합니다** – 선택한 서비스와 관계없이 입출력 구조는 동일하게 유지됩니다

### 필요한 서비스 선택하기

여러 서비스 구현 방식 중에서 선택할 때는 다음 요소들을 고려하세요:

- **기능** – 일부 서비스는 멀티모달 처리 등 특수 기능을 제공합니다
- **성능** – 서비스마다 속도나 품질에 차이가 있을 수 있습니다
- **비용** – 외부 서비스는 각각의 과금 체계를 따릅니다
- **API 키** – 대부분의 외부 서비스는 사용자가 직접 API 키를 입력해야 합니다

### 나의 API 키 사용하기 (BYOK, Bring Your Own Key)

외부 서비스(OpenAI, Anthropic 등)를 사용하려면, 사용자가 직접 API 키를 입력해야 합니다:

1. 하이퍼플로우의 **Account** 섹션으로 이동합니다
2. **API Configuration**을 선택합니다
3. 사용하려는 서비스의 API 키를 추가합니다
4. 해당 키는 관련된 서비스 어댑터를 사용할 때 자동으로 적용됩니다

<br/>

[[TOP]](#index)

---
## 고급 기능 활용 팁

### 서비스 전환을 통한 실험

슈퍼 노드의 가장 강력한 기능 중 하나는, 서로 다른 서비스 구현체를 손쉽게 전환할 수 있다는 점입니다:

1. 원하는 서비스 구현체를 사용해 플로우 그래프를 구성합니다
2. 플로우를 실행하고 결과를 확인합니다
3. 노드의 파라미터에서 서비스 선택 항목을 변경합니다
4. 다시 실행하여 다양한 서비스 제공자의 결과를 비교합니다

이 기능 덕분에 HyperFlow는 다양한 AI 서비스를 벤치마킹하거나 특정 용도에 가장 적합한 서비스를 찾는 데 이상적인 도구가 됩니다.

### 다양한 서비스 조합하기

AI 워크플로우의 각 단계에 서로 다른 서비스를 사용할 수 있습니다:

- 초기 텍스트 생성에는 OpenAI를 사용합니다
- 검색 결과의 재정렬에는 Cohere를 사용합니다
- 지식베이스 저장에는 ElasticSearch를 사용합니다

이러한 조합형 접근 방식은 각 서비스 제공자의 강점을 효과적으로 활용할 수 있게 해줍니다.

### MCP 서비스 어댑터

MCP(Model Control Protocol) 서비스 어댑터는 다른 어댑터와 구별되는 특징이 있습니다. 이 어댑터는 여러 LLM 제공자를 하나의 통합 API로 연결할 수 있는 **표준화된 인터페이스**를 제공합니다.

다음과 같은 상황에서 특히 유용합니다:

- 다양한 제공자 간에도 일관된 인터페이스로 작업하고자 할 때
- 서로 다른 회사의 모델을 손쉽게 전환하고자 할 때
- API 변경에 영향을 받지 않고 플로우를 미래에도 유지하고자 할 때

<br/>

[[TOP]](#index)

---
## 맺음말

하이퍼플로우의 슈퍼 노드 아키텍처는 AI 애플리케이션 구축에 탁월한 유연성을 제공합니다. 무엇을 할 것인지에 대한 정의(노드)와 그것을 어떻게 실행할 것인지(서비스)를 분리함으로써, 강력하고 유연한 플로우 그래프를 구성할 수 있으며, 빠르게 변화하는 AI 환경에 따라 쉽게 진화시킬 수 있습니다.

하이프펄로우에 새로운 서비스나 기능이 추가되면, 기존 노드에서도 자동으로 사용할 수 있으므로 AI 애플리케이션을 다시 구축할 필요 없이 최신 상태를 유지할 수 있습니다.

## 부록: 기술 참고 자료

### 서비스 타입 내부 코드

참고용으로, 각 서비스 유형을 식별할 때 사용되는 내부 코드 목록은 다음과 같습니다:

**LLM Services (LLM 서비스):**

- `llm.generate` - 텍스트 생성
- `llm.generate.image` - 이미지 생성
- `llm.generate.video` - 비디오 생성
- `llm.generate.audio` - 오디오 생성
- `llm.generate.transcription` - 음성 텍스트 변환 (STT)
- `llm.generate.upscaling` - 이미지 해상도 향상
- `llm.embedding.text` - 텍스트 임베딩

**RAG Content Services (RAG 콘텐츠 처리 서비스):**

- `rag.content.import` - 콘텐츠 가져오기
- `rag.content.segment` - 콘텐츠 분할
- `rag.content.transform` - 콘텐츠 변환
- `rag.pdf.segment` - PDF 분할
- `rag.html.segment` - HTML 분할
- `rag.text.segment` - 텍스트 분할
- `rag.multimodal-pdf.segment` - 멀티모달 PDF 분할

**Knowledge Database Services (지식베이스 서비스):**

- `rag.knowledgeDB.create` - 지식베이스 생성
- `rag.knowledgeDB.select` - 지식베이스 선택
- `rag.knowledgeDB.search` - 지식베이스 검색
- `rag.rerank` - 검색 결과 재정렬

**Tool Services (도구 서비스):**

- `tools.web.urlAccess` - 웹 콘텐츠 수집
- `tools.web.search` - 웹 검색

### 노드 내부 식별자

참고용으로, 본 가이드에서 언급된 노드들의 표시 이름과 내부 식별자는 다음과 같습니다:

- Call LLM (LLM 호출 노드) (`contextAndGenerate`)
- Call LLM with Tools (LLM 툴 에이전트 노드) (`toolAgent`)
- Import Content (콘텐츠 가져오기 노드) (`contentImport`)
- Transform Content (콘텐츠 변환 노드) (`transformContent`)
- Segment Content (콘텐츠 분할 노드) (`segmentContent`)
- Segment PDF (PDF 분할 노드) (`segmentPDF`)
- Segment HTML (HTML 분할 노드) (`segmentHTML`)
- Segment Text (텍스트 분할 노드) (`segmentText`)
- Segment Multi-modal PDF (멀티모달 PDF 분할 노드) (`segmentMultiModalPDF`)
- Create Embedding Vectors (임베딩 생성 노드) (`createEmbeddings`)
- Create Knowledge DB (지식 DB 생성 노드) (`createKnowledgeDB`)
- Search Knowledge DB (지식 DB 검색 노드) (`searchKnowledgeDB`)
- Re-rank (순위 재조정 노드) (`rerank`)
- URL Access (URL 접근 노드) (`urlAccess`)
- Web Search (웹 검색 노드) (`search`)
- Real-time Knowledge Injector (실시간 지식 주입 노드) (`knowledgeInjector`)
- Data Transform (데이터 변환 노드) (`transform`)

<br/>

[[TOP]](#index)

---
