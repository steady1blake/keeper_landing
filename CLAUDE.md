# KEEPER 랜딩페이지 프로젝트

## 프로젝트 개요
한화비전 KEEPER CCTV 자영업자 전용 랜딩 페이지.  
단일 HTML 파일(`index.html`)로 구성된 정적 사이트. Vercel로 배포.

- **GitHub**: https://github.com/steady1blake/keeper_landing.git
- **로컬 경로**: `C:\Users\ok45i\keeper_landing`
- **배포**: Vercel (vercel.json 설정 포함)

---

## 파일 구조

```
keeper_landing/
├── index.html       # 전체 랜딩페이지 (CSS·JS 포함 단일 파일)
├── vercel.json      # Vercel 라우팅 설정
├── README.md
└── .claude/
    ├── settings.json  # 자동 push 훅 설정
    └── launch.json    # 로컬 미리보기 서버 설정
```

---

## 로컬 개발 환경

### 미리보기 서버 실행
```bash
npx serve -p 3000 C:\Users\ok45i\keeper_landing
```
또는 Claude Code에서 `preview_start("keeper-landing")` 사용.

### GitHub 자동 push 훅
`.claude/settings.json`에 PostToolUse 훅 설정됨.  
**이 폴더를 작업 디렉토리로 열었을 때만 동작**:
```
claude C:\Users\ok45i\keeper_landing
```
파일 수정 시 자동으로 `git add -A → commit → push` 실행.

### 수동 push
```powershell
cd C:\Users\ok45i\keeper_landing
git add index.html
git commit -m "설명"
git push
```

---

## 페이지 섹션 구조 (index.html)

| 섹션 | 설명 | HTML 주석 |
|------|------|-----------|
| NAV | 고정 상단 네비게이션 | `<!-- NAV -->` |
| HERO | 메인 배너 + QHD 모니터 목업 | `<!-- HERO -->` |
| MARQUEE | 스크롤 텍스트 배너 | `<!-- MARQUEE -->` |
| PAIN | 고객 불만 포인트 섹션 | `<!-- PAIN -->` |
| COMPARISON | 비교표 (빌리지 말고 구매하세요) | `<!-- COMPARISON -->` |
| COST SIMULATOR | 연간 납부금액 비교 차트 | `<!-- COST SIMULATOR -->` |
| KEEPER MISSION | 키퍼의 약속 | `<!-- KEEPER MISSION -->` |
| TECH | 한화비전 기술력 | `<!-- TECH -->` |
| CTA / FOOTER | 상담 신청 + 하단 | `<!-- CTA -->` |

---

## 디자인 시스템

### 주요 CSS 변수
```css
--keeper-green: #FF7A1A      /* 메인 오렌지 */
--keeper-green-deep: #E05E00
--amber: #FFD747             /* 강조 노란색 */
--warning: #FF5252           /* 경고 빨간색 */
--bg-deepest: #0C0A07        /* 가장 어두운 배경 */
--text-cream: #F5F0E8        /* 기본 텍스트 */
```

### 폰트
- **Pretendard** — 본문, 제목 (한글)
- **Bebas Neue** — 숫자, 영문 강조
- **Archivo Black** — 로고, 브랜드명
- **JetBrains Mono** — CCTV 모니터 UI, 코드성 텍스트

---

## 주요 수정 이력

### 모바일 비교표 스크롤 버그 수정
**문제**: `@media(max-width:820px)`에서 3·4번 컬럼을 `display:none`으로 숨겨  
모바일에서 좌우 스크롤 시 ADT캡스·KT텔레캅 컬럼이 보이지 않음.

**수정 내용** (line ~600):
```css
/* 수정 전 */
@media(max-width:820px){
  .compare-header,.compare-row{grid-template-columns:1fr 1fr;}
  .compare-header .col:nth-child(3),.compare-header .col:nth-child(4),
  .compare-row .col:nth-child(3),.compare-row .col:nth-child(4){display:none;}
}

/* 수정 후 */
@media(max-width:820px){
  .compare-table{overflow-x:auto;-webkit-overflow-scrolling:touch;}
  .compare-header,.compare-row{min-width:680px;}
}
```

### 비용 시뮬레이터 리디자인
**변경**: 누적 금액 카드형 → 연간 납부금액 기반 그룹 막대 차트

**데이터 기준** (CCTV 4대 / 3년 의무약정):
| 연도 | 렌탈 (연간) | 키퍼 (연간) |
|------|------------|------------|
| 1년 | 78만원 | 48만원 |
| 2년 | 78만원 | 48만원 |
| 3년 | 78만원 | 48만원 |
| **4년** | **78만원** | **0원 ★** |
| **5년** | **78만원** | **0원 ★** |

- 렌탈 기준: 월 65,000원 × 12 = 78만원/년
- 키퍼 기준: 월 40,000원 × 12 = 48만원/년 (3년 후 완납)
- 3년 누적 절감: 약 40%, 5년 누적 절감: 약 63%

---

## 작업 시 주의사항

- `index.html` 단일 파일에 CSS·JS가 모두 인라인으로 포함되어 있음
- CSS는 `<style>` 태그 내, JS는 `</body>` 직전 `<script>` 태그 내
- `.reveal` 클래스는 IntersectionObserver로 스크롤 애니메이션 처리
- 막대 차트 애니메이션: `.ac-bar[data-h]` 요소의 `data-h` 값(px)을 뷰포트 진입 시 height에 적용
