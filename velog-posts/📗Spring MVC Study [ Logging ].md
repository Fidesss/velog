<blockquote>
<p>운영 시스템에서는 System.out.println() 같은 시스템 콘솔을 사용해서 필요한 정보를 출력하지 않고, 별도의 로깅 라이브러리를 사용해서 로그를 출력한다. </p>
</blockquote>
<h2 id="로깅-라이브러리">로깅 라이브러리</h2>
<ul>
<li><p>스프링 부트 라이브러리를 사용하면 스프링 부트 로깅 라이브러리(spring-boot-starter-logging)가 함께 포함된다.</p>
</li>
<li><p>스프링 부트 로깅 라이브러리는 기본으로 다음 로깅 라이브러리를 사용한다.</p>
<ul>
<li>SLF4J - <a href="http://www.slf4j.org">http://www.slf4j.org</a></li>
<li>Logback - <a href="http://logback.qos.ch">http://logback.qos.ch</a></li>
</ul>
</li>
<li><p>로그 라이브러리는 Logback,Log4J, Log4J2 등등 수많은 라이브러리가 있는데, 그것을 통합해서 인터페이스로 제공하는 것이 바로 <code>SLF4J</code>라이브러리다.</p>
</li>
</ul>
<pre><code class="language-java">package hello.springmvc.basic;
import lombok.extern.slf4j.Slf4j;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
//@Slf4j
@RestController
public class LogTestController {
    private final Logger log = LoggerFactory.getLogger(getClass());
    @RequestMapping(&quot;/log-test&quot;)
    public String logTest() {
    String name = &quot;Spring&quot;;

    log.trace(&quot;trace log={}&quot;, name);
    log.debug(&quot;debug log={}&quot;, name);
    log.info(&quot; info log={}&quot;, name);
    log.warn(&quot; warn log={}&quot;, name);
    log.error(&quot;error log={}&quot;, name);
    //로그를 사용하지 않아도 a+b 계산 로직이 먼저 실행됨, 이런 방식으로 사용하면 X
    log.debug(&quot;String concat log=&quot; + name);
     return &quot;ok&quot;;
    }
 }</code></pre>
<h3 id="로그-선언">로그 선언</h3>
<ul>
<li><code>private Logger log = LoggerFactory.getLogger(getClass());</code></li>
<li><code>private static final Logger log = LoggerFactory.getLogger(Xxx.class)</code></li>
<li><code>Slf4j</code> : 롬복 사용 가능</li>
</ul>
<h3 id="로그-호출">로그 호출</h3>
<ul>
<li><code>log.info(&quot;hello&quot;)</code></li>
<li><code>System.out.println(&quot;hello&quot;)</code></li>
<li>시스템 콘솔로 직접 출력하는 것 보다 로그를 사용하면 다음과 같은 장점이 존재한다. 실무에서는 항상 로그를 사용할 것</li>
</ul>
<h3 id="매핑-정보">매핑 정보</h3>
<ul>
<li><code>@RestController</code><ul>
<li><code>@Controller</code>는 반환값이 <code>String</code>이면 뷰 이름으로 인식된다. 그래서 <strong>뷰를 찾고 뷰가 렌더링</strong> 된다.</li>
<li><code>@RestController</code>는 반환 값으로 뷰를 찾는 것이 아니라, HTTP 메시지 바디에 바로 입력한다. 따라서 실행 결과로 ok를 받을 수 있다. <code>@ResponseBody</code>와 관련이 있다.</li>
</ul>
</li>
</ul>
<h3 id="테스트">테스트</h3>
<ul>
<li>로그가 출력되는 포멧 확인<ul>
<li>시간, 로그 레벨, 프로세스ID, 쓰레드 명, 클래스명, 로그 메시지</li>
</ul>
</li>
<li>로그 레벨 설정을 변경해서 출력 결과를 보자.<ul>
<li>LEVEL : <code>TRACE &gt; DEBUG &gt; INFO &gt; WARN &gt; ERROR</code></li>
<li>개발 서버는 debug 출력</li>
<li>운영 서버는 info 출력</li>
</ul>
</li>
<li><code>@Slf4j</code>로 변경</li>
</ul>
<h3 id="로그레벨-설정">로그레벨 설정</h3>
<pre><code># application.properties
# 전체 로그 레벨 설정(기본 info)
logging.level.root=info

#hello.springmvc 패키지와 그 하위 로그 레벨 설정
logging.level.hello.springmvc=debug</code></pre><h3 id="올바른-로그-사용법">올바른 로그 사용법</h3>
<ul>
<li><code>log.debug(&quot;data=&quot;+data)</code> &gt; <strong>잘못된 로그 사용법</strong><ul>
<li>로그 출력 레벨 info를 설정해도 해당 코드에 있는 &quot;data=&quot;+data가 실제 실행이 되어 버린다. 결과적으로 문자 더하기 연산이 발생하여 cpu, 메모리 등을 사용하기 때문에 잘못된 로그 사용법이라고 할 수 있다.</li>
</ul>
</li>
<li><code>log.debug(&quot;data={}&quot;, data)</code><ul>
<li>로그 출력 레벨을 info로 설정하면 아무일도 발생하지 않는다. 따라서 앞과 같은 의미없는 연산이 발생하지 않는다.</li>
</ul>
</li>
</ul>
<h3 id="로그-사용시-장점">로그 사용시 장점</h3>
<ul>
<li>쓰레드 정보, 클래스 이름 같은 부가 정보를 함께 볼 수 있고, 출력 모양을 조정할 수 있다.</li>
<li>로그레벨에 따라 개발 서버에서는 모든 로그를 출력하고, 운영서버에서는 출력하지 않는 등 로그를 상황에 맞기 조절할 수 있다.</li>
<li>시스템 아웃 콘솔에만 출력하는 것이 아니라, 파일이나 네트워크 등, 로그를 별도의 위치에 남길 수 있다. 특히 파일로 남길 때는 일별, 특정 용량에 따라 로그를 분할하는 것도 가능하다.</li>
<li>성능도 일반 System.out보다 좋음, (내부 버퍼링, 멀티 쓰레드 등등) 그래서 실무에서는 꼭 로그를 사용해야 한다.</li>
</ul>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>