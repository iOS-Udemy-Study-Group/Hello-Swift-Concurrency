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

### 2주차 스터디 

- 4/1(일), 오후 1시 ~ 3시
- Section 6: Async/Await Using Continuation ~ Section 9: Download RandomImages and Quotes
- 👩🏻‍💻 [applebuddy](https://github.com/applebuddy) | [ChoiYS](https://github.com/choi88andys) |  [Jae-eun](https://github.com/jae-eun) |  [JongHoooon](https://github.com/JongHoooon) | [Lim-YongKwan](https://github.com/lim-yongkwan)


### 3주차 스터디 (오프라인 😀)

- 4/9(일), 오후 1시 ~ 3시
- Section 9: Download RandomImages and Quotes ~ Section 12: What are Actors?
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



## Section 7: Project  Time: News App

News App 초기상태는 async await, continiuation 등의 Concurrency를 사용하지 않은 버전입니다. @escaping closure 등으로 콜백 이벤트를 처리할 수도 있지만, 콜백 지옥을 야기하거나, 콜백 클로져 실행 후 특정 분기 return을 놓치면 비정상 동작을 할 수 있는 단점이 있습니다.

이제 이 앱에 async/await, continuation, mainActor 등의 개념을 적용해 봅시다!

async/await, continuation, @MainActor 등의 개념들은 URLSession, Notification, HealthKit, CoreData 등 다양한 곳에서 활용 가능하다



## Section 8: Understanding Structured Concurrency in Swift

##### 👩🏻‍💻 learning point : Structured Concurrency, Async Let, Task Group, Unstructured Tasks, Detached Tasks, Task Cancellation

### async-let Tasks

~~~swift
// try await을 사용하였기에 equifaxUrl로부터 결과 값을 수신받을때까지 suspend 된다. ㅠㅠ equifaxUrl 요청이 끝나야 experianUrl로부터 요청을 수행한다..
  // => Concurrently하게 두개 다 요청하는 방법?
  // "Let's work on these two tasks(equifax, experian) concurrently!!"
  // => then, how do we do that?? => async let!
  // MARK: Async-let
  // - async let을 사용하면, async 작업에 대한 reference를 잡고 있는다. 즉시 반환되며, concurrent task로 동작하게 된다.
  // - async let을 붙였다면 뒤에 붙여 사용했던 try await은 명시하지 않아도 된다.(ex) 아래 코드의 URLSession 앞에 try await를 명시할 의무가 없음
  // * 아래 equifaxData, experianData는 모두 async let으로 정의된다.
  async let (equifaxData, _) = URLSession.shared.data(from: equifaxUrl)
  async let (experianData, _) = URLSession.shared.data(from: experianUrl)
  
  // custom code
  // async throws 메서드로부터 async let 상수를 받은 것이므로, 이를 사용할때는 try await을 사용해야 한다.
  // 아래와 같이 async let 값에 대한 await(try await)을 할때 비로소 suspend 된다! 따라서 async task는 동시에 동작시키고, 이후에 실제 값을 받는 부분에서 기다리는 것 => API 요청은 concurrently하게 하고, 받은 값을 feeding할때만 순차적으로 나눠줌.
  let equifaxCreditScore = try? JSONDecoder().decode(CreditScore.self, from: try await equifaxData)
  let experianCreditScore = try? JSONDecoder().decode(CreditScore.self, from: try await experianData)
~~~



### async-let Tasks in loop (언제 Concurrent하게, Serial하게 동작하는가)

~~~ swift
let ids = [1, 2, 3, 4, 5]
Task {
  for id in ids {
    // * 아래와 같이 loop문에서 async/await을 사용할 수 있는데 알아두어야 할 점
    // 1) loop 문이 한번 돌 때, getAPR 내의 async let task들이 concurrent 하게 수행된다.
    // 2) task는 concurrent 하게 동작하지만, 결국 feeding 단계에서 suspending이 된다.
    // 3) 두개의 task가 전부 끝나고, feeding까지 끝나면, 비로소 loop의 다음 getAPR를 수행한다. (결국 각 getAPR 메서드 내에서 await하는 라인이 있기 때문에 suspend하긴 함. API 요청이 concurrent 할 뿐.)
    // => loop를 사용한다고, 모든 getAPR 동작들이 concurrent하게 동작하는것이 아니라는 점을 알아야 한다. (task group을 활용하면 이 또한 concurrent 하게 동작은 가능 함.)
    // task group을 살펴 보기 전에 먼저 중요한 요소 중 하나인 cancelling a task 를 알아보자.
    let apr = try await getAPR(userId: id)
    print(apr)
  }
}
~~~



### Cancelling a Task, Task.checkCancellation()

~~~swift
let ids = [1, 2, 3, 4, 5]
var invalidIds: [Int] = []
Task {
  for id in ids {
    do {
      // Task.checkCancellation()을 사용하면, 에러가 throwing되어도 이후의 loop task를 멈추지 않고 지속 수행할 수 있다.
      try Task.checkCancellation()
      let apr = try await getAPR(userId: id)
      print(apr)
    } catch {
      print(error)
      invalidIds.append(id)
    }
  }
  
  // error가 발생한 id를 출력 => invalidIdList : 2 4
  print("invalidIdList : \(invalidIds.map { String($0) }.joined(separator: " "))")
}
~~~



### Group Tasks

##### - withTaskGroup, withThrowingTaskGroup (group.addTask { ... })

~~~swift
// MARK: 41. Group Tasks
// async let 을 loop문에서 사용하면 lopp 내 각각의 task 내에서 API 요청은 concurrent 하게 동작하지만 결국 feeding 과정에서 suspend 되고, 이를 기다리는 것을 알 수 있었다.
// => 루프 내 각각의 task를 모두 concurrent하게 동작하고 싶다면? => task groups를 사용하면 된다.

// getAPR은 각각 2개의 API 요청을 concurrent하게 진행함
// [Main Task] -> first Group (getAPR) -> two tasks concurrently
//             -> second Group (getAPR) -> two tasks concurrently
//             -> ..... (getAPR) -> two tasks concurrently

let ids = [1, 2, 3, 4, 5]
var invalidIds: [Int] = []
func getAPRForAllUsers(ids: [Int]) async throws -> [Int: Double] {
  var userAPR: [Int: Double] = [:]
  
  // 1) loop 내 작업들을 concurrent하게 동작하기 위해 for loop 바깥에 try await withThrowingTaskGroup을 사용할 수 있다.
  // - of: group에 추가할 task 결과 타입
  // - body: group task가 수행될 클로져를 정의
  try await withThrowingTaskGroup(of: (Int, Double).self, body: { group in
    for id in ids {
      // 2) group.addTask { ... } 내에 concurrently하게 동작시킬 작업을 정의, 결과는 위에서 정의한 (Int, Double) 튜플타입으로 반환
      group.addTask {
        // 해당 블럭에서는 task 블럭 밖의 값은 변경할 수 없다 getAPR의 결과를 튜플방식으로 group task로 추가한다.
        // loop가 one by one으로 동작이 되기 때문에 dataRacing을 발생할 걱정도 없다.
        // 여기의 작업은 loop 내 각각의 task 중 어떤게 가장 먼저 완료될 지 알 수 없어요. concurrent하게 동작하기 때문에!
        return (id, try await getAPR(userId: id))
      }
    }
    
    // 3) group에 추가된 task들을 async하게 차례대로 작업한다. 여기에서 loop 내부 각 task들은 순차적으로 동작하여 data racing 걱정 없다.
    for try await (id, apr) in group {
      // loop문에서 각 task 결과에 대한 addTask를 수행하ㅗ for try await loop에서 비로소 딕셔너리에 셋팅이 가능했다. (여기는 addTask 블럭 내부가 아니므로, 외부 값 변경이 가능
      userAPR[id] = apr
    }
  })

  return userAPR
}

Task {
  let userAPRs = try await getAPRForAllUsers(ids: ids)
  print(userAPRs)
}
~~~



### Additional Task Group Example

##### getting random images concurrently and asynchronously, and awaiting after that time.

~~~swift
// task group을 사용해서 loop 내의 각 image 요청을 모두 concurrent하게 수행하도록 해보자.
  func getRandomImages(ids: [Int]) async throws -> [RandomImage] {
    try await withThrowingTaskGroup(of: (Int, RandomImage).self, body: { group in
      for id in ids {
        // 루프내 각 task 각각 concurret task group을 만든다.
        // task group 내의 addTask 클로져 내부에 concurrently 동작시킬 작업을 작업하고, of: 레이블에 설정한 타입에 맞게 결과를 반환한다.
        group.addTask {
          let randomImage = try await self.getRandomImage(id: id)
          return (id, randomImage)
        }
      }
      
      // group에 추가했던 task들을 순차적으로 suspending하여 결과값을 randomImages에 appending한다.
      // => 모든 getRandomImage 요청들은 concurrently 동작을 한다. 이후 아래 for try await loop에서 suspending을 하며, 수신한 값을 순차적으로 randomImages에 추가한다.
      // => task group을 사용하지 않았을때 : loop의 각 task 내부 동작은 async하지만, 각 getRandomImage 결과값을 얻기 전까지 suspending되어 루프 다음 task(getRandomImage)를 동시에 수행하지 못했음.
      // => task group을 사용했을때 : loop의 각 task는 concurrently, asynchronous 하게 동작한다. suspending은 for try await loop에서 발생한다.
      for try await (_, randomImage) in group {
        self.randomImages.append(randomImage)
      }
    })
    
    return randomImages
  }
~~~

<br>

## Section 9: Project Time - Random Images and Random Quotes

아래와 같은 요소를 활용하여 randomImage API 요청을 concurrent하게 요청할 수 있다.

- ##### structured concurrency : async let

  - 다수의 API 요청을 concurrent하게 수행하고, feeding 단계(await 사용 위치)에서 suspend하여 순차적으로 feeding을 할 수 있다.

- ##### unstructured concurrency : Task { ... }

  - 가장 앞 단에서 await 동작을 수행하기 위해서는 Task 블럭 내부에서 요청한다. 혹은, View의 onAppear 이벤트 시 await 동작을 수행하고자 한다면, .task { ... } viewModifier를 사용할 수 있다.

- ##### task group : withThrowingTaskGroup, withTaskGroup (group.addTask)

  - 모든 loop의 Task(각각 random, quote API를 호출하는 task)들을 concurrent하게 동작시킬때 사용할 수 있다.
  - withThrowingTaskGroup, withTaskGroup 내에서 concurrent하게 동작해야할 코드를 작성 후(addTask), 그 다음 for await - in loop / for try await - in loop 문 내에서 요청 결과를 순차적으로 feeding 할 수 있다.

- ##### @MainActor

  - @MainActor를 지정한 영역은 항상 메인스레드에서의 동작이 보장되므로 내부에 추가적으로 DispatchQueue.main.async { ... }, MainActor.run { ... } 와 같은 thread 명시를 할 필요가 없다.

- ##### Section 9 예제 앱 동작 결과 (concurrent하게 random image, quote를 요청 및 수신 하여 UI 랜더링)

<div> 
  <img width=200 src="https://user-images.githubusercontent.com/4410021/197397459-fb6257d0-0b23-43e1-bb24-aee7dd6e8e43.gif">
</div>


<br>


## Sesion 10: AsyncSequence

- AsyncSequence Availability
  - iOS 13.0+

- Sequence protocol에 Async 성격이 추가된 것
- Sequence와 거의 동일하다. (for loop에 사용하고 makeIterator, next를 구현한다거나, operator 사용 등... 유사)
  - map, allSatisfy, max, prefix, compactMap, zip, flatMap, dropFirst, contains, filter, reduce 등 모두 사용 가능!
- related posting link : https://0urtrees.tistory.com/361



### AsyncSequence 가 아닌 Sequence와 async/await을 함께 사용할때 생기는 문제점

~~~swift
// MARK: - Section 10: AsyncSequence
// MARK: Loop Over Sequence Without AsyncSequence
// - AsyncSequence를 활용해보기에 앞서서 먼저 일반 Sequence를 사용해보자.

import SwiftUI
import PlaygroundSupport

extension URL {
  func allLines() async -> Lines {
    Lines(url: self)
  }
}

struct Lines: Sequence {
  
  let url: URL
  
  func makeIterator() -> some IteratorProtocol {
    let lines = (try? String(contentsOf: url))?.split(separator: "\n") ?? []
    return LinesIterator(lines: lines)
  }
}

// IteratorProtocol을 conform하기 위해서는 next() 메서드를 구현해야 한다.
struct LinesIterator: IteratorProtocol {
  typealias Element = String
  var lines: [String.SubSequence]
  
  // struct 내부 메서드에 멤버 변경이 있으므로 mutating을 명시한다.
  mutating func next() -> Element? {
    if lines.isEmpty {
      return nil
    }
    return String(lines.removeFirst())
  }
}

let endPointURL = URL(string: "https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.csv")!

Task {
  // Sequene에 대한 await 동작을 하고 있다. -> allLines()에서 모든 line들을 처리하고 난 이후에야 loop의 각 line이 출력된다.
  // 아래 라인은 endPointURL.allLines()가 먼저 실행되어 모든 동작이 완료되면 -> 그때서야 iterate 하게 된다.
  for line in await endPointURL.allLines() {
    // endPointURL.allLines() 작업이 끝나기 전까진 아래 라인은 시작도 못한다... allLines() 작업이 매우 크다면 매우 비효율적으로 처리 될 수 있다.
    print("line : \(line)")
  }
}

~~~



### Sequence + async/await 대신 AsyncSequene + for try await 을 사용해보자

- 앞서 Sequence를 사용했을때는 모든 lines의 작업을 처리한 뒤에 뒤늦게 iterator가 돌아갔다. 이는 big pause를 발생시킨다. 
- AsyncSequence + for try await을 사용하면 각각의 line task를 순회하면서 작업이 되기 때문에 big pause를 해결할 수 있다.  (각각의 iterator에 대한 작업이 async하게 동작, 하면 완료되는대로 그 다음 iterator에 대한 작업을 수행)

~~~swift
let endPointURL = URL(string: "https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.csv")!

// Task { ... } -> unstructured concurrency
Task {
  // endPointURL.lines는 AsyncLineSequence<URL.AsyncBytes> 타입
  // AsyncSequence를 사용하면 for await - in / for try await - in loop를 사용할 수 있다.
  // Sequence를 사용했을때에는 endPointURL.allLines()에 대한 모든 작업이 끝나고 나서야 순회를 했지만....
  // -> AsyncSequence와 for try await를 사용하면 big pauce할 필요없이 순회를 하며 각 line에 대한 try await 작업을 진행한다.
  for try await line in endPointURL.lines {
    print(line)
  }
}

/*
Task {
  // Sequene에 대한 await 동작을 하고 있다. -> allLines()에서 모든 line들을 처리하고 난 이후에야 loop의 각 line이 출력된다.
  // 아래 라인은 endPointURL.allLines()가 먼저 실행되어 모든 동작이 완료되면 -> 그때서야 iterate 하게 된다.
  for line in await endPointURL.allLines() {
    // endPointURL.allLines() 작업이 끝나기 전까진 아래 라인은 시작도 못한다... allLines() 작업이 매우 크다면 매우 비효율적으로 처리 될 수 있다.
    print("line : \(line)")
  }
}
*/

~~~



### Built-In-Functions using AsyncSequence

- There're a lot of built-in functions using AsyncSequence

~~~swift
// MARK: 55. Built-In AsyncSequences in iOS Framework
// - AsyncSequences를 사용하는 다양한 framework 내장 기능(Built-In-Functions)들이 있다.
// ex) 로컬파일, URL의 byte 읽을때, NotificationCenter로부터 특정 이벤트 처리할때 등...

import Foundation
import UIKit
import _Concurrency

// txt파일을 불러와서 line을 출력
let paths = Bundle.main.paths(forResourcesOfType: "txt", inDirectory: nil)
let fileHandle = FileHandle(forReadingAtPath: paths[0])
/*
Task {
  for try await line in fileHandle!.bytes {
    // print async bytes
    print(line)
  }
}
*/

// 이처럼 URL, local data에 대한 byte를 읽을때도 AsyncSequence를 활용할 수 있다.
let url = URL(string: "https://www.google.com")!
Task {
  let (bytes, _) = try await URLSession.shared.bytes(from: url)
  for try await byte in bytes {
    print(byte)
  }
}

Task {
  let center = NotificationCenter.default
  await center.notifications(named: UIApplication.didEnterBackgroundNotification).first {
    guard let key = $0.userInfo?["key"] as? String else { return false }
    return key == "SomeValue"
  }
}
~~~



### AsyncSequence를 알아서 만들어주는 AsyncStream, AsyncThrowingStream (Swift 5.7+)

- Concurrency에서는 AsyncStream, AsyncSequence를 준수하여 비동기 Iterator를 직접 구현하지 않고도 AsyncSequence를 쉽게 작성할 수 있습니다.
- AsyncStream의  Continuation에서  yield를 사용해서 데이터를  stream에 제공하거나, 더이상 데이터를 받지 못하는 경우,  finish를 호출합니다. 혹은 데이터 성공 여부를  yield.(with: .success()), yield.(with: .failure))로 전달할 수 있습니다.  failure로 전달할때는  AsyncThrowingStream을 사용하면 됩니다.
- withCheckedThrowingContinuation, withCheckedContinuation은 단순, async await method으로 바꾸기 어려운 애들을 래핑해서 async await 방식으로 사용하기 위한 기능이었다면,  AsyncStream은 연속적인 비동기 동작으로  for await, for try  await 방식으로 처리하기  쉽게 AsyncStream으로 변환 시켜주는 기능

- AsyncStream, for await (with AsyncStream<String>)사용 예시

~~~swift
import SwiftUI

func countdown() async {
  let counter = AsyncStream<String> { continuation in
    var countdown = 3
    Timer.scheduledTimer(
      withTimeInterval: 1.0,
      repeats: true
    ) { timer in
      guard countdown > 0 else {
        timer.invalidate()
        // .failure 이벤트도 전달하고 싶다면, AsyncStream 대신, AsyncThrowingStream을 사용해야함
        continuation.yield(with: .success("\(Date()) bye bye!"))
        // countdown이 모두 끝나면, continuation 종료
        return
      }
      
      // countdown 할때마다 yield로 AsyncStream에 제공할 값을 전달
      continuation.yield("\(Date()) countdown : \(countdown)")
      // Timer에 의해 1초마다 countdown이 1씩 감소, 0이되면 Timer 중지
      countdown -= 1
    }
  }
  // AsyncStream인 counter를 순회하며 await 작업을 진행하고 있다.
  // 만약 AsyncThrowingStream이었다면, for try await 로 작업이 되었을 것이다.
  for await count in counter {
    print(count)
  }
}

func runAsyncStreamTask() {
  Task {
    await countdown()
  }
}

~~~

- AsyncThrowingStream for try await (with AsyncThrowingStream<String, Error>) 사용 예시 

~~~swift
import SwiftUI

enum MyError: Error {
  case invalidCount
}

func countdown() async throws {
  let counter = AsyncThrowingStream<String, Error> { continuation in
    var countdown = 3
    Timer.scheduledTimer(
      withTimeInterval: 1.0,
      repeats: true
    ) { timer in
      guard countdown > 0 else {
        timer.invalidate()
        // .failure 이벤트도 전달하고 싶다면, AsyncStream 대신, AsyncThrowingStream을 사용해야함
        continuation.yield(with: .success("\(Date()) bye bye!"))
        // countdown이 모두 끝나면, continuation 종료
        return
      }
      
      // 특정 상황에 에러를 던지고 싶을때 .failure 이벤트를 보내면 AsyncSequence에 에러이벤트가 제공된다.
      if countdown == 1 {
        continuation.yield(with: .failure(MyError.invalidCount))
      }
      // countdown 할때마다 yield로 AsyncStream에 제공할 값을 전달
      continuation.yield("\(Date()) countdown : \(countdown)")
      // Timer에 의해 1초마다 countdown이 1씩 감소, 0이되면 Timer 중지
      countdown -= 1
    }
  }
  // AsyncThrowingStream인 counter를 순회하며 try await 작업을 진행하고 있다.
  for try await count in counter {
    print(count)
  }
}

func runAsyncThrowingStreamTask() {
  // 마지막 Task { ... } 블럭 사용 부에서는 메서드 반환부 앞에 async, async throws를 붙히지 않는다.
  Task {
    do {
      try await countdown()
    } catch {
      // error를 throw하지 않는 경우, 메서드에 throws 키워드 설정 안해도 됨
      print(error.localizedDescription)
    }
  }
}

runAsyncThrowingStreamTask()
~~~


- BitcoinPriceMonitor callback들을 AsyncStream으로 AsyncSeqence화해서 처리하기
  * TaskGroup, AsyncStream 모두 내부적으로 AsyncSequence임. 그래서 모두 for try await, for await loop에 
  - callback stream -> AsyncSequence로 바꾸어 사용할 수 있다.
    - 기존 Sequence를 채택한 애들이 사용가능한 다양한 연산자를 함께 활용 가능하다.
    - Async/Await하게 동시성 프로그래밍을 할 수 있다.

~~~swift
// MARK: 56. Adapting Existing Callbacks or Handlers to AsyncSequence Using AsyncStream

import UIKit

class BitcoinPriceMonitor {
  
  var price: Double = 0.0
  var timer: Timer?
  var priceHandler: (Double) -> Void = { _ in }
  
  // Timer 설정에 #selector로 지정되기 위해서는 @objc를 붙여서 Objective-C runtime에서 소통가능하도록 해주어야 합니다.
  @objc func getPrice() {
    priceHandler(Double.random(in: 20000...40000))
  }
  
  func startUpdating() {
    timer = Timer.scheduledTimer(timeInterval: 1.0, target: self, selector: #selector(getPrice), userInfo: nil, repeats: true)
  }
  
  func stopUpdating() {
    timer?.invalidate()
  }
}

/*
let bitcoinPriceMonitor = BitcoinPriceMonitor()
// 아래와 같은 타이머 값을 수신하는 클로져를 AsyncStream화해서 Async/Await 하게 사용할 수 있을까? -> 가능함.
bitcoinPriceMonitor.priceHandler = {
  print($0)
}

bitcoinPriceMonitor.startUpdating()
*/

let bitcoinPriceStream = AsyncStream(Double.self) { continuation in
  let bitcoinPriceMonitor = BitcoinPriceMonitor()
  bitcoinPriceMonitor.priceHandler = {
    // AsyncSequence에 제공될 값을 전달할때 yield를 사용
    continuation.yield($0)
  }
  // continuation을 통해 onTermination callback 클로져를 설정 가능
  // continuation.onTermination = { _ in }
  bitcoinPriceMonitor.startUpdating()
}

Task {
  // AsyncStream을 사용할때 장점
  // 1) 콜백 스트림들을 AsyncSequene로 변환해서 사용할 수 있는데 이때 기존 Sequence를 채택한 애들이 사용가능한 다양한 연산자를 함께 활용 가능하다.
  // 2) Async/Await하게 동시성 프로그래밍을 할 수 있다.
  for await bitcoinPrice in bitcoinPriceStream {
    print(bitcoinPrice)
  }
}
~~~

<br>

## Section 11. Concurrent Programming: Problem and Solutions

### 은행 계좌 출금이 동시적으로 요청될 때 생길 수 있는 문제점

- 동시적으로(using concurrent queue) 출금요청작업이 진행되면 잔고가 -(minus)가 될 수도 있음
  - serial queue, actor 등을 사용하여 해결 가능

~~~swift
// MARK: - Section 11. Concurrent Programming: Problem and Solutions
// MARK: 58. Problem: Bank Account Withdraw (은행 계좌 출금 문제)
// MARK: 59. Solutiion 1: Bank Account Withdraw Using Serial Queue
  
import UIKit

class BankAccount {
  var balance: Double
  
  init(balance: Double) {
    self.balance = balance
  }
  
  func withdraw(_ amount: Double) {
    if balance >= amount {
      let processingTime = UInt32.random(in: 0...3) // 처리 시간은 1 ~ 3초로 동작
      print("[withdraw] Processing for \(amount) \(processingTime) seconds")
      sleep(processingTime) // 3초 후 amount만큼 출금을 시도
      print("withdrawing \(amount) from account")
      balance -= amount
      print("Balance is \(balance)")
    }
  }
}

// Q. 만약 동시에 많은 요청이 오게 된다면??

let bankAccount = BankAccount(balance: 500)
let queue = DispatchQueue(label: "ConcurrentQueue", attributes: .concurrent)

queue.async {
  bankAccount.withdraw(300)
}

queue.async {
  bankAccount.withdraw(500)
}

// concurrent 하게 다수의 출금 요청을 실행 시 잔고가 -가 되는 상황이 발생!!
/*
 [withdraw] Processing for 300.0 2 seconds
 [withdraw] Processing for 500.0 3 seconds
 withdrawing 300.0 from account
 Balance is 200.0
 withdrawing 500.0 from account
 Balance is -300.0
*/
~~~



### 은행 출금문제 Solutions

- Actor (section 12에서 알아보자)
- NSLock 인스턴스의  lock(), unlock()을 통한 locking
- concurrent queue 대신 serial queue를 사용하기

~~~swift
// MARK: - Section 11. Concurrent Programming: Problem and Solutions
// MARK: 58. Problem: Bank Account Withdraw (은행 계좌 출금 문제)
// MARK: 59. Solution 1: Bank Account Withdraw Using Serial Queue
// MARK: 60. Solution 2: Bank Account Withdraw Using Locks(NSLock)
  
import UIKit

class BankAccount {
  var balance: Double
  let lock = NSLock()
  
  init(balance: Double) {
    self.balance = balance
  }
  
  func withdraw(_ amount: Double) {
    // NSLock을 통한 locking을 하면, 동시적 작업으로 인해 잔고가 minus가 되는것을 방지해준다. (하나의 스레드에 하나의 동작씩만 실행되도록 보장)
    // lock() 사용 시, 반드시 unlock()을 직접 지정해주어야 하는 단점이 있다. 만약 까먹고 unlock()을 처리하지 않으면, 해당 스레드의 다음 작업이 매우 지연될 수 있다.
    // -> 차선책으로 actor를 사용할 수 있다.
    lock.lock()
    if balance >= amount {
      let processingTime = UInt32.random(in: 0...3) // 처리 시간은 1 ~ 3초로 동작
      print("[withdraw] Processing for \(amount) \(processingTime) seconds")
      sleep(processingTime) // 3초 후 amount만큼 출금을 시도
      print("withdrawing \(amount) from account")
      balance -= amount
      print("Balance is \(balance)")
    }
    lock.unlock()
  }
}

// Q. 만약 동시에 많은 요청이 오게 된다면??

let bankAccount = BankAccount(balance: 500)
// 작업을 concurrent하게 하지않고, serial하게 동작하면, 동시에 작업이 수행되는 일이 없기에 잔고가 -가 발생하지는 않는다.
let queue = DispatchQueue(label: "Serial Queue")

queue.async {
  bankAccount.withdraw(300)
}

queue.async {
  bankAccount.withdraw(500)
}

// concurrent 하게 다수의 출금 요청을 실행 시 잔고가 -가 되는 상황이 발생!!
/*
 [withdraw] Processing for 300.0 2 seconds
 [withdraw] Processing for 500.0 3 seconds
 withdrawing 300.0 from account
 Balance is 200.0
 withdrawing 500.0 from account
 Balance is -300.0
*/

// serial 하게 실행하거나, NSLock()의 lock(), unlock()을 사용 시, 잔고가 -가 되는 문제는 해결이 됨.
// -> 또다른 해결방법 => Actor를 Section 12에서 알아보자!!
/*
 [withdraw] Processing for 300.0 2 seconds
 withdrawing 300.0 from account
 Balance is 200.0
 // 두번째 작업은 실행되지 않음. 출금이 불가능(출금할 amount가 잔고보다 큼)하기에
*/
~~~


<br>


## Section 12: What are Actors?



protect mutable state, accessing Actor isolated states,  MainActor, Nonisolated instances

- actor는 class와 유사하나, 상속이 불가능하다.
- 하나의 스레드에서만 동작하여 data racing 문제를 방지할  수 있다.
- 내부에 정의된 메서드는 await 키워드로 호출이 가능하며, 단기간에 다차례 반복 호출을 해도, 한번의 동작이 끝나야 그 다음 동작을 수행한다.

~~~swift
// MARK: - Section 12: What are Actors?
// MARK: 63. Understanding Actors

import SwiftUI

// 1) class로 사용한다면
/*
class Counter {
  var value: Int = 0
  
  func increment() -> Int {
    value += 1
    return value
  }
}
// => concurrently 동작 시, 출력 순서가 보장되지 않음
*/

/*
// 2) struct로 사용한다면
struct Counter {
  var value: Int = 0
  
  mutating func increment() -> Int {
    value += 1
    return value
  }
}
// => concurrently 동작 시, 출력 순서가 보장되지 않음
// 값 복사해서 호출할 경우, 1이 무수히 출력 됨..
*/

// 3) class, struct 대신 actor를 사용해보기
// actor는 단 하나의 스레드에서만 동작하도록 보장해준다. 따라서 data racing 문제가 해결된다.
actor Counter {
  var value: Int = 0
  // 하나의 스레드에서 한번에 하나의 동작만, 동작이 완료되면 suspended 다음 동작이 수행되므로 출력 순서가 보장
  // actor 내의 methods는 await를 붙혀서 호출, 두개 이상의 스레드에서 한번에 동작하지 않음
  func increment() -> Int {
    value += 1
    return value
  }
}

struct ContentView: View {
    var body: some View {
      Button {
        let counter = Counter()
        // 1) 만약 concurrent 하게 동시에 increment가 발생한다면?
        // 100까지 증가하면서 출력되는 것을 기대하고 아래코드를 실행한다면? => 카운팅 뒤죽박죽 순서로 출력이 됨... => concurrently 하게 동작하므로, 개별 작업들에 대한 시작은 순서대로 더라도, 완료되는 순서가 보장되지 않는다.
        DispatchQueue.concurrentPerform(iterations: 100) { _ in
          // 2) 아래처럼 struct상태 counter의 copy를 생성하고, increment()를 호출하면? -> 전부 값복사로 zero에서 시작하므로 1이 무수하게 출력됨.
          // var counter = counter // struct를 사용하는 경우
          // print(counter.increment())
          // 3) actor를 사용해보자.
          Task {
            // await, try await 등은 Task 블럭 내부, .task viewModifier 내부 등(unstructured concurrency)에서 사용해야한다.
            // => increment() 출력 결과, 순서가 보장된다!
            print(await counter.increment())
          }
        }
      } label: {
        Text("Increment")
      }
    }
}
~~~
