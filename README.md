# 면접고사 안내 페이지 설정 방법

## 1. Firebase 프로젝트 준비
1. https://console.firebase.google.com 에서 프로젝트 생성 (이미 있다면 생략)
2. 왼쪽 메뉴 **Firestore Database** → 데이터베이스 만들기 → "프로덕션 모드"로 시작
3. 왼쪽 메뉴 **Authentication** → Sign-in method → **이메일/비밀번호** 사용 설정
4. Authentication → Users 탭 → **사용자 추가**로 관리자 계정 1개 생성 (예: admin@hufs.ac.kr / 원하는 비밀번호)
5. 프로젝트 설정(톱니바퀴) → 일반 → "내 앱" → 웹 앱 추가(</> 아이콘) → 앱 등록 후 나오는 `firebaseConfig` 객체를 복사

## 2. index.html에 설정 값 입력
`index.html` 상단의 아래 부분을 복사한 값으로 교체하세요.

```js
const firebaseConfig = {
  apiKey: "여기에_API_KEY",
  authDomain: "여기에_PROJECT_ID.firebaseapp.com",
  projectId: "여기에_PROJECT_ID",
  storageBucket: "여기에_PROJECT_ID.appspot.com",
  messagingSenderId: "여기에_SENDER_ID",
  appId: "여기에_APP_ID"
};
```

## 3. Firestore 보안 규칙 설정 (중요 — 개인정보 보호)
Firestore Database → 규칙 탭에서 아래 내용으로 교체 후 게시하세요.

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /applicants/{examno} {
      allow get: if true;                 // 본인 수험번호로 개별 조회만 허용
      allow list: if request.auth != null; // 전체 명단 열람은 로그인한 관리자만
      allow write: if request.auth != null; // 등록/수정은 로그인한 관리자만
    }
    match /meta/{docId} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

이렇게 하면 지원자는 자기 수험번호로 개별 조회만 가능하고, 전체 지원자 명단(전화번호·이메일 포함)을 통째로 긁어가는 건 로그인하지 않은 사람에게는 막힙니다.

## 4. GitHub에 올리기
1. 기존 리포지토리(예: `limchangsuk-hub/2026lunch`)에 새 파일로 `index.html`(과 원하면 `README.md`)을 업로드
   - GitHub 웹에서 "Add file" → "Upload files"로 끌어다 놓으면 됩니다.
2. 저장소 Settings → Pages → Source를 `main` 브랜치, `/ (root)`로 설정 → Save
3. 잠시 후 `https://<계정명>.github.io/<저장소명>/` 주소로 접속하면 페이지가 보입니다.

## 5. 사용법
- **지원자**: 페이지 접속 → 수험번호 8자리 + 이름 입력 → 조회
- **관리자**: 우측 상단 "관리자" 클릭 → Firebase에서 만든 이메일/비밀번호로 로그인 → 엑셀 업로드 → 공통 안내문 작성 → "업로드한 데이터 저장" 클릭

## 엑셀 형식
첫 행은 헤더로 다음 이름을 정확히 사용하세요 (열 순서는 상관없음):
`이름, 수험번호, 지원학과, 전화번호, 이메일주소, 면접장소, 면접시간`
