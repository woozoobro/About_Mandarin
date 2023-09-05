# About_Mandarin


>## Firebase를 활용한 SwiftUI 프로젝트 입니다. UI/UX 작업은 개발과 함께 진행 했습니다.

>개발 이외의 작업들이 궁금하다면!
| [기획](https://github.com/woozoobro/About_Mandarin/blob/main/기획%2C%20디자인/기획.md) | [디자인](https://github.com/woozoobro/About_Mandarin/blob/main/기획%2C%20디자인/디자인.md) | [마케팅](https://github.com/woozoobro/About_Mandarin/blob/main/기획%2C%20디자인/외부%20활동.md) |

___

## 📱 앱 스샷

### 홈
| 홈 Pagination | 게시글 | 푸시 알림 |
| :-----: | :-----: | :-----: |
| ![홈 스크롤](https://github.com/woozoobro/About_Mandarin/assets/99154211/c9f717a8-9f7c-43cc-a794-2eb5cc593982) | ![게시글](https://github.com/woozoobro/About_Mandarin/assets/99154211/cbc6c0db-9209-4a35-bb0c-7ac246e7e3fa) | ![푸시 알림](https://github.com/woozoobro/About_Mandarin/assets/99154211/3281b908-99b2-4e70-a0e1-081f318eaf5a) |

### 프로젝트
| 프로젝트 확인 | 프로젝트 게시 | 리더에게 연락 |
| :-----: | :-----: | :-----: |
| ![프로젝트 확인](https://github.com/woozoobro/About_Mandarin/assets/99154211/332523e3-93c8-46e4-9b83-7ed8b5b54478) | ![모집글 작성](https://github.com/woozoobro/About_Mandarin/assets/99154211/5934b83b-7f73-4755-9442-d0f3cea20999) | ![URL연락](https://github.com/woozoobro/About_Mandarin/assets/99154211/2a380b18-f268-469c-9675-b42fe4e889bd) |

### 프로필

| 프로필 로그인 | 프로필 기능 | 검색 기능 |
| :-----: | :-----: | :-----: |
| ![프로필 로그인](https://github.com/woozoobro/About_Mandarin/assets/99154211/f4dd557b-2fff-4934-86e4-f45abfb4c74d) | ![프로필 기능](https://github.com/woozoobro/About_Mandarin/assets/99154211/31ecb10e-492c-4ab6-a247-104f2eb8a316) |![검색 기능](https://github.com/woozoobro/About_Mandarin/assets/99154211/0068e262-200b-4f06-96c1-d1c7248e3b66) |


## 🌟 핵심 키워드

- SwiftUI (iOS 16.0) + MVVM
- Firebase Authentication / SSO 로그인
- FCM + Cloud Functions 푸시 알림 기능
- Modern Swift Concurrency (async- await)
- Pagination
- Image Cache
- Memory Trouble Shooting
- LPLinkPreview / NSKeydArchiver 
- Posts Search (Algolia)
- Custom SwiftUI Component

## 🏭 구조
![MVVM](https://github.com/woozoobro/About_Mandarin/assets/99154211/f8bf5b9c-8573-416c-90b7-2f7022d47fb8)

Apple Developer Forum에 기고된 “Stop using MVVM for SwiftUI” 글을 읽은 뒤
SwiftUI에 맞는 아키텍쳐가 정말 MVVM이 맞는지 고민을 해보게 되었습니다.

직접 체감을 해봐야 왜 불필요한지 판단을 할 수 있다는 생각이 들어 현재의 프로젝트엔
MVVM을 적용해서 개발을 진행하게 되었습니다. 또한 현업에서 여전히 MVVM 구조를
많이 사용하게 된다는 점도 고려했습니다.

이 후 MVVM의 대안으로 TCA를 사용한다는 걸 알게 되었습니다.
그리고 Apple의 SwiftUI Tutorial인 Scrumdinger 어플을 제작해보며 
현재 같이 작은 규모에서 구조를 설계하게 된다면 Model - View 구조로도 
충분하겠다는 생각을 해보게 되었습니다.

## Firebase

백엔드 개발자와의 협업 없이 개발을 진행할 수 있는 대안으로 Firebase를 사용하게 되었습니다.
Realtime Database와 Firestore 중에 어떤 것을 DB로 사용할 지 고민하다 현재의 프로젝트엔
JSON 데이터를 실시간으로 주고 받을 필요가 없다는 판단을 하게 되어 Firestore를 DB로 채택하게 되었습니다.

현재의 프로젝트에 총 4개의 Firebase SDK를 적용했습니다.  


1. FirebaseAuth

싱글톤 패턴으로 Auth를 관리해주는 AuthenticationManager 객체를 구성해 ViewModel에서
로그인이 필요한 경우 AuthenticationManager를 호출하게 구성했습니다.

추가적으로 SignInWithGoogle, SignInWithApple 헬퍼 클래스를 구성해 authResult를 관리하고,
해당 클래스들을 추후에 어떤 프로젝트에서든 적용할 수 있게 라이브러리화를 진행 했습니다.

DB에 저장되는 유저 정보인 DBUser 정보의 경우 앱 내에 단 하나만 존재해야 한다고 생각이 들었습니다. 
해당 정보를 저장, 관리해주고 정보를 효율적으로 가져올 수 있는 UserStore 객체를 구성해 
불필요한 Networking을 최소화 했습니다.

2. FirebaseFirestore

Firestore는 Collection으로 구분되는 그룹이 있고, 해당 Collection 안에 document형태의 문서들,
그리고 이 문서들 안의 필드와 해당 필드에 대한 값을 Key : Value 형태로 데이터를 저장하게 됩니다.
지금과 같은 NoSQL DB의 경우 Reference를 통한 참조가 불가능하다는 걸 알게 되었습니다.

한 가지 Trick으로 서로 다른 collection에 있는 문서들이 서로 참조가 필요한 경우 
필요한 document의 uid값을 저장해, 해당 uid 값을 바탕으로 다시 한번 query를 통해 필요한 정보를
가져올 수 있게 model을 구성했습니다.

그리고 Pagination에 필요한 Query를 구성하거나 SnapshotListener를 추가할 수 있는 
Query가 필요한 경우 Generic하게 사용할 수 있도록 Query에 대한 Extension을 구성해줬습니다.

3. FirebaseStorage

이전과 마찬가지로 싱글톤 패턴으로 StorageManager 객체를 구성했습니다.
개발하며 한 가지 적용하지 못해 아쉬웠던 부분은 Firebase 자체 Extension을 사용하면
Storage에 데이터를 업로드할 시 효율적으로 압축이 가능하다는 걸 발견했지만 해당 기능을 구현을 하기엔
물리적인 시간이 부족하다는 판단에 클라이언트쪽에서 이미지를 업로드 할 시에 압축을 진행하는 방법으로
대체하게 되었습니다.

4. FirebaseMessaging

채팅에 대한 푸시 알림 기능의 구현이 필요해, 조사해본 결과 FCM만 사용해선
해당 기능의 구현이 어렵다는 걸 알게 되었습니다.

node.js를 통해 Firebase function을 Firebase와 연결된 google cloud에 deploy하면
별도의 푸시 서버 없이 알림을 보내는 게 가능하다는 걸 알게 되어 일주일 정도 javascript와 node.js를
학습한 후 푸시 알림 기능을 구현하게 되었습니다.
![Cloud Functions](https://github.com/woozoobro/About_Mandarin/assets/99154211/2928367f-cbab-4c70-94d5-4b285789640a)

## 🔫 Trouble Shooting (메모리 성능 이슈)
포스팅 된 콘텐츠에 URL이 포함되어 있는 경우 String 값에서 URL을 detect, 해당 URL을 바탕으로
링크에 대한 Preview기능을 구현하게 되었습니다.

LPLinkPreview의 경우 SwiftUI로 컴포넌트가 제공되고 있지 않아 UIViewRepresentable을 사용해
SwiftUI View로 변환하여 구현했는데 이 때 메모리 누수가 있다는 걸 알게 되었습니다.

![LinkPreview없을 때](https://github.com/woozoobro/About_Mandarin/assets/99154211/0a28dd4f-d3fd-4aef-839f-f997ddb7712b)
*링크 프리뷰가 없을 경우*

![LinkPreview있을 때](https://github.com/woozoobro/About_Mandarin/assets/99154211/4664096b-2fee-4ff1-b8bd-d0cd6137b350)
*링크 프리뷰가 있을 경우*

Heap영역의 Total bytes가 비정상적으로 누적이 된다는 걸 확인하고 메모리 그래프를 체크해봤습니다. 앱의 새로고침을 10번 정도 진행했을 때 WebKit 컴포넌트가 지속적으로 쌓이고 있다는 걸 발견하게 되었습니다.
조금 더 분석해본 결과 여러 가지 복합적인 이유로 현재 메모리 누수가 발생한다는 걸 알게 되었습니다.

LPLinkView는 WebKit에 포함되어 있고, 해당 View가 생성될 때 url 호스팅을 통해 Metadata를 가져오는데 이 때 가져오는 Metadata가 reference 타입이고 해당 객체가 Screen에서 보이지 않을 때
할당 해제가 되어야 하는데 해제 되지않고 누수가 발생했습니다.

또한 데이터를 새로 고침하게 되면 현재 할당해제가 되지 않은 Metadata를 사용하는게 아니라 새로운 Metadata를 호스팅해서 가져오게 되는 게 원인이었습니다.

공식 문서를 읽던 중 Metadata에 대한 캐싱을 하게 되면 증상이 완화되지 않을까 추론하게 되었습니다.

첫 번째로 시도했던 건 FileManager의 캐싱폴더를 사용한 캐싱이었습니다.
Metadata를 저장하는 것에 실패를 했지만 NSObject를 상속받은 class는 다른 방법으로 캐싱이 필요하다는 걸 알게 되었습니다.

그래서 두 번째로 시도했던 NSKeydArchiver를 사용한 캐싱으로 Metadata의 저장을 성공하고메모리 Trace를 재진행 했을 때 비정상적인 메모리 누수를 해결하게 되었습니다.  

![메모리 해결](https://github.com/woozoobro/About_Mandarin/assets/99154211/8aba719e-3746-461f-a0c5-606a26449a86)*메모리 이슈 해결*

## 🔍 Posts 검색 기능
트위터 혹은 메타와 유사한 피드의 UI를 메인 화면에서 보여주고 있었습니다.
게시되는 콘텐츠에 대한 검색 기능이 필요해 조사한 결과 Firestore로 저장된 DB는 Query를 통한 비교 연산으로
Full - Text Search가 불가능하다는 걸 알게 되었습니다. 따로 검색을 위한 Index를 관리하는 DB가 필요했고
Firebase 공식 문서에서 추천하는 방법 중 Algolia SDK를 검토하게 되었습니다.

추후 검색어 추천 기능과 카테고라이징을 위한 확장성이 용이하다는 장점 때문에 해당 SDK를 적용했습니다.
Search 기능이 있는 Custom한 SwiftUI View를 만들어 검색 엔진과 유사한 Search 기능을 구현했습니다.

## ⚙️ Custom SwiftUI Component

[제작한 튜토리얼](https://www.youtube.com/watch?v=cX26eGPCUxI&t=7s)
Navigation View가 Deprecated된 이후 iOS 16부터 Navigation Stack이 새롭게 등장했습니다.
이전의 Navigation View가 가지고 있던 문제점인 반복문에서 그려지는 Destination View가 미리
렌더링 된다는 이슈가 해결 되었지만 현재의 컴포넌트를 그대로 사용하게 될 때 또 다른 이슈가 있었습니다.
같은 Model을 가지고 서로 다른 Destination으로 갈 수 없다는 점이었습니다.

ViewBuilder 패턴과 Enum을 활용하여 이 문제를 해결하고, 추후에 Navigation이 필요한 경우 언제든 
사용할 수 있게 Custom한 컴포넌트 구조를 구축했습니다.

필요한 경우 popToRoot 기능과 scrollToTop 기능 또한 구현했습니다.

현재처럼 앱에서 Navigation을 관리하는 객체를 구성하게 될 때
장단점이 있다는 걸 알게 됐습니다.

장점: 
1. navigationDestination modifier를  프로젝트의 전체 소스 코드에서 단 한 줄로 표현이 가능합니다.
2. 푸시 알림이 온 경우 유저가 탭을 통해 해당 게시물로 Navigation을  가능케 합니다.
3. 검색 기능 구현 시에도 다른 DB에 있는 Model이지만 비동기 처리를  통해 같은 Navigation 처리가 가능합니다.

단점:
1. enum을 사용하게 될 때 가독성이 떨어져 협업에서 용이하지 못할 가능성이 있다는 걸 발견하게 되었습니다.
2. Binding된 모델을 enum의 연관값을 통해 전달이 불가능합니다.  List를 통해 뿌려진 모델이 업데이트 될경우 해당 List 전체의 업데이트가 필요하게 됩니다.

## 개발을 마무리하며
iOS 개발에 있어 어떤 문제가 생겼을 때 해결할 수 있는 가장 좋은 방법은 항상!!!
애플의 공식 문서라는 사실을 알게 되었습니다. Metadata를 캐싱해야 한다는 Keyword를 알게 된 것도,SwiftUI에서 제공되는 PhotosPicker 컴포넌트를 사용하게 되면 더 이상 유저에게 개인정보와 관련된 Alert을 띄우고 유저의 허락을 구하지 않는다는 것도 공식문서와 WWDC 세션을 통해 알게 되었습니다. 

그리고 관련된 WWDC 세션을 참고하면 더 활용가능한 방안이 많은 정보들을 습득할 수 있다는 것을 깨닫게 되어 이후에 비슷한 문제를 겪을 경우 지금과 같이 Apple 공식 문서와 WWDC 세션을 잘 활용해야겠다는 생각이 들었습니다.
