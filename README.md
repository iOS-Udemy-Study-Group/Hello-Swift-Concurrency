# Hello-Swift-Concurrency
Udemy Study for Swift Concurrency

## 스터디 방식

- Udemy 강의를 구매 후 개별 인증을 해야 스터디 참여 가능
- 각자 학습한 내용을 공용 study git repository에 공유하고 토의하면서 내용 보완
  - 개인적으로 공부한 내용을 개인폴더에 기록 (fork 후, 개인 스터디 내용 기록 했다가 공용 repository에 PR)
    - 각자 의무감을 갖고 스터디하기 위함
  - 매주 다수결로 스터디 날짜 지정 후, 2시간씩 스터디 진행
- 스터디 기록 정리 방식에 대한 좋은 의견 자유롭게 공유
- 스터디 중에 자유롭게 이해한 내용을 얘기할 수 있으며, 이해가 되지 않은 내용이 있다면 언제든지 공유




### 1주차 스터디 

- 3/26(일), 오후 1시 ~ 3시
- Section 1: Introduction ~ Async/Await Using Continuation
- 👩🏻‍💻 [applebuddy](https://github.com/applebuddy) | [AppleCEO](https://github.com/appleceo) | [ChoiYS](https://github.com/choi88andys) |  [Jae-eun](https://github.com/jae-eun) |  [JongHoooon](https://github.com/JongHoooon) | [Lim-YongKwan](https://github.com/lim-yongkwan)

<br>



## Concurrency

Concurrency란, 동시에 다수의 작업을 진행하는 것.

- serial하게 동작하는 Main thread에서 UI Event, Downloading Images task등을 모두 동작시킨다면?… 멈춤현상이 발생할 수 있음 => 매우 나쁜 user experience를 만들 수 있음

- 작업 특성에 맞게 각기 thread에서 동작하도록 할 수 있음 (ex) downloading images를 main thread 대신 background thread에서 동작)

- - DispatchQueue.global().async { let _ = try? Data(contentOf: imageURL }

- 이후 main thread에서 UI를 업데이트 시킬 수 있음

- - DispatchQueue.main.async { // update the ui }

- 이처럼 GCD를 이용해 상황에 따라 main, background thread에서 비동기로 작업을 수행할 수 있음



# GCD

DispatchQueue(Grand Central DispatchQueue)는 맞춤형 작업 실행을 위한 C-기반 메커니즘입니다. DispatchQueue는 순차적이거나 동시적으로 작업을 실행하지만 이때 항상 FIFO(First In First Out) 순서로 실행됩니다. SerialQueue는 순차적으로 작업이 진행되고, ConcurrentQueue는 동시적으로 작업이 진행됩니다. 그 외 GlobalQueue(QoS), CustomQueue(Serial, Concurrent), MainQueue(Serial)등을 통해 다양한 작업을 수행할 수 있습니다.



### DispatchQueue의 특징

- 쉽고 간결한 프로그래밍 인터페이스를 제공합니다.
- 자동적이고 전체적인 스레드 풀 관리기능을 제공합니다.
- 적절히 조율된 어셈블리어의 스피드를 제공합니다.
- 메모리 관리에 효율적입니다. (스레드 스택이 어플리케이션 메모리에 남지 않기 때문입니다.)
- 커널의 부하를 주지 않습니다.
- DispatchQueue를 통한 작업 비동기 전송은 대기열에 교착상태를 야기하지 않습니다. 



## Main Queue (serial queue)

- Main thread는 serial queue로 동작한다. 하나씩 순차적으로 작업이 진행 된다. 하나의 작업이 진행되는동안 다른 이벤트는 처리할 수가 없다.



## Global Queue (Concurrent)

- Global Queue는 QoS(Quality of Service)를 설정할 수 있다.
  - User Interactive
    - animation, event handling, updating user interface 등 사용자와 직업 상호작용하는 작업
    - 메인 스레드에서 처리하면 많은 로드가 걸릴 수 있는 작업들은 userInteractive에서 처리해서 바로 동작하는 것처럼 보이게 할 수 있음
  - User Initiated
    - 저장된 문서 열기 등 클릭 시 작업을 수행할 때 처럼 즉각적인 결과가 필요한 작업, 
    - userInteractive보다는 조금 오래걸릴 수 있지만 유저가 이를 인지하고 있음
  - Utility
    - 데이터 다운로드 처럼 보통 progress bar와 함께 길게 실행되는 작업
  - Background
    - 동기화 및 백업 처럼 유저가 직접적으로 인지할 필요성이 적은 작업
  - Default
    - 일반적인 작업
  - Unspecified
    - 명확히 지정된 QoS가 없음



### Creating a global Background Queue

~~~swift
DispatchQueue.global().async {
  // download the image
  
  // refresh the UI (background queue 에서 UI를 업데이트 하면 안됨)
}

DispatchQueue.global().async {
  // download the image
  DispatchQueue.main.async {
    // refresh the UI (UI 관련 작업은 Main thread에서 동작시켜야 함)
  }
}
~~~





### Creating a my Serial Queue

~~~swift
// Serial Queue는 Concurrent Queue와 달리 순차적으로 작업이 진행되므로 작업 순서가 보장된다.
let queue = DispatchQueue(label: "SerialQueue")

queue.async {
  // this task is executed first
}

queue.async {
  // this task is executed second
}
~~~



### Creating a my Concurrent Queue

~~~swift
// Concurrent Queue는 Serial Queue와 달리 작업 순서가 보장되지 않는다.
let queue = DispatchQueue(label: "ConcurrentQueue", attributes: .concurrent)

queue.async {
  // ...
}

queue.async {
  // ...
}

// Tasks will start in the order they are added but they can finish in any order (시작은 순서대로 진행되지만, 작업이 종료되는 순서는 보장되지 않음)
~~~



### Why is the Design Patterns important?

- Best practices, 실용적인 구조를 만들기 위한 노력

- Relationships between classes and objects, 클래스 등의 객체 간의 관계를 정의
- Speed up development, 개발 속도 향상
- Programming independent, 프로그래밍 독립성
- Flexible, reusable and maintainable, 융통적으로, 재사용가능하게, 유지보수가 더욱 쉽게 만들기 위해



## MVVM

- Model, View, ViewModel로 구성되는 디자인패턴 기법
- ViewModel이 비즈니스로직을 가져가게 되며 MVC의 Massive ViewController를 해결하고 Testability의 어려움을 해소할 수 있다.
- ViewModel이 주요 비즈니스로직을 갖고 있다. ViewModel의 변화를 View는 감지하고 그에 맞게 변화한다.
- View는 이벤트를 ViewModel에 전달하고, ViewModel은 이벤트에 맞는 비즈니스 로직을 수행한다.
- ViewModel에서는 Constant값이나 복잡해지는 비즈니스 모델 등은 Model로 분리하여 관리된다. (View, Model은 서로 직접적으로 소통할일이 없다.)
- why MVVM? : view로부터 들어오는 value에 대한 validation을 ViewModel에서 할 수 있다. ViewModel은 View 분리되어있기 때문에 View에 영향을 미치지 않고 ViewModel에 독립적인 테스트코드를 작성해서 테스트하기 용이하다.

#### MVVM에서의 Web API 동작

- View -> Web service -> API 요청을 하는 것은 가능은 하지만 결코 좋은 로직이 아니다.
- MVVM 패턴에서는 View - 이벤트 -> ViewModel -> Web service / Client -> API 요청을 할 수 있다.



## What is Continuation?

- continuation을 사용하면 기존의 callback closure가 있는 legacy 메서드를 그대로 유지하고 wrapping해서 외부에서 콜벡 결과에 따른 async await 처리를 할 수 있도록 도와준다.

- callback closure를 사용하거나 여러 사유로 변형하기 힘든 third-party, legacy 메서드를 wrapping해서 외부에서 async await 방식으로 처리하고자 할때 유용하게 사용할 수 있다.

- withCheckedContinuation 사용 예시

~~~ swift
func getPosts() async throws -> [Post] {
  // error를 throw할 일이 없으면 withCheckedContinuation을 사용
  return await withCheckedContinuation { continuation in
    // continuation을 활용하면 callback closure가 있는 getPosts 메서드를 외부에서는 async await 방식으로 처리할 수 있도록 할 수 있다.
		getPosts { posts in
			continuation.resume(returning: posts)
		}
	}
}
~~~
