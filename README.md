# frontend1

## 개요
Vue 3 + Vite 기반의 프론트엔드 예제 프로젝트입니다. 컴포넌트 기반 구조와 타입스크립트, 모던 프론트엔드 개발 환경을 제공합니다.

## 폴더 구조

```
frontend1/
├── public/                       # 정적 파일
├── src/
│   ├── assets/                   # 이미지, 폰트 등 에셋
│   ├── components/               # 공통 컴포넌트
│   ├── pages/                    # 라우트별 페이지 컴포넌트
│   ├── plugins/                  # 플러그인(i18n, vuetify 등)
│   ├── router/                   # 라우터 설정
│   ├── styles/                   # 전역 스타일 및 SCSS
│   ├── App.vue                   # 루트 컴포넌트
│   └── main.ts                   # 진입 파일
├── index.html                    # HTML 템플릿
├── package.json                  # 프로젝트 설정 및 의존성
├── tsconfig*.json                # 타입스크립트 설정
├── vite.config.mts               # Vite 설정
└── README.md                     # 프로젝트 설명
```

## 실행 방법
1. `npm install` 또는 `npm install`로 의존성 설치
2. `npm run hello` 또는 `npm run dev`로 개발 서버 실행
3. 환경 설정은 `src/plugins/`, `vite.config.mts` 등에서 확인