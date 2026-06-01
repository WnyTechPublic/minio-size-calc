# MinIO AIStor 사이징 계산기

MinIO AIStor 클러스터 도입 시 스펙 기반으로 스토리지 용량·성능·데이터 안정성을 실시간으로 계산하는 단일 HTML 계산기입니다.  
서버 설치 없이 브라우저에서 바로 실행되며, 오프라인 환경에서도 동작합니다.

---

## 주요 기능

### 계산 항목
- **Usable 용량** — Erasure Coding + RAID 효율 적용 후 실제 사용 가능한 스토리지
- **데이터 안정성** — 장애 허용 드라이브 수 / Write Quorum / MaxParity 케이스 자동 감지
- **네트워크 처리량** — NIC 대역폭 기반 노드당·클러스터 이론 상한
- **동시 요청 수** — RAM 기반 최대 동시 처리 요청 수 (MinIO 공식 공식 적용)

### 시각화
- **Erasure Set 드라이브 그리드** — 노드 × 드라이브 구성을 Set별 색상으로 표시, 미사용 드라이브 즉시 확인
- **Data / Parity 비율 바** — Parity 수치 변경 시 효율↔안정성 트레이드오프 실시간 반영
- **스토리지 분해 차트** — RAW 대비 EC 손실 / RAID 손실 / Usable 비율
- **Parity 옵션별 비교 차트** — Stripe Size 기준 전체 Parity 선택지의 Usable 용량 비교 (장애 허용 드라이브 수 포함)

### 운영 검증
- **권고 기준 체크리스트** — CPU·RAM·NIC·드라이브 타입·RAID·EC HA·Erasure Set 구성 항목별 ✅/⚠️/❌
- **환경별 기준 분리** — 운영(Production) / 개발(Dev) 환경 임계값 별도 적용
- **Erasure Set 구성 경고** — 잔여 드라이브 발생 시 낭비율 계산 및 해결 방법 안내

### 내보내기
- **텍스트 복사** — 계산 결과 전체를 클립보드에 복사
- **PDF 저장** — 브라우저 인쇄 기능으로 결과 패널 PDF 출력

---

## 사용 방법

별도 설치 없이 `index.html` 파일을 브라우저에서 열면 바로 실행됩니다.

```
index.html 다운로드 → 브라우저에서 열기 → 스펙 입력 → 결과 확인
```

### 입력 항목

| 섹션 | 항목 |
|------|------|
| ① 클러스터 기본 정보 | 노드 수, 환경 구분 (운영/개발) |
| ② 드라이브 구성 | 드라이브 타입, 용량 (TB/GB), 노드당 드라이브 수, RAID 구성 |
| ③ EC 설정 | Stripe Size (2/4/8/16), Parity 수 |
| ④ 메모리 / CPU / NIC | RAM, 물리코어 수, NIC 대역폭 |

---

## 계산 공식

```
총 RAW     = 드라이브 용량 × 노드당 드라이브 수 × 노드 수
유효 RAW   = 총 RAW × RAID 효율 계수  (JBOD/RAID0=1.0, RAID1/RAID10=0.5)
Usable     = 유효 RAW × (Data 샤드 수 / Stripe Size)
Data 샤드  = Stripe Size - Parity
장애 허용  = Parity  (MaxParity 시 Parity - 1)
Write Q    = Data    (MaxParity 시 Data + 1)
처리량     = NIC(Gbps) / 8  →  GBps
동시 요청  = RAM(GiB) × 1024 × 0.75 / 2
```

> MinIO 공식 EC 계산기([min.io/product/erasure-code-calculator](https://min.io/product/erasure-code-calculator)) 검증 7/7 케이스 일치 확인

---

## 기술 스택

| 항목 | 내용 |
|------|------|
| 파일 형식 | 단일 HTML (서버 불필요, 오프라인 동작) |
| 스타일 | Tailwind CSS CDN |
| 스크립트 | Vanilla JavaScript |
| 차트 | Chart.js 4.4.4 CDN |

---

## 참고 문서

- [MinIO AIStor Hardware Requirements](https://docs.min.io/aistor/reference/aistor-server/requirements/)
- [MinIO Erasure Coding](https://min.io/docs/minio/linux/operations/concepts/erasure-coding.html)
- [MinIO EC Calculator](https://min.io/product/erasure-code-calculator)
