# Brief_Intorudctions_for_iOS_concurrnecy

### Queues

[https://tngusmiso.tistory.com/49](https://tngusmiso.tistory.com/49)

```swift
DispatchQueue.main.sync { ... } // 사용 불가! --ERROR--

DispatchQueue.main.async { ... } // 비동기적으로 main 쓰레드에 보냄

DispatchQueue.main.asyncAfter(.now()+2) {... } // 지금으로부터 2초 뒤에 비동기적으로 main 쓰레드로 보냄

// 유저와 직접 인터렉티브 : UI관련 (즉시)
DispatchQueue.global(qos: .userInteractive)

// 반드시 필요, 비동기 처리 : 앱 내에서 첨부파일을 열기, 내부 데이터베이스 조회 등 (몇초)
DispatchQueue.global(qos: .userInitiated)	

// 일반적인 작업
DispatchQueue.global()	

// ProgressIndicator와 함께 길게 사용되는 작업 : 지속적인 데이터 feed, Networking (몇초~몇분)
DispatchQueue.global(qos: .utility)	

// 사용자가 직접적으로 인지하지 않는 부분 : 데이터베이스 유지 등 (속도보다는 에너지 효율성 중시)
DispatchQueue.global(qos: .background) // io throttled

// 사용하지 않음 legacy API
DispatchQueue.global(qos: .unspecified)

/*
DispatchQueue(label:)
DispatchQueue(label: , attributes: ) 
- 디폴트 : Serial (Concurrent로 설정 가능)
- QoS 설정 가능 : 하지만 사실 서비스 품질을 굳이 설정해주지 않아도, OS가 알아서 QoS를 추론하게 된다.
- label을 설정해주면 된다
*/

let queue = DispatchQueue(label: "mySerialQueue")

let queue2 = DispatchQueue(label: "myConcurrentQueue", attributes: .concurrent)
```

### Example of Usage

```swift
DispatchQueue.global(qos: .userInitiated).async {
	
	longRunningTimeConsumingTask()

	DispatchQueue.main.async {
		taleView.reloadData()
	}	

}
```
