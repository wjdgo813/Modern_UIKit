# UIKit: Sneak Peek, Part 1

**Brandon**

Apple의 WWDC 이벤트까지 불과 2주 남았습니다. 이 이벤트에서 Apple은 SwiftUI의 5주년을 기념하여 프레임워크의 놀라운 새로운 기능과 성능을 소개할 예정입니다.

그리고 우리는 UIKit에 대해 논의하며 작은 축하를 하고 싶습니다.

**Stephen**

오늘 우리는 조금 이상할 수 있는 시리즈를 시작하려고 합니다. 도메인에 사용되는 모델을 간결하게 만들어서 Modern UIKit 개발이 어떤 모습이 될지 보여드리겠습니다.

한가지 의문점이 있을 겁니다. SwiftUI가 대세인데, 왜 UIKit에 시간을 할애해야할까요?

**Brandon**

지금은 사람들이 100% UIKit으로 앱을 만드는 것이 점점 더 드물어지고 있습니다. 활발하게 개발되고 있는 대부분의 앱에는 SwiftUI가 포함되어 있으며, 곧 대부분의 앱은 대부분 SwiftUI와 UIKit 사용이 섞이게 될 것입니다.

하지만 개인적으로 100% SwiftUI로 사용되기까지는 오랜 시간이 걸릴 것으로 예상합니다. 아직까지도 UIKit에서 지원되는 강력한 것들이 SwiftUI에서는 지원하지 않기 때문입니다. 매년 SwiftUI는 점차 강력해지고 있으며 향상되고 있습니다. 하지만 UIKit도 동일한 이야기입니다. 따라서 SwiftUI가 UIKit을 앞지르는 순간을 예상할 수 있는 사람이 누가 있을까요. 

**Stephen**

따라서 개발하는데, UIKit으로 작업해야하는 순간은 분명히 찾아올 것이고 즉 우리는 UIKit 기능을 잘 활용할 수 있는 방법을 찾아야합니다. 일반적으로 기능은 복잡한 논리와 동작을 가지고 있으 API, DB, 위치 관리자 등과 같은 외부 시스템과 상호작용흘 하게 됩니다. 우리는 View와 Controller가 기능의 State 변경을 Observing 하고 그에 따라 UI를 업데이트 할 수 있는 방법을 연구해야합니다. 

그리고 놀랍게도 Swift의 Observing 도구들은 SwiftUI가 State를 관찰하는데 강력한 힘을 제공했지만, Modern UIKit 앱을 만드는 데 큰 도움을 주게 됩니다. 사실 SwiftUI 앱과 UIKit을 유사한 방식으로 앱을 빌드 할 수 있습니다. 물론 간단한 value로 view 계층을 구성하는 좋은 방법은 없지만 데이터 흐름과 탐색은 SwiftUI와 거의 동일한 방식으로 수행할 수 있습니다. 

**Brandon**

WWDC 시작하기 2주전에 "Modern UIKit" 시리즈를 시작하는 것은 위험할 수도 있습니다 ㅎㅎ
이런 논의는 WWDC 발표후에 legacy가 될 수 있기 때문이죠. 하지만 이번 시리즈는 WWDC의 내용과 별개로 강력한 내용이 될 것임을 확신합니다. 

우리는 UIKit API, SwiftUI API 사용하는 방법 혹은 Apple API를 사용하는 방식을 논의하려고 하는 것이 아닙니다. 우리는 앱을 구성하고 도메인을 모델링하는 방법의 진정한 본질을 파고들 것 입니다. 시리즈에서 다룰 내용은 특정 플랫폼을 넘어서 Windows, Linux 등 여러 플랫폼에서 적용할 수 있습니다. 

그럼 바로 시작해보도록 하죠! 

#### The Wi-Fi feature

몇년 전 Apple은 [Advances in Collection View Layout](https://developer.apple.com/videos/play/wwdc2019/215/) 세션에서 [sample code project](https://developer.apple.com/documentation/uikit/views_and_controls/collection_views/implementing_modern_collection_views) 프로젝트를 공개했습니다. 해당 세션의 데모에서는 다양한 종류의 collection view를 개발하는 방법을 제시합니다. 우리가 관심을 가지는 것은 "Diffable Data Source"섹션이고, "Settings: Wi-Fi"입니다. 

Apple은 Wifi 설정 화면의 축소 버전을 만들었습니다. 해당 프로젝트를 실행하면 network를 찾거나 유실 될 때마다 리스트를 실시간으로 업데이트 하는 것을 볼 수 있습니다. 또한 현재 선택된 네트워크는 상위로 노출시킵니다. 그리고 toggle 버튼을 통해 wifi를 off 한다면 전체 리스트가 멋진 애니메이션과 함께 접히는 것을 볼 수 있습니다. 

하지만 이 데모 프로젝트는 CollectionView의 기능을 보여주기 위함이므로 실제 iOS 설정의 기능과는 많이 다릅니다. 

실제 설정 기능은 직접 사용해보면 알겠지만 매우 복잡합니다. 동적으로 업데이트 되는 리스트가 있고, 토글 및 텍스트 필드와 같이 값에 바인딩해야하는 UI 컨트롤이 있으며 sheet, 드릴 다운등 탐색 유형등이 존재합니다. 이러한 종류들은 사실 SwiftUI로 구현하기 유리합니다. SwiftUI를 작업해보면 이 기능들을 구현하는 것이 얼마나 쉬운지 알 수 있습니다. 

하지만 우리는 UIKit으로 이것을 개발할 것이고 비교적 어려울수도 있습니다. 우리는 이번 에피소드를 통해 우리가 만들고 있는 툴킷을 사용할 것입니다. 

**Stephen**

이 툴킷은 SwiftUI에서 많은 영감을 받고 Swift의 Observing 관찰 도구를 사용하지만 UIKit의 정신에 충실한 스타일로 아주 복잡한 기능을 UIKit으로 개발해보도록 하겠습니다. 우리는 UIKit 패러다임에 알맞지 않은 도구는 만들지 않을 것입니다. 

**Brandon**

도메인 모델링을 최우선 과제로 삼아서 기능의 복잡성을 해소하고 UIKit, AppKit, SwiftUI와 같이 View 패러다임에 대한 기능을 동시에 빌드하는 것이 가능하며 Apple 플랫폼, Windows, Wasm 등의 여러 플랫폼에 기능을 동시에 출시하는 것이 가능하다는 것을 보여드리겠습니다! 

그럼 시작하죠!

### Domain modeling: network detail

우리는 iOS 세팅 화면과 동일한 기능을 구현할 것입니다. 

"WiFiSettings"라는 프로젝트를 새로 생성합니다.

작업을 시작할 때, 동시성 경고를 최대로 높여서 처음부터 작업을 안전하게 수행할 수 있도록 할 것입니다.
처음은 도메인 모델링에 대해서 집중적으로 이야기를 해보겠습니다. 우선 뷰에 대해서는 전혀 신경쓰지 않을 것입니다. 이유는 도메인이 View를 조금이라도 고려하게 되는 순간, 특정 패러다임과 특정 플랫폼에 국한되게 될 것 입니다. 우리는 안드로이드, 윈도우, 웹과 같은 새로운 플랫폼에 열려있도록 도메인을 모델링해야합니다. 

그리고 굳이 다른 플랫폼이 아니더라도 우리는 SwiftUI의 전환을 항상 생각해야합니다. 이상적으로는 새로운 플랫폼, 새로운 패러다임이 적용된다 하더라도 비지니스 로직을 다시 구현할 필요가 없도록 개발해야합니다. 

우리가 논의할 첫번째 도메인 모델링은 Collection에 표시할 수 있는 Network를 표현하는 것입니다. 이전에 보았듯이 Network는 이름, 보안 여부를 결정하는 Bool 그리고 연결성 측정으로 구성됩니다. 그리고 고유 식별자(UUID)도 추가합니다.

~~~swift
struct Network: Identifiable, Hashable {
  let id = UUID()
  var name = ""
  var isSecured = true
  var connectivity = 1.0
}
~~~

이제 앱에 핵심 도메인은 만들었습니다. 하지만 해당 모델만으로 도메인의 동작과 비지니스 로직을 정의하기는 어렵습니다. 또한 value 타입이기에 비지니스 로직과 동작을 갖기에는 적합한 형태는 아닙니다. 

어떠한 "동작"을 모델링하려면 참조 유형이 필요합니다. 
우선 간단하게 "네트워크 세부 정보 화면"을 기준으로 모델링을 진행해보겠습니다.

이 화면에서는 "이 네트워크 지우기"라는 옵션이 존재합니다. 해당 옵션을 tap하면 알림이 먼저 표시되는데요. 그 상태를 관찰 가능한 모델로 표현해보도록 하겠습니다.

~~~swift
import Observation

@MainActor
@Observable
class NetworkDetailModel {
  var forgetAlertIsPresented = false
  let onConfirmForget: () -> Void
  let network: Network
  
  init(
    network: Network,
    onConfirmForget: @escaping () -> Void
  ) {
    self.onConfirmForget = onConfirmForget
    self.network = network
  }
}
~~~

- forgetAlertIsPresented: "네트워크 지우기" 얼럿 노출 유무 Bool값 
- onConfirmForget: "네트워크 지우기" 얼럿에서 "확인"버튼을 눌렀을 때, 네트워크를 지우기 위한 실제 로직을 실행할 수 있도록 콜백 클로저를 정의합니다. 

그리고 UI에서 접근 가능하도록 엔드 포인트를 정의합니다.

~~~swift
extension NetworkDetailModel { 
  func forgetNetworkButtonTapped() {
  	forgetAlertIsPresented = true
	}

	func confirmForgetNetworkButtonTapped() {
  	onConfirmForget()
	}
}
~~~

세부 모델에서 필요한 내용은 위의 내용이 전부입니다. 물론 실제와 동일하게 기능 구현을 하기 위해서는 저데이터 모드, 자동 가입, DNS, 프로시 등 네트워크에 대한 모든 유형의 항목을 정의해야합니다. 

따라서 이러한 기능을 모두 구축하면 모델이 훨씬 더 복잡해질 것입니다.

### Domain modeling: connect and settings

앞서 말했듯이 뷰에 대한 생각은 전혀 하지 않고 도메인 모델링을 최우선 순위로 집중해야합니다. 이상적인 형태는 뷰가 Model의 의지에 따라야지 그 반대가 되어서는 안됩니다. 이렇게 하면 다양한 뷰 패러다임의 종류에 대해서 유연하게 대처할 수 있습니다.

**Stephen**

또한 우리는 앞으로 이 기능을 iOS와 Window에서 모두 실행하고 싶을 수도 있으며, UIKit 또는 SwiftUI 관련 개념을 도메인에 많이 집어넣게 된다면 Window와 같이 전혀 다른 플랫폼과 로직을 공유할 때 작업이 어려워질 것입니다. 

이제 조금 더 복잡한 기능으로 넘어가겠습니다. 바로 "연결" 기능입니다. 이 기능은 사용자가 연결하려는 네트워크의 비밀번호를 입력할 수 있습니다.

아래 새로운 모델을 추가합니다.

~~~swift
import Observation

@Observable
@MainActor
class ConnectToNetworkModel {
  var incorrectPasswordAlertIsPresented = false
  var isConnecting = false
  var onConnect: (Network) -> Void
  let network: Network
  var password = ""
  
  init(
    network: Network,
    onConnect: @escaping (Network) -> Void
  ) {
    self.onConnect = onConnect
    self.network = network
  }
  
  func joinButtonTapped() async {
	  isConnecting = true
	  defer { isConnecting = false }
	  try? await Task.sleep(for: .seconds(1))
  	if password == "blob" {
    	onConnect(network)
	  } else {
  	  incorrectPasswordAlertIsPresented = true
  }
}
~~~

- incorrectPasswordAlertIsPresented: 잘못된 비밀번호가 입력되었을 때, 노출을 결정하는 Bool 값입니다. 
- isConnecting: 현재 연결을 시도하고 있는지 추적하는 State입니다.
- onConnect: 연결이 성공적으로 이루어졌을 때, 부모에게 알리는 콜백 클로저입니다.
- password: 사용자가 입력한 비밀번호에 대한 상태입니다.
- func joinButtonTapped(): "네트워크 가입" 버튼을 탭 했을 때를 위한 엔드포인트이며 비밀번호가 단지 "blob" 으로 입력했을 때만 연결이 가능하도록 적용했습니다.

물론 앱의 실제 버전에서는 훨씬 더 복잡한 작업이 수행될 것입니다.

다시 한번 언급하자면 여전히 뷰에 대해 전혀 신경쓰지 않을 것입니다. 우리는 기능의 도메인 모델링에만 집중할 것입니다.

다음은 메인 화면이 될 것으로 보이는 네트워크 목록의 주요 기능입니다.

~~~swift
import Observation

@Observable
@MainActor
class WiFiSettingsModel {
  var foundNetworks: [Network]
  var isOn = true
  var selectedNetworkID: Network.ID?
  
  init(
    foundNetworks: [Network],
    isOn: Bool = true,
    selectedNetworkID: Network.ID? = nil
  ) {
    self.foundNetworks = foundNetworks
    self.isOn = isOn
    self.selectedNetworkID = selectedNetworkID
  }
}
~~~

- foundNetworks: 지금까지 발견된 모든 네트워크
- isOn: Wifi가 켜져있는지 여부 
- selectedNetworkID: 현재 선택한 네트워크 ID

이 모델에는 뷰에서 호출되고 사용자가 하는 일과 정확히 같은 이름을 가진 일부 엔드포인트도 있습니다. 예를 들어, 사용자는 목록에서 네트워크를 탭할 수 있습니다.

~~~swift
func networkTapped(_ network: Network) {
}
~~~

그런 다음 사용자는 네트워크에서 "정보" 버튼을 탭할 수 있습니다.

~~~swift
func infoButtonTapped(network: Network) {
}
~~~

위의 메소드를 두가지로 나눈 이유가 있는데요. 목록에서 항목을 탭하면 두 가지중 하나가 발생하게 됩니다. 선택한 네트워크를 탭하면 세부 정보로 넘어가지만 선택하지 않은 네트워크를 탭하면 연결을 요청하는 시트가 노출됩니다. View에 그 로직을 담당하지 않는 이유는 비지니스 로직으로 테스트할 수 없고 다른 플랫폼으로 적용한다면 해당 로직을 두 번 구현해야합니다. 그래서 모델에 명시적으로 메서드를 만들고 추가 로직 없이 뷰에서 해당 메서드를 호출하도록 할 것입니다. 

infoButtonTapped()부터 이야기를 해보겠습니다. 네트워크 목록에서 특정 네트워크의 "정보"버튼을 탭하면 해당 네트워크의 세부 기능까지 push할 계획을 해보겠습니다. 하지만 push 라는 개념도 플랫폼에 따라서 다르게 정의됩니다. iOS에서는 자연스럽지만, Windows에서는 세부 정보를 모달로 표시하고 웹에서는 새로운 웹 페이지로 정의합니다. 

최대한 유연하게 하려면 뷰 특정 개념이 모델에 침투하지 않도록 해야하며 세부 기능이 표시되는지 여부를 나타내는 상태를 유지해야합니다. 

~~~swift
var detail: NetworkDetailModel?
~~~

detail에 객체가 할당된다면 상세 화면으로 진입할 수 있게 됩니다.

그래서 info 버튼을 탭하면 우리는 detail 변수에 객체를 할당하면 됩니다.

~~~swift
func infoButtonTapped(network: Network) {
  self.detail = NetworkDetailModel(
    …
  )
}
~~~

상세 화면으로 넘어가기 위해 조금 더 디테일하게 정의하면 아래와 같습니다.

~~~swift
func infoButtonTapped(network: Network) {
  self.detail = NetworkDetailModel(
    network: network,
    onConfirmForget: { [weak self] in
			guard let self else { return }
			detail = nil
			selectedNetworkID = nil
    }
  )
}
~~~

이러한 작은 코드는 부모 기능에서 자식기능까지 복잡한 navigation 흐름을 추상적으로 설명할 수 있고, 또한 자식에서 부모까지의 통신 통로를 클로저와 같이 표현합니다. 설정 기능은 일부 상태를 채워 세부 기능으로 navigation 동작을 할 수 있으며 자식 기능은 클로저를 호출하여 부모와 통신할 수 있습니다. 이때 부모는 탐색 과정을 해제하기 위해 State들을 nil처리 하여 다른 navigation 이벤트를 수행할 수 있도록 합니다.

여전히 우리는 View를 고려하지 않고 모델 비지니스 로직만 고민하고 있습니다. 

networkTapped() 또한 비슷한 방식으로 구현할 수 있습니다. 이번에는 3가지의 경로가 존재합니다. 
첫째, 사용자가 이미 선택한 네트워크를 탭할 경우에는 세부 정보 화면으로 이동합니다. 

위와 동일하게 detail 변수에 객체를 할당하면 됩니다.

~~~swift
func networkTapped(network: Network) {
	if network.id == selectedNetworkID {
  self.detail = NetworkDetailModel(
    network: network,
    onConfirmForget: { [weak self] in
      guard let self else { return }
      detail = nil
      selectedNetworkID = nil
    }
  )
	}
}
~~~

추가로 네트워크가 안전할 경우 "네트워크 연결" 화면으로 이동할 것입니다.

~~~swift
var connect: ConnectToNetworkModel?
...

func networkTapped(network: Network) {
	...
	} else if network.isSecure { 
		connect = ConnectToNetworkModel(
    network: network,
    onConnect: { [weak self] network in
      guard let self else { return }
      detail = nil
      selectedNetworkID = network.id
    })
	}
}

~~~

동일하게 connect 변수를 통해 "네트워크 연결" 화면을 이동하도록 정의합니다.
모델을 구축하려면 network를 주입하고, wifi 연결이 성공적으로 이루어졌음이 확인되면 호출되는 클로저도 주입합니다.

마지막으로 네트워크가 안전하지 않은 경우 바로 네트워크에 연결할 수 있습니다.

~~~swift
} else {
  selectedNetworkID = network.id
}
~~~

우리는 뷰에 필요한 것이 무엇인지 생각하지 않고 추상적으로 도메인 모델링 연습을 수행하였습니다.

하지만 우린 도메인에 최적되지 않은 부분이 하나 존재합니다.

~~~swift
var connect: ConnectToNetworkModel?
var detail: NetworkDetailModel?
~~~

우리는 두 개의 다른 목적지로 이동할 수 있다는 것을 나타내기 위해 두 개의 Optional State를 선언했습니다. 하지만 이렇게 작성한다는 것은 connect, detail 모두 객체가 할당될 수 있다는 의미이며 동시에 화면 이동이 일어날 수 있다는 것을 의미합니다. 

따라서 이것은 부정확하게 모델링된 도메인입니다. 

이러한 변수가 많아질수록 화면의 이동이 가능한 경우의 수가 기하급수적으로 증가할 수 있습니다. 

이러한 이유로 화면 이동에 대해서는 열거형으로 묶는 것이 좋습니다.

~~~swift
enum Destination {
  case connect(ConnectToNetworkModel)
  case detail(NetworkDetailModel)
}
~~~

~~~swift
var destination: Destination?
~~~

그리고 Optional State로 정의하여 화면 이동에 대한 State를 하나로서만 유지합니다.

이제 우리는 한번에 하나의 목적지만 활성화할 수 있는 도메인을 갖게 되었습니다. 

## UIKit views: detail

우리는 이제부터 View를 구현할 겁니다. 하지만 진행하는 과정에서 Model은 단 한번도 건드리지 않을 것입니다. View에서 어떠한 문제가 생기더라도 도메인에 영향을 미치지 않을 것이기 때문입니다. View 의지를 만족시키기 위해 도메인을 변경하여 왜곡해서는 안됩니다. 오히려 View를 수정하여 도메인에 맞게 작업해야합니다. 

다양한 View 패러다임과 플랫폼에 사용할 수 있도록 이런 도메인 모델을 자체 SPM 패키지로 추출할 수도 있습니다. 

**Brandon**

많은 사람들이 UIKit을 사용하는 앱 아키텍처가 더 어렵다고 생각할 것입니다.
SwiftUI와 같은 좋은 State 관리 모델이 없기 떄문입니다.

하지만 Swift의 Observing 도구 덕분에 모델의 데이터를 짧고 간결하게 View에 바인딩이 가능합니다. SwiftUI를 연상시키는 방식으로 바인딩과 Navigation을 처리할 수도 있습니다.

그럼 시작해보도록 하겠습니다. 

