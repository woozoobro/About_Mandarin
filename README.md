# About_Mandarin

### [App Store Page](https://apps.apple.com/kr/app/mandarin/id6466782712)

>## A SwiftUI project built with Firebase. UI/UX design was carried out alongside the development process.
___

## 📱 App Screenshots

### Home
| Main Pagination | Post Feed | Push Notification |
| :-----: | :-----: | :-----: |
| ![홈 스크롤](https://github.com/woozoobro/About_Mandarin/assets/99154211/c9f717a8-9f7c-43cc-a794-2eb5cc593982) | ![게시글](https://github.com/woozoobro/About_Mandarin/assets/99154211/cbc6c0db-9209-4a35-bb0c-7ac246e7e3fa) | ![푸시 알림](https://github.com/woozoobro/About_Mandarin/assets/99154211/3281b908-99b2-4e70-a0e1-081f318eaf5a) |

### Side Project Tab
| View Project | Post a Project | Contact Leader |
| :-----: | :-----: | :-----: |
| ![프로젝트 확인](https://github.com/woozoobro/About_Mandarin/assets/99154211/332523e3-93c8-46e4-9b83-7ed8b5b54478) | ![모집글 작성](https://github.com/woozoobro/About_Mandarin/assets/99154211/5934b83b-7f73-4755-9442-d0f3cea20999) | ![URL연락](https://github.com/woozoobro/About_Mandarin/assets/99154211/2a380b18-f268-469c-9675-b42fe4e889bd) |

### Profile

| SignIn | Profile Feature | Search |
| :-----: | :-----: | :-----: |
| ![프로필 로그인](https://github.com/woozoobro/About_Mandarin/assets/99154211/f4dd557b-2fff-4934-86e4-f45abfb4c74d) | ![프로필 기능](https://github.com/woozoobro/About_Mandarin/assets/99154211/31ecb10e-492c-4ab6-a247-104f2eb8a316) |![검색 기능](https://github.com/woozoobro/About_Mandarin/assets/99154211/0068e262-200b-4f06-96c1-d1c7248e3b66) |


## 🌟 Core Feature
- SwiftUI (iOS 16.0) + MVVM
- Firebase Authentication / SSO Login
- FCM + Cloud Functions Push Notification
- Modern Swift Concurrency (async-await)
- Pagination
- Image Cache
- Memory Trouble Shooting
- LPLinkPreview / NSKeydArchiver 
- Posts Search (Algolia)
- Custom SwiftUI Component
- WebKit + Framer

## 🏭 Architecture
![image](https://github.com/woozoobro/About_Mandarin/assets/99154211/a2c2c6ed-8ffe-4388-a352-0ec330e077e0)

After reading the article “Stop using MVVM for SwiftUI” on the Apple Developer Forum, I began questioning whether MVVM is truly suitable for SwiftUI.

To understand the pros and cons firsthand, I decided to implement MVVM in this project. I also considered the fact that MVVM is still widely used in real-world production.

Later, I explored The Composable Architecture (TCA) and built Apple’s Scrumdinger tutorial app. Through that, I realized that for smaller-scale projects, a simple Model–View architecture might be more than enough.

## Performance Optimization
After about 5 pages of views were rendered through pagination, abnormal hangs and hitches occurred. Navigation actions took over 1 second, so I profiled this using Instruments.
![Screenshot 2023-10-30 at 2 03 26 PM](https://github.com/woozoobro/About_Mandarin/assets/99154211/bf9780bd-20e3-48db-bc04-7d26ce0fceaf)

The issue was that when using the Binding initializer of ForEach, one of SwiftUI's default components, unexpected state changes caused the entire array to update.
I changed the data structure to avoid passing Binding, and the app's responsiveness improved from 1 second to 200ms.
When state management behaves differently than intended like this, I considered whether TCA is necessary and summarized this in a video.

https://www.youtube.com/watch?v=gs5Iw9TwTeU


## Firebase

I chose Firebase as an alternative to develop without collaborating with a backend developer. While considering whether to use Realtime Database or Firestore as the database, I decided that the current project didn't need real-time JSON data exchange, so I adopted Firestore as the database.
I applied a total of 4 Firebase SDKs to the current project.


1. FirebaseAuth

I configured an AuthenticationManager object that manages Auth using the singleton pattern, so ViewModels call AuthenticationManager when login is required.
Additionally, I configured SignInWithGoogle and SignInWithApple helper classes to manage authResult and made these classes into libraries that can be applied to any future project.
For DBUser information, which is user information stored in the database, I thought there should be only one instance within the app. I configured a UserStore object to store and manage this information and efficiently retrieve it, minimizing unnecessary networking.

2. FirebaseFirestore

Firestore has groups separated by Collections, documents within those Collections, and data stored as Key:Value pairs for fields and their values within these documents. I learned that NoSQL databases like this cannot use references for relationships.
As a trick, when documents in different collections need to reference each other, I configured the model to store the uid value of the required document and fetch the necessary information through another query based on that uid value.
I also configured Extensions for Queries to be used generically when composing Queries needed for Pagination or when adding SnapshotListeners.

3. FirebaseStorage

Similar to before, I configured a StorageManager object using the singleton pattern. One aspect I regretted not implementing during development was discovering that using Firebase's own Extension allows efficient compression when uploading data to Storage. However, judging that there wasn't enough physical time to implement this feature, I replaced it with a method of compressing images on the client side when uploading.

4. FirebaseMessaging

Push notification functionality for chat was needed. After research, I found that implementing this feature using FCM alone was difficult.
I learned that by deploying Firebase functions to Google Cloud connected to Firebase through node.js, it's possible to send notifications without a separate push server. After studying JavaScript and node.js for about a week, I implemented the push notification feature.
![Cloud Functions](https://github.com/woozoobro/About_Mandarin/assets/99154211/2928367f-cbab-4c70-94d5-4b285789640a)

## 🔫 Trouble Shooting (Memory Leak)
When posted content includes URLs, I implemented a feature to detect URLs from String values and provide link previews based on those URLs.
Since LPLinkPreview doesn't provide SwiftUI components, I implemented it by converting it to a SwiftUI View using UIViewRepresentable, but I discovered there was a memory leak.

![LinkPreview없을 때](https://github.com/woozoobro/About_Mandarin/assets/99154211/0a28dd4f-d3fd-4aef-839f-f997ddb7712b)
*Without link preview*

![LinkPreview있을 때](https://github.com/woozoobro/About_Mandarin/assets/99154211/4664096b-2fee-4ff1-b8bd-d0cd6137b350)
*With link preview*

I confirmed that Total bytes in the Heap area were accumulating abnormally and checked the memory graph. After refreshing the app about 10 times, I discovered that WebKit components were continuously accumulating. Further analysis revealed that memory leaks were occurring due to several complex reasons.
LPLinkView is included in WebKit, and when this View is created, it fetches Metadata through URL hosting. This Metadata is a reference type, and when the object should be deallocated when not visible on screen, it wasn't being deallocated, causing leaks.
Additionally, when refreshing data, instead of using the currently unallocated Metadata, it was hosting and fetching new Metadata.
While reading the official documentation, I hypothesized that caching Metadata might alleviate the symptoms.
The first approach I tried was caching using FileManager's cache folder. Although I failed to store Metadata, I learned that classes inheriting from NSObject require different caching methods.
So the second approach I tried was caching using NSKeydArchiver, successfully storing Metadata, and when I re-ran the memory trace, I resolved the abnormal memory leak.  

![메모리 해결](https://github.com/woozoobro/About_Mandarin/assets/99154211/8aba719e-3746-461f-a0c5-606a26449a86)*Memory issue resolved*

## 🔍 Posts Search Feature
I was showing a Twitter or Meta-like feed UI on the main screen. When search functionality for posted content was needed, I discovered through research that databases stored in Firestore cannot perform Full-Text Search through Query comparison operations. A separate database for managing search indexes was needed, and I considered the Algolia SDK, which is one of the methods recommended in the Firebase official documentation.
I applied this SDK because of its advantages in extensibility for future search suggestion features and categorization. I created a custom SwiftUI View with Search functionality to implement search engine-like Search features.

## ⚙️ Custom SwiftUI Component

[Tutorial I Created(Korean)](https://www.youtube.com/watch?v=cX26eGPCUxI&t=7s)
After Navigation View was deprecated, Navigation Stack newly appeared from iOS 16. The issue where Destination Views drawn in loops were pre-rendered, which was a problem with the previous Navigation View, was resolved, but there was another issue when using the current component as-is: the inability to navigate to different Destinations with the same Model.
I solved this problem using ViewBuilder pattern and Enum, and built a custom component structure that can be used whenever Navigation is needed in the future.
I also implemented popToRoot and scrollToTop features when necessary.
I learned that there are pros and cons when configuring objects that manage Navigation in apps like this.

Advantages: 
1. The navigationDestination modifier can be expressed in just one line throughout the entire project source code.
2. When push notifications arrive, users can navigate to the corresponding post through taps.
3. When implementing search functionality, the same Navigation processing is possible through asynchronous processing even for Models in different databases.

Disadvantages:
1. When using enums, readability decreases, potentially making collaboration difficult.
2. Bound models cannot be passed through enum associated values. When models distributed through Lists are updated, the entire List needs to be updated.

## Concluding Development
I learned that the best way to solve problems in iOS development is always Apple's official documentation! I discovered the keyword that Metadata should be cached, and that using the PhotosPicker component provided by SwiftUI no longer requires showing users alerts related to personal information and asking for permission, all through official documentation and WWDC sessions.
I also realized that referring to related WWDC sessions provides access to much more actionable information. When facing similar problems in the future, I think I should make good use of Apple's official documentation and WWDC sessions as I did this time.
