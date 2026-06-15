생활비 구조대 V1.1.13 어드민 인식 보완 버전

이 버전은 정적 HTML 사이트에 Firebase Authentication + Cloud Firestore를 연결하는 버전입니다.
Netlify에는 index.html과 firebase-config.js를 같은 폴더에 올려야 합니다.

[설정 순서]
1. Firebase Console에서 새 프로젝트를 만듭니다.
2. Authentication → Sign-in method에서 Email/Password를 활성화합니다.
2-1. Google 로그인도 쓰려면 Google 제공업체를 누르고 사용 설정을 켠 뒤, 프로젝트 지원 이메일을 선택하고 저장합니다.
3. Firestore Database를 생성합니다. 처음 테스트할 때는 테스트 모드로 시작해도 되지만, 공개 배포 전에는 아래 보안 규칙으로 바꾸세요.
4. 프로젝트 설정 → 일반 → 내 앱 → 웹 앱 추가를 누르고 Firebase SDK 설정값을 복사합니다.
5. firebase-config.js 파일에는 Firebase 설정값이 이미 들어 있습니다.
6. Netlify에 life-budget-helper-v1.1.2-ready 폴더 전체를 다시 배포합니다.

[Firestore 보안 규칙 예시]
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read: if true;
      allow create, update: if request.auth != null && request.auth.uid == userId;

      match /dailyQuests/{date} {
        allow read, write: if request.auth != null && request.auth.uid == userId;
      }
    }
  }
}

[데이터 구조]
users/{uid}
- uid
- email
- nickname
- xp
- gems
- totalGems
- badges
- active
- createdAt
- updatedAt

users/{uid}/dailyQuests/{YYYY-MM-DD}
- date
- done
- updatedAt

[주의]
- Firebase 설정 전에는 로그인/회원가입이 작동하지 않습니다.
- 실제 이메일/비밀번호 또는 Google 로그인을 사용하는 기능이므로 보호자와 함께 계정/운영/개인정보 안내를 관리하세요.
- 결제, 광고, 정산 기능은 이 버전에 포함되어 있지 않습니다.

[V1.1.3 추가]
- 랭킹 섹션에 내가 모은 총 보석, 현재 보유 보석, 레벨, 장착 칭호 카드가 추가되었습니다.

[V1.1.4 추가]
- 로그인 후 닉네임 변경 버튼이 추가되었습니다.
- 닉네임 변경 비용은 첫 변경 무료, 2번째 100보석, 3번째 200보석처럼 100보석씩 증가합니다.
- 닉네임 변경 시 Firestore 사용자 문서와 Google 프로필 displayName 업데이트를 시도합니다.

[V1.1.5 추가]
- 회원가입/닉네임 변경 시 Firestore users 컬렉션에서 nicknameLower 중복을 확인합니다.
- 닉네임은 한글, 영어, 숫자, 밑줄(_)만 사용 가능하며 2~12글자입니다.
- Google 로그인으로 처음 가입할 때 이름이 중복되면 뒤에 숫자를 붙여 자동 생성합니다.

[V1.1.6 추가]
- 모바일 랭킹 줄에서도 닉네임 옆에 총 보석 수가 보이도록 수정했습니다.
- 랭킹 섹션에 “랭킹 초기화는 24시간 뒤” 문구를 추가했습니다.

[V1.1.7 추가]
- 랭킹을 오늘 00:00부터 다음날 00:00까지 획득한 보석 기준으로 표시합니다.
- 랭킹 화면에 오늘 랭킹 기간과 초기화까지 남은 시간을 표시합니다.
- 1등, 2등, 3등은 🥇🥈🥉 메달로 표시됩니다.
- 날짜가 바뀌면 이전 날짜 랭킹은 제외되고 오늘 랭킹만 표시됩니다.

[V1.1.8 추가]
- FAQ 항목을 4개에서 14개로 확장했습니다.
- 로그인, Google 로그인, 일일 랭킹, 보석, 닉네임 중복, 개인정보 관련 질문을 추가했습니다.

[V1.1.9 추가]
- 퀘스트를 한 번 클리어하면 해당 퀘스트는 1시간 뒤 다시 가능하도록 변경했습니다.
- 퀘스트 카드에 남은 쿨타임과 “1시간 뒤 가능” 문구가 표시됩니다.
- FAQ에 퀘스트 반복/쿨타임 설명을 추가했습니다.

[V1.1.10 추가]
- 퀘스트 쿨타임 남은 시간을 초 단위로 표시합니다.
- 다시 가능한 시각을 HH:MM:SS 형식으로 표시합니다.
- 쿨타임 진행률 바를 추가했습니다.

[V1.1.11 추가]
- 개발자 전용 관리자 패널을 추가했습니다.
- admins/{UID} 문서가 있는 계정만 관리자 패널을 볼 수 있습니다.
- 관리자 패널에서 유저 밴/밴 해제가 가능합니다.
- banned:true 유저는 퀘스트, 보석 획득, 닉네임 변경 기능이 차단됩니다.

[관리자 설정 방법]
1. Firebase Authentication → Users에서 본인 계정 UID를 복사합니다.
2. Firestore Database에서 admins 컬렉션을 만듭니다.
3. 문서 ID를 본인 UID로 만들고, role: "owner" 필드를 넣습니다.
4. 아래 규칙을 Firestore Rules에 적용하세요.

[Firestore Rules 예시]
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    function signedIn() {
      return request.auth != null;
    }
    function isAdmin() {
      return signedIn() && exists(/databases/$(database)/documents/admins/$(request.auth.uid));
    }
    function notBanned() {
      return signedIn() && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.banned != true;
    }

    match /admins/{adminId} {
      allow read: if signedIn() && request.auth.uid == adminId;
      allow write: if false;
    }

    match /users/{userId} {
      allow read: if true;
      allow create: if signedIn() && request.auth.uid == userId;
      allow update: if isAdmin() || (signedIn() && request.auth.uid == userId && notBanned());

      match /dailyQuests/{date} {
        allow read, write: if signedIn() && request.auth.uid == userId && notBanned();
      }
    }
  }
}

[V1.1.16 추가]
- 계정 생성 시 LB-XXXXXX 형식의 공개 계정 ID가 자동 발급됩니다.
- 내 프로필 창에서 계정 ID, 이메일, 레벨, 보석, 계정 상태를 볼 수 있습니다.
- 관리자 패널에서 영구 밴과 원하는 일수만큼 일시 정지를 할 수 있습니다.
- 정지된 유저는 정지 기간 동안 퀘스트, 보석 획득, 닉네임 변경 기능이 제한됩니다.

[V1.1.16 권장 Firestore Rules]
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    function signedIn() {
      return request.auth != null;
    }
    function isAdmin() {
      return signedIn() && exists(/databases/$(database)/documents/admins/$(request.auth.uid));
    }
    function userDoc() {
      return get(/databases/$(database)/documents/users/$(request.auth.uid)).data;
    }
    function notRestricted() {
      return signedIn()
        && userDoc().banned != true
        && (userDoc().suspendedUntil == null || userDoc().suspendedUntil <= request.time);
    }

    match /admins/{adminId} {
      allow read: if signedIn() && request.auth.uid == adminId;
      allow write: if false;
    }

    match /users/{userId} {
      allow read: if true;
      allow create: if signedIn() && request.auth.uid == userId;
      allow update: if isAdmin() || (signedIn() && request.auth.uid == userId && notRestricted());

      match /dailyQuests/{date} {
        allow read, write: if signedIn() && request.auth.uid == userId && notRestricted();
      }
    }
  }
}

[V1.1.13 추가]
- 개발자 UID(LD2yJDTgUNvnIDGh8HC3hGxyHdi1)를 코드에서도 직접 인식하도록 보완했습니다.
- admins 문서 확인이 늦거나 실패해도 해당 UID 계정은 관리자 메뉴가 표시됩니다.
- 프로필 창에 Firebase UID 표시를 추가했습니다.


V1.1.16: 관리자 메뉴 표시 보강. 개발자 Firebase UID와 Google 이메일 fallback을 추가했습니다. 실제 밴/정지 권한은 Firestore Rules와 admins 컬렉션이 최종적으로 막습니다.


V1.1.16: 관리자 메뉴 표시 문제 수정. Firebase UID/admins/email fallback에 더해 현재 닉네임이 웹개발자인 계정도 관리자 UI 표시 대상으로 보강했습니다. 실제 밴/정지는 Firestore Rules/admins 문서가 최종 권한을 보호합니다.


V1.1.16: 관리자 메뉴를 로그인 사용자에게 항상 표시하고, 실제 권한이 없으면 관리자 패널 안에서 차단 안내와 현재 UID를 보여줍니다. 밴/정지 기능은 isAdmin과 Firestore Rules/admins 문서가 보호합니다.
