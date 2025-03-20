<h2 id="핸들러-매핑과-핸들러-어댑터">핸들러 매핑과 핸들러 어댑터</h2>
<ul>
<li>현재 Spring은 annotation 기반으로 컨트롤러나 핸들러를 사용한다. </li>
<li>원리를 알아보기위해 과거에 사용했던 방법으로 공부해보자.</li>
</ul>
<h2 id="controller-interface">Controller Interface</h2>
<ul>
<li>과거 버전 스프링 컨트롤러</li>
</ul>
<pre><code class="language-java"> public interface Controller {
 ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse 
 response) throws Exception;
 }</code></pre>
<ul>
<li>스프링도 처음에는 이런 딱딱한 형식의 컨트롤러를 제공했었다.</li>
<li>참고<ul>
<li>Controller 인터페이스는 @Controller와 전혀 다름.</li>
</ul>
</li>
</ul>
<pre><code class="language-java">@Component(&quot;/springmvc/old-controller&quot;)
public class OldController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, 
    HttpServletResponse response) throws Exception {
         System.out.println(&quot;OldController.handleRequest&quot;);
        return null;
    }
 }</code></pre>
<ul>
<li><code>@Component</code> : 이 컨트롤러는 <code>/springmvc/old-controller</code>라는 이름의 <strong>스프링 빈</strong>으로 등록되었다.</li>
<li><strong>빈의 이름으로 URL을 매핑</strong></li>
</ul>
<h3 id="이-컨트롤러는-어떻게-호출되었을까">이 컨트롤러는 어떻게 호출되었을까?</h3>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/720f2971-bae1-4c2f-972f-60d310a11efd/image.png" /></p>
<p>이 컨트롤러가 호출되려면 다음 2가지가 필요하다.</p>
<ul>
<li><p>HandlerMapping (핸들러 매핑)</p>
<ul>
<li>핸들러 매핑에서 이 컨트롤러를 찾을 수 있어야 한다.</li>
<li>예) 스프링 빈의 이름으로 핸들러를 찾을 수 있는 핸들러 매핑이 필요하다.</li>
</ul>
</li>
<li><p>HandlerAdaptor (핸들러 어댑터)</p>
<ul>
<li>핸들러 매핑을 통해서 찾은 핸들러를 실행할 수 있는 핸들러 어댑터가 필요하다.</li>
<li>예) Controller 인터페이스를 실행할 수 있는 핸들러 어댑터를 찾고 실행해야 한다.</li>
</ul>
</li>
</ul>
<blockquote>
<p>사실상 개발자가 직접 핸들러 매핑과 핸들러 어댑터를 만드는 일은 거의 없다. &gt; 스프링이 이미 필요한 핸들러 매핑과 핸들러 어댑터를 대부분 구현해두었기 때문이다.</p>
</blockquote>
<h3 id="스프링-부트가-자동-등록하는-핸들러-매핑과-핸들러-어댑터">스프링 부트가 자동 등록하는 핸들러 매핑과 핸들러 어댑터</h3>
<ul>
<li>실제로는 더 많지만 중요한 부분 위주로 확인하기 위해 일부 생략</li>
</ul>
<p><strong>HandlerMapping</strong></p>
<table>
<thead>
<tr>
<th>0 = RequestMappingHandlerMapping</th>
<th>애노테이션 기반의 컨트롤러인 @RequestMapping에서 사용</th>
</tr>
</thead>
<tbody><tr>
<td>1 = BeanNameUrlHandlerMapping</td>
<td>스프링 빈의 이름으로 핸들러를 찾는다.</td>
</tr>
</tbody></table>
<p><strong>HandlerAdapter</strong></p>
<table>
<thead>
<tr>
<th>0 = RequestMappingHandlerAdapter</th>
<th>애노테이션 기반의 컨트롤러인 @RequestMapping에서 사용</th>
</tr>
</thead>
<tbody><tr>
<td>1 = HttpRequestHandlerAdapter</td>
<td>HttpRequestHandler 처리</td>
</tr>
<tr>
<td>2 = SimpleControllerHandlerAdapter</td>
<td>Controller 인터페이스(애노테이션X, 과거에 사용) 처리</td>
</tr>
</tbody></table>
<ul>
<li>핸들러 매핑도, 핸들러 어댑터도 모두 순서대로 찾고 만약 없으면 다음 순서로 넘어간다. <code>여기서 순위는 숫자가 상대적으로 작을수록 우선순위가 높다.</code></li>
</ul>
<h4 id="1-핸들러-매핑으로-핸들러-조회">1. 핸들러 매핑으로 핸들러 조회</h4>
<blockquote>
<ol>
<li>HandlerMapping을 순서대로 실행해서, 핸들러를 찾는다.</li>
<li>이 경우 빈 이름으로 핸들러를 찾아야 하기 때문에 이름 그대로 빈 이름으로 핸들러를 찾아주는 <code>BeanNameUrlHandlerMapping</code>가 실행에 성공하고 핸들러인 <code>OldController</code>를 반환한다.</li>
</ol>
</blockquote>
<h4 id="2-핸들러-어댑터-조회">2. 핸들러 어댑터 조회</h4>
<blockquote>
<ol>
<li>HandlerAdapter의 supports()를 순서대로 호출</li>
<li>SimpleControllerHandlerAdapter가 Controller 인터페이스를 지원하므로 대상이 된다.</li>
</ol>
</blockquote>
<h4 id="3-핸들러-어댑터-실행">3. 핸들러 어댑터 실행</h4>
<blockquote>
<ol>
<li>디스패처 서블릿이 조회한 SimpleControllerHandlerAdapter를 실행하면서 핸들러 정보도 함께 넘겨준다.</li>
<li>SimpleControllerHandlerAdapter는 핸들러인 OldController를 내부에서 실행하고, 그 결과를 반환한다.</li>
</ol>
</blockquote>
<blockquote>
<p>정리 - OldController 핸들러 매핑, 어댑터</p>
</blockquote>
<ul>
<li>OldController를 실행하면서 사용된 객체는 다음과 같음</li>
<li>HandlerMapping = BeanNameUrlHandlerMapping</li>
<li>HandlerAdapter = SimpleControllerHandlerAdapter</li>
</ul>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>