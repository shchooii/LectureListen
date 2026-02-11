# LectureListen
> 2023 동계 AI SCI 부트캠프 – “인공지능을 통한 음성인식” <br> 
> 🏆 최우수상 수상 (참가 8 팀 중 2 팀 선발)

한국어 대학강의 음성(4800 h)을 이용해 ESPnet으로 STT 모델을 학습하고  
음성 ↔︎ ChatGPT 대화를 지원하는 서비스를 구축했습니다.

## 프로젝트 개요

LectureListen은  
대학 강의 환경에 특화된 한국어 음성인식 모델을 구축하고,  
이를 실제 서비스로 연결하는 것을 목표로 했습니다.

일반 음성인식 모델은  
강의 환경에서 다음과 같은 문제를 보입니다:

- 빠른 말속도  
- 전문 용어 및 드문 음절  
- 반복 발화(이중 전사)  
- 발화 길이 편차  

본 프로젝트는 이러한 도메인 특성을 반영해  
STT 성능을 개선하는 데 초점을 맞췄습니다.



## 목표

- 대학 강의 도메인에서 CER 25% 이상 개선  
- 오류 유형 분석을 통한 개선 포인트 도출  
- 학습 모델을 실시간 음성-ChatGPT 서비스로 연결  



## 개발 환경

| 항목 | 사양 |
|--|--|
| GPU | RTX A6000 48GB ×4 |
| Python | 3.10 |
| PyTorch | 2.1.1 |
| ESPnet | 2023-10 release |
| Mixed Precision | AMP(fp16) |



## 프로젝트 구조

```

code/espnet      # ESPnet 기반 학습 코드
service/         # FastAPI + JS 서비스
docs/            # 발표 자료

```



## 데이터 준비

AIHUB 한국어 대학강의 데이터 사용:

- Training: 1,712,897 utterances  
- Validation: 193,257 utterances  
- 데이터 분할: 8:1:1  

전처리 과정:

- 음성-라벨 매칭 검증  
- wav.scp / text 리스트 생성  
- 길이 필터링 및 정제



## 모델 설정

- Conformer encoder-decoder  
- SpecAugment 적용  
- Encoder depth 12  
- Beam size 10  
- CTC/Attention weight 1.0  
- Language Model 사용하지 않음



## 학습 파이프라인

| 단계 | 내용 |
|--|--|
| 데이터 변환 | flac 변환 및 필터링 |
| 통계 수집 | 정규화 통계 계산 |
| 모델 학습 | 35 epochs |
| 디코딩 | Validation 데이터 평가 |

총 학습 시간 약 44시간.



## 주요 실험 결과

| 설정 | CER (%) |
|--|--|
| Baseline | 12.4 |
| 이중 전사 정리 | 11.6 |
| LM dropout 적용 | 9.8 |
| 최종 모델 | **9.3** |

최종적으로  
CER을 약 25% 이상 개선했습니다.  
(12.4 → 9.3)



## 서비스 구현

학습된 모델을 실제 서비스로 연결했습니다.

### 주요 API

- `/stt`  
  음성 → 텍스트 변환

- `/chat`  
  텍스트 → ChatGPT 응답

- `/voice-chat`  
  음성 업로드 → STT → ChatGPT 대화

FastAPI 기반으로  
실시간 음성-텍스트 변환과 대화가 가능하도록 구현했습니다.

## demo
<div align="center"> <table> <tr> <td align="center"><strong>🖼️ 구성 </strong></td> <td align="center"><strong>📽️ STT 및 ChatGPT 응답</strong></td> </tr> <tr> <td> <img src="https://github.com/user-attachments/assets/f08a984f-def4-414a-801f-abd9fefb3580" width="400"/> </td> <td> <img src="https://github.com/cshooon/LectureListen/assets/113033780/88dc14a1-e47c-4591-bb5f-5cf2cc8bdef2" width="400"/> </td> </tr> </table> </div>

## 참고

- [ESPnet Toolkit](https://github.com/espnet/espnet)

- [AIHUB 한국어 대학강의 음성 데이터](https://aihub.or.kr)

본 프로젝트는  
ESPnet 기반 한국어 음성인식 학습 레시피를 활용하여  
대학 강의 도메인에 맞게 실험 및 튜닝을 진행한 사례입니다.
