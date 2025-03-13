<h2 id="빈-생명주기-콜백">빈 생명주기 콜백</h2>
<ul>
<li>데이터베이스 커넥션 풀이나, 네트워크 소켓처럼 애플리케이션 시작 시점에 필요한 연결을 미리 해두고, 애플리케이션 종료 시점에 연결을 모두 종료하는 작업을 한다고 가정해보자</li>
<li>이런 작업을 수행하려면 객체의 초기화와 종료 작업이 필요하다.<h3 id="스프링-빈의-이벤트-라이프-사이클">스프링 빈의 이벤트 라이프 사이클</h3>
</li>
<li>스프링 컨테이너 생성 -&gt; 스프링 빈 생성 -&gt; 의존관계주입 -&gt; 초기화 콜백 -&gt; 사용 -&gt; 소멸전 콜백 -&gt; 스프링 종료</li>
</ul>
<h3 id="초기화-콜백">초기화 콜백</h3>
<ul>
<li>빈이 생성되고, 빈의 의존관계 주입이 완료된 후 호출</li>
</ul>
<h3 id="소멸전-콜백">소멸전 콜백</h3>
<ul>
<li>빈이 소멸되기 직전에 호출</li>
</ul>
<h3 id="참고">참고</h3>
<blockquote>
<p><strong>객체의 생성과 초기화를 분리하자</strong></p>
</blockquote>
<ul>
<li>생성자는 필수 정보(파라미터)를 받고, 메모리를 할당해서 객체를 생성하는 책임을 가진다.</li>
<li>반면에 초기화는 이렇게 생성된 값들을 활용해서 외부 커넥션을 연결하는 등 무거운 동작을 수행한다.</li>
<li>따라서 생성자 안에서 무거운 초기화 작업을 함께 하는 것 보다는 객체를 생성하는 부분과 초기화 하는 부분을 명확하게 나누는 것이 유지보수 관점에서 좋다.</li>
<li>초기화 작업이 내부 값들만 약간 변경하는 정도로 단순한 경우에는 생성자에서 한번에 다 처리하는게 더 나을 수 있다.
<strong>싱글톤 빈들의 콜백</strong></li>
<li>싱글톤 빈들은 스프링 컨테이너가 종료될 때 싱글톤 빈들도 함께 종료되기 때문에 스프링 컨테이너가 종료되기 직전에 소멸전 콜백이 일어난다.</li>
<li>싱글톤처럼 컨테이너의 시작과 종료까지 생존하는 빈도 있지만, 생명주기가 짧은 빈들도 있는데 이 빈들은 컨테이너와 무관하게 해당 빈이 종료되기 직전에 소멸전 콜백이 일어난다. </li>
</ul>
<h3 id="스프링의-콜백지원">스프링의 콜백지원</h3>
<ol>
<li>인터페이스 (initializingBean, DisposableBean)</li>
<li>설정 정보에 초기화 메서드, 종료 메서드 지정</li>
<li>@PostConstruct, @PreDestroy annotation 지원 (주로 3번을 사용)</li>
</ol>
<hr />
<h2 id="애노테이션-postconstruct-predestroy">애노테이션 @PostConstruct, @PreDestroy</h2>
<pre><code class="language-java">
 public class NetworkClient {
    private String url;
    public NetworkClient() {
        System.out.println(&quot;생성자 호출, url = &quot; + url);
    }

    public void setUrl(String url) {
        this.url = url;
    }

    //서비스 시작시 호출
    public void connect() {
        System.out.println(&quot;connect: &quot; + url);
    }

     public void call(String message) {
         System.out.println(&quot;call: &quot; + url + &quot; message = &quot; + message);
    }

     //서비스 종료시 호출
    public void disConnect() {
         System.out.println(&quot;close + &quot; + url);
    }

     @PostConstruct
     public void init() {
         System.out.println(&quot;NetworkClient.init&quot;);
         connect();
         call(&quot;초기화 연결 메시지&quot;);
    }

     @PreDestroy
     public void close() {
         System.out.println(&quot;NetworkClient.close&quot;);
         disConnect();
    }
 }</code></pre>
<pre><code class="language-java"> @Configuration
 static class LifeCycleConfig {
    @Bean
 public NetworkClient networkClient() {
 NetworkClient networkClient = new NetworkClient();
        networkClient.setUrl(&quot;http://hello-spring.dev&quot;);
 return networkClient;
    }
 }</code></pre>
<ul>
<li>@PostConstruct, @PreDestroy 이 두 애노테이션을 사용하면 다른 두 가지 방법보다 훨씬 편리하게 초기화와 종료를 실행할 수 있다.</li>
</ul>
<h3 id="postconstruct-predestroy-애노테이션-특징">@PostConstruct, @PreDestroy 애노테이션 특징</h3>
<ul>
<li>최신 스프링에서 가장 권장하는 방법</li>
<li>애노테이션 하나만 붙이면 되므로 매우 편리</li>
<li>패키지를 잘 보면 javax.annotation.PostConstruct이다. 스프링에 종속적인 기술이 아니라 JSR-250라는 자바 표준, 그렇기에 스프링이 아닌 다른 컨테이너에서도 동작하는 장점이 존재한다.</li>
<li>컴포넌트 스캔과 잘 어울린다.</li>
<li>유일한 단점은 외부 라이브러리에 적용하지 못한다.<ul>
<li>외부라이브러리를 초기화, 종료해야할 때는 @Bean의 기능을 사용하자. (2번 방법)</li>
</ul>
</li>
</ul>


<h3 id="📭-참고출처">📭 참고출처</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 핵심원리 - 기본편</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8</a></li>
</ul>