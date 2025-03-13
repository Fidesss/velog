<h1 id="싱글톤-패턴-singleton-pattern">싱글톤 패턴 (Singleton Pattern)</h1>
<ul>
<li>클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴</li>
<li>객체 인스턴스를 2개 이상 생성하지 못하도록 막는 패턴이다.</li>
</ul>
<hr />
<h3 id="그럼-왜-쓸까">그럼 왜 쓸까?</h3>
<ul>
<li>일반적인 DI패턴으로 객체를 구체화 하여 사용한다고 생각해보자</li>
<li>초당 1000개의 고객 트래픽이 발생하는 웹 애플리케이션에 일반적인 DI 패턴을 적용했다고 하면 초당 1000개의 객체가 생성되고 사라진다. -&gt; 고객 트래픽의 수가 많아질 수록 부담이된다.</li>
<li>싱글톤 패턴을 적용하면 한개만 생성하고 그것을 공유하게 된다. 이러한 이점 때문에 싱글톤 패턴을 사용한다.<br />

</li>
</ul>
<pre><code class="language-java">public class SingletonService {
    private static final SingletonService instance = new SingletonService();

    public static SingletonService getInstance() {
        return instance;
    }

    private SingletonService() {}
}</code></pre>
<br />

<ul>
<li>싱글톤 패턴은 일반적으로 java가 실행될때 static 영역에 객체를 생성하여 사용한다.</li>
<li>생성자를 private 접근 제한자로 막아놓음으로서 외부에서 new 키워드를 통해 접근하지 못하게 한다.</li>
<li>이 객체 인스턴스가 필요하면 오직 getInstance() 메서드를 통해서만 조회할 수 있음</li>
<li>이 메서드를 호출하면 항상 같은 인스턴스를 반환하게 된다.</li>
</ul>
<hr />
<h3 id="장점">장점</h3>
<ul>
<li>고객의 요청이 올 때 마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 사용하기 때문에 메모리를 효율적으로 관리할 수 있다.<br />
### 단점</li>
<li>싱글톤 패턴을 구현하는 코드 자체가 일반적인 방법으로 구현하는 것 보다 많이 들어간다.</li>
<li>의존 관계상 클라이언트가 구체 클래스에 의존하게 된다. <code>DIP 위반</code></li>
<li>클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다.</li>
<li>테스트하기 어려움 </li>
<li>내부 속성을 변경하거나 초기화 하기 어렵다.</li>
<li>private 생성자로 자식 클래스를 만들기 어려움</li>
<li>결론적으로 유연성이 떨어짐</li>
<li>다른말로 <code>안티패턴</code> 이라고 불리기도 한다.</li>
</ul>
<p><br /><br /></p>
<h3 id="📭-참고출처">📭 참고출처</h3>
<ul>
<li><code>인프런 김영한 강사님의 스프링 핵심원리</code></li>
</ul>