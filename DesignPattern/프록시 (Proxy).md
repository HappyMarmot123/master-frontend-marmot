# 프록시 (Proxy)

## 개요

프록시(Proxy) 패턴은 **어떤 다른 객체에 대한 접근을 제어하기 위해 그 객체의 대리인(surrogate) 또는 자리표시자(placeholder) 역할을 하는 객체**입니다. 🕵️‍♂️ 실제 객체와 동일한 인터페이스를 가지지만, 실제 객체의 로직이 실행되기 전후에 추가적인 작업을 수행할 수 있도록 합니다.

### 프록시 패턴의 목적과 작동 방식

이 패턴의 주요 목적은 다음과 같습니다.

- **접근 제어** : 특정 조건(권한, 캐시, 로딩 상태 등)에 따라 실제 객체의 메서드 호출을 허용하거나 거부합니다.
- **성능 최적화** : 무거운 객체의 생성을 지연시키거나(Lazy Loading), 반복적인 호출 결과를 캐싱하여 성능을 향상시킵니다.
- **추가 기능 삽입** : 실제 객체의 핵심 로직을 건드리지 않고, 로깅, 모니터링, 유효성 검사 등 부가적인 기능을 추가합니다.

프록시 패턴의 핵심은 **클라이언트가 프록시를 실제 객체로 착각하고 사용하도록 만드는 것**입니다. 클라이언트는 프록시 객체의 메서드를 호출하지만, 그 요청은 프록시를 거쳐 실제 객체로 전달됩니다.

리액트의 특성상 **컴포넌트 중심 아키텍처**와 **선언적(Declarative) 방식** 때문에 클래스 기반의 프록시 패턴이 전통적인 의미로는 잘 사용되지 않습니다. 단, 고차 컴포넌트(HOC)나 커스텀 훅(Custom Hook)으로 바꿔 사용합니다.

## JavaScript 예제

```javascript
const withAuthentication = (WrappedComponent) => {
  return function AuthenticatedComponent(props) {
    const { isLoggedIn } = useAuth(); // 인증 상태를 가져오는 훅 (외부 상태)

    if (!isLoggedIn) {
      // 로그인이 되어 있지 않으면 로그인 페이지로 리디렉션하거나 메시지를 표시합니다.
      return <p>로그인이 필요합니다.</p>;
    }
    return <WrappedComponent {...props} />;
  };
};

export default withAuthentication;

// `App.js` - 클라이언트 코드
import AdminDashboard from "./AdminDashboard";
import withAuthentication from "./withAuthentication";

// 프록시를 통해 컴포넌트를 감쌉니다.
const AuthenticatedDashboard = withAuthentication(AdminDashboard);

function App() {
  return (
    <div>
      <h1>관리자 페이지</h1>
      <AuthenticatedDashboard />
    </div>
  );
}
```
