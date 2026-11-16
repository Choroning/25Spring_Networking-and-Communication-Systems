# [2025학년도 봄학기] 자율주행통신시스템

![Last Commit](https://img.shields.io/github/last-commit/Choroning/25Spring_Networking-and-Communication-Systems)
![Languages](https://img.shields.io/github/languages/top/Choroning/25Spring_Networking-and-Communication-Systems)

이 레포지토리는 자율주행통신시스템 과목의 강의 노트와 수업 결과물을 정리한 아카이브입니다.

*작성자: 박철원 (고려대학교(세종), 컴퓨터소프트웨어학과) - 2025년 기준 2학년*
<br><br>

## 📑 목차

- [레포지토리 소개](#about-this-repository)
- [강의 정보](#course-information)
- [사전 요구사항](#prerequisites)
- [레포지토리 구조](#repository-structure)
- [라이선스](#license)

---


<br><a name="about-this-repository"></a>
## 📝 레포지토리 소개

- `Lecture-Notes/`에는 주차(`Week##`)별 강의 노트가 있으며, 한 주차에 다룬 여러 강의자료와 부록은 그 주차의 강의 노트 하나에 함께 정리했습니다.
- `Coursework/`에는 세 번의 과제 안내문과 과제 해설이 있습니다.
- 모든 강의 노트와 과제 해설은 영어(`.md`)와 한국어(`.ko.md`) 파일이 짝을 이룹니다.

> **🤖 생성형 AI 도구 사용**
> 이 레포지토리는 문서화에 [Claude Code](https://claude.ai/download)를 사용합니다.

<br><a name="course-information"></a>
## 📚 강의 정보

- **학기:** 2025학년도 봄학기 (3월 - 6월)
- **소속:** 고려대학교(세종)

|학수번호      |강의명    |이수구분|교수자|개설학과|
|:----------:|:-------|:----:|:------:|:----------------|
|`DAMO423-00`|자율주행통신시스템|전공선택|유시복 교수|미래모빌리티학과|

### 과목 개요

자율주행차의 차량 내부 유선 통신(CAN, LIN, FlexRay, 차량용 이더넷)과 C-ITS와 연계한 V2X 무선 통신(DSRC, WAVE, C-V2X, CALM)을 데이터 통신의 기초부터 배우는 전공선택 과목입니다. 교수자, 수업 일정, 평가, 수업 운영 규정, 진도는 [강의 정보 문서](Course-Information.ko.md)에 정리했습니다.

<br><a name="prerequisites"></a>
## ✅ 사전 요구사항

- 지정된 선이수 과목은 없습니다.
- 통신에 대한 기초 지식이 없어도 수강할 수 있도록 데이터 통신과 네트워크의 기초를 함께 다룹니다.

- **💻 개발 환경**

| 도구 | 회사 |  운영체제  | 비고 |
|:-----|:-------:|:----:|:------|
|한컴오피스 한글|한글과컴퓨터|macOS|과제 보고서 작성|

<br><a name="repository-structure"></a>
## 🗂 레포지토리 구조

```plaintext
25Spring_Networking-and-Communication-Systems
├── Lecture-Notes               주차별 강의 노트
├── Coursework                  과제 안내문과 과제 해설
├── images                      강의 노트와 과제 해설의 그림
├── Course-Information.ko.md    수업 운영 정보
├── Course-Information.md
├── LICENSE                     문서 라이선스 (CC BY-NC-SA 4.0)
├── LICENSE-CODE                코드 라이선스 (PolyForm Noncommercial 1.0.0)
├── README.ko.md
└── README.md
```

단원 목록은 [`Lecture-Notes` 색인](Lecture-Notes/README.ko.md)에, 과제는 [`Coursework` 색인](Coursework/README.ko.md)에 있습니다.

<br><a name="license"></a>
## 🤝 라이선스

이 레포지토리의 문서(강의 노트, 해설, 요약지)는 [CC BY-NC-SA 4.0](LICENSE), 코드는 [PolyForm Noncommercial 1.0.0](LICENSE-CODE)에 따라 배포됩니다. 강의자료와 교재에서 가져온 그림과 예제 코드, 강의가 배포한 과제 원본과 템플릿 코드는 원저작자에게 권리가 있으며 이 라이선스의 대상이 아닙니다.

---
