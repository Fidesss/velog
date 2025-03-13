<h2 id="웹-스코프">웹 스코프</h2>
<h3 id="특징">특징</h3>
<ul>
<li>웹 스코프는 웹 환경에서만 동작한다.</li>
<li>웹 스코프는 프로토타입과 다르게 스프링이 해당 스코프의 종료시점까지 관리한다. 따라서 종료 메서드가 호추로딘다.</li>
</ul>
<h3 id="종류">종류</h3>
<ul>
<li><code>request</code> : HTTP 요청 하나가 들어오고 나갈 때 까지 유지되는 스코프, 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고, 관리된다.</li>
<li><code>session</code> : HTTP Session과 동일한 생명주기를 가지는 스코프</li>
<li><code>application</code> : 서블릿 컨텍스트(ServletContext)와 생명주기를 가지는 스코프</li>
<li><code>websocket</code> : 웹 소켓과 동일한 생명주기를 가지는 스코프</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/a0941b9d-6134-4daa-a71b-a193fe3b8721/image.png" /></p>
<h2 id="request-스코프">request 스코프</h2>
<ul>
<li>먼저 웹 스코프는 웹 환경에서만 동작하므로 web 환경이 동작하도록 라이브러리를 추가하자</li>
</ul>
<h4 id="buildgradle에-추가">build.gradle에 추가</h4>
<pre><code>//web 라이브러리 추가
implementation 'org.springframework.boot:spring-boot-starter-web'</code></pre><ul>
<li>spring Project 파일의 <code>@SpringBootApplication</code> annotation이 작성되어있는 파일을 실행해 웹 환경을 열어준다.</li>
<li>실행하게되면 아래와같은 log를 확인할 수있다.<pre><code>Tomcat started on port(s): 8080 (http) with context path ''
Started CoreApplication in 0.914 seconds (JVM running for 1.528)</code></pre></li>
</ul>
<blockquote>
<p>참고 </p>
</blockquote>
<ul>
<li><p><code>spring-boot-starter-web</code>라이브러리를 추가하면 스프링 부트는 내장 톰켓 서버를 활용해서 웹 서버와 스프링을 함께 실행시킨다.</p>
</li>
<li><p>스프링 부트는 웹 라이브러리가 없으면 우리가 지금까지 학습한 <code>AnnotationConfigApplicationContext</code>을 기반으로 애플리케이션을 구동한다. 웹 라이브러리가 추가되면 웹과 관련된 추가 설정과 환경들이 필요하므로 <code>AnnotationConfigServletWebServerApplicationContext</code>를 기반으로 애플리케이션을 구동한다.</p>
</li>
<li><p>request 스코브 빈은 client의 요청이 와야 실제로 빈을 생성한다. 그렇기 때문에 아무생각 없이 사용하면 오류가 발생한다.</p>
</li>
</ul>
<pre><code>Error creating bean with name 'myLogger': Scope 'request' is not active for the 
current thread; consider defining a scoped proxy for this bean if you intend to 
refer to it from a singleton;</code></pre><ul>
<li>위와같은 내용의 오류가 발생</li>
<li>스프링 애플리케이션을 실행하는 시점에 싱글톤 빈은 생성해서 주입이 가능하지만, request 스코프 빈은 아직 생성되지 않았기 때문에 (client의 요청이 오지않았기 때문에) 이러한 오류가 발생하는 것이다.</li>
</ul>
<hr />
<h2 id="해결방안">해결방안</h2>
<ol>
<li>Provider 사용</li>
<li>Proxy 사용</li>
</ol>
<h2 id="provider-사용">Provider 사용</h2>
<pre><code class="language-java">//Controller
 @Controller
 @RequiredArgsConstructor
 public class LogDemoController {
     private final LogDemoService logDemoService;
     private final ObjectProvider&lt;MyLogger&gt; myLoggerProvider;

    @RequestMapping(&quot;log-demo&quot;)
    @ResponseBody
     public String logDemo(HttpServletRequest request) {
         String requestURL = request.getRequestURL().toString();
         MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.setRequestURL(requestURL);
        myLogger.log(&quot;controller test&quot;);
        logDemoService.logic(&quot;testId&quot;);
         return &quot;OK&quot;;
    }
 }

 //Service
 @Service
 @RequiredArgsConstructor
 public class LogDemoService {
     private final ObjectProvider&lt;MyLogger&gt; myLoggerProvider;
     public void logic(String id) {
         MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.log(&quot;service id = &quot; + id);
    }
 }</code></pre>
<blockquote>
<ul>
<li><code>ObjectProvider</code> 덕분에 <code>ObjectProvider.getObject()</code>를 호출하는 시점까지 request scope <strong>빈의 생성을 지연할 수 있다.</strong></li>
</ul>
</blockquote>
<ul>
<li><code>ObjectProvider.getObject()</code>를 호출하는 시점에는 HTTP 요청이 진행중이므로 request scope 빈의 생성이 정상 처리된다.</li>
<li><code>ObjectProvider.getObject()</code>를 <code>LogDemoController</code>, <code>LogDemoService</code>에서 각각 한번씩 따로 호출해도 같은 HTTP 요청이면 같은 스프링 빈이 반환된다.</li>
<li>Proxy 방법은 Provider 방법보다 코드를 더 줄일 수 있다.</li>
</ul>
<h2 id="스코프와-프록시">스코프와 프록시</h2>
<pre><code class="language-java">@Component
@Scope(value = &quot;request&quot;, proxyMode = ScopedProxyMode.TARGET_CLASS)
    public class MyLogger {
}</code></pre>
<blockquote>
<ul>
<li>위의 코드와 같이 <code>proxyMode = ScopedProxyMode.TARGET_CLASS</code>를 추가하자</li>
</ul>
</blockquote>
<ul>
<li>적용 대상이 인터페이스가 아닌 클래스면 <code>TARGET_CLASS</code>를 선택</li>
<li>적용 대상이 인터페이스이면 <code>INTERFACES</code>를 선택<ul>
<li>이렇게 하면 MyLogger의 가짜 프록시 클래스를 만들어두고 HTTP request와 상관 없이 가짜 프록시 클래스를 다른 빈에 미리 주입해 둘 수 있다.</li>
</ul>
</li>
</ul>
<h2 id="웹-스코프와-프록시-동작-원리">웹 스코프와 프록시 동작 원리</h2>
<ul>
<li>아까 위에서 proxy 설정을 했던 myLogger를 조사해서 출력결과를 확인해보자</li>
<li>출력결과 : <code>myLogger = class hello.core.common.MyLogger$$EnhancerBySpringCGLIB$$b68b726d</code></li>
<li>이걸로 스프링 컨테이너는 CGLIB라는 바이트코드를 조작하는 라이브러리를 사용해, MyLogger를 상속받은 가짜 프록시 객체를 생성한다는 것을 알 수 있다.</li>
<li><code>MyLogger$$EnhancerBySpringCGLIB</code>은 스프링 컨테이너에<code>myLogger</code>라는 이름 대신 객체로 등록된다는 것을 알 수 있다.</li>
<li>즉 의존관계 주입을 이 가짜 프록시 객체가 주입되는것이다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/8ee36ee4-3a75-4562-afdd-6cec81de36e5/image.png" /></p>
<blockquote>
<p><strong>가짜 프록시 객체는 요청이 오면 그 때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.</strong></p>
</blockquote>
<ul>
<li>가짜 프록시 객체는 내부에 진짜 <code>myLogger</code>를 찾는 방법을 알고있다.</li>
<li>클라이언트가 <code>myLogger.log()</code>을 호출하면 사실 가짜 프록시 객체의 메서드를 호출한 것이다.</li>
<li>가짜 프록시 객체는 <code>request</code> 스코프의 진짜 <code>myLogger.log()</code>를 호출한다.</li>
<li>가짜 프록시 객체는 원본 클래스를 상속 받아서 만들어졌기 때문에 이 객체를 사용하는 클라이언트 입장에서는 사실 원본인지 아닌지도 모르게, 동일하게 사용할 수 있다. (다형성)</li>
</ul>
<blockquote>
<p><strong>동작 정리</strong></p>
</blockquote>
<ul>
<li><code>CGLIB</code>라는 라이브러리로 내 클래스를 상속 받은 가짜 프록시 객체를 만들어서 주입한다.</li>
<li>이 가짜 프록시 객체는 실제 요청이 오면 그때 내부에서 실제 빈을 요청하는 위임 로직이 들어있다.</li>
<li>가짜 프록시 객체는 실제 <code>REQUEST SCOPE</code>와는 관계가 없다. 그냥 가짜일 뿐이고, 내부에 단순한 위임 로직만 있고, 싱글톤 처럼 동작한다.</li>
</ul>
<blockquote>
<p><strong>특징 정리</strong></p>
</blockquote>
<ul>
<li>프록시 객체 덕분에 클라이언트는 마치 싱글톤 빈을 사용하듯이 편리하게 <code>request scope</code>를 사용할 수 있다.</li>
<li>사실 <code>Provider</code>를 사용하든, 프록시를 사용하든 핵심아이디어는 진짜 객체 조회를 꼭 필요한 시점까지 지연처리 한다는 점이다.</li>
<li>단지 애노테이션 설정 변경만으로 원본 객체를 프록시 객체로 대체할 수 있다.</li>
<li>이러한 점이 바로 다형성과 DI 컨테이너가 가진 큰 강점이다.</li>
<li>꼭 웹 스코프가 아니어도 프록시는 사용할 수 있다.</li>
</ul>
<blockquote>
<p><strong>주의점</strong></p>
</blockquote>
<ul>
<li>마치 싱글톤을 사용하는 것 같지만 다르게 동작하기 때문에 결국 주의해서 사용해야 한다.</li>
<li>이런 특별한 scope는 꼭 필요한 곳에서만 최소화해서 사용하자, 무분별하게 사용하면 유지보수하기 어려워진다.


</li>
</ul>
<h3 id="📭-참고출처">📭 참고출처</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 핵심원리 - 기본편</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8</a></li>
</ul>