# Optional
Java8에서부터 추가된 Wrapper 클래스. Null 관리가 무척 용이해졌다  
는데 이해가 되지 않다가 왜 써야 하고 언제 쓰면 좋은지 정리한 생각 

## vs if(null)
```java
if(x == null) thr MyCustomException();
```
과
```java
x.orElseThrow(() -> new MyCustomException());
```
의 차이에 대해 고민해보자  
전자는 java8 이전(아마)의 NPE 관리방법이고 후자가 Optional 등장 이후의 NPE 관리일 것이다.

Optional의 필요성을 증명하려면 전자가 별로고 후자가 낫다는 것을 증명해야 할 것이다.

만약
1. x라는 인스턴스가 라이프 사이클이 속한 영역에 한정된 객체이거나
2. 개발자 아무개가 x가 Null일 가능성이 있는, 즉 Nullable한 객체라는 것을 '절.대.로' 까먹지 않는다면
Optional을 굳이 써야할까 싶다.
오히려 불필요한 boxing, unboxing 리소스가 들어간다고 생각한다.

### 개발자는 어제 자기가 짠 코드도 기억을 못한다
는게 내 강한 믿음 중 하나이다.  
즉, 개인 프로젝트여도 협업을 하듯 코딩을 해야한다는 것인데,  

특정 인스턴스가 여러 객체나 메서드를 넘어다니는, 짧지 않은 생명 주기를 가지고 있다면  
또 그 인스턴스가 Nullable하다면 반드시 NPE 처리를 해야할 것이다.  

개발자 아무개가 한달 전 자신이 짠 코드에서 발생한 인스턴스가 Nullable한 사실을 절대로 까먹지 않을 거라는 보장은 어디에도 없다.  
보다 현실적으로 팀원이 만든 인스턴스를 굉장히 Null에 취약하게 사용하고 있을 수도 있다.  

### 빨간줄은 좋은것!
그렇다면 Optional을 썼을때의 장점은 꽤 명확하다.  
 - 메시지로 해당 인스턴스를 넘겨준 로직을 까볼 필요 없이
 - 그 인스턴스가 Null일 수 있는 가능성을 타입으로 알려주고
 - 해당 인스턴스의 Null 처리를 강제한다.
즉, Null에 취약한 객체의 처리를 빨간줄 및 컴파일 에러로 강제한다.

### 과도한 사용은 좀
역시 만능 실버키는 존재하지 않기 때문에 과도한 Optional도 기피해야 한다.
- 직렬화&역직렬화
- 라이프 사이클이 뻔히 제한돼 있는데 굳이 말았다가 꺼내 쓰는 행위
- Spring의 RequestBody  
등의 경우에는 주의가 필요하다.

### Property, Parameter에 사용?
https://stackoverflow.com/questions/29033518/is-it-a-good-practice-to-use-optional-as-an-attribute-in-a-class

Optional은 반환값에 쓰기를 의도하고 만들었다  

직렬화 & 역직렬화 문제도 있으나  

parameter에 Optional을 passing하는 것 자체가 안티 패턴  
 - 넘겨받는 메서드의 분기를 강제로 나눠버려서 그런듯  
 - 메서드 오버로딩 사용해서 분기를 확실히 하는게 더 나은 사용법이라는데,,  
 - https://medium.com/javarevisited/4-reasons-why-you-should-use-java-optional-or-not-4e44d51a9645

Property에 Optional을 쓰고 싶을때는 Getter의 Return에 쓰는 것이 더 건전해보임  