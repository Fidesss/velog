<h2 id="httprequesthandler">HttpRequestHandler</h2>
<ul>
<li><code>HttpRequestHandler</code>핸들러(컨트롤러)는 <strong>서블릿과 가장 유사한 형태</strong>의 핸들러이다.</li>
</ul>
<pre><code class="language-java"> public interface HttpRequestHandler {
     void handleRequest(HttpServletRequest request, HttpServletResponse response)
         throws ServletException, IOException;
 }</code></pre>
<h3 id="myhttprequesthandler">MyHttpRequestHandler</h3>
<pre><code class="language-java">@Component(&quot;/springmvc/request-handler&quot;)
public class MyHttpRequestHandler implements HttpRequestHandler {
    @Override
    public void handleRequest(HttpServletRequest request, HttpServletResponse 
    response) throws ServletException, IOException {
         System.out.println(&quot;MyHttpRequestHandler.handleRequest&quot;);
    }
}</code></pre>
<h4 id="1-핸들러-매핑으로-핸들러-조회">1. 핸들러 매핑으로 핸들러 조회</h4>
<blockquote>
<ol>
<li>HandlerMapping 을 순서대로 실행해서, 핸들러를 찾는다.</li>
<li>이 경우 빈 이름으로 핸들러를 찾아야 하기 때문에 이름 그대로 빈 이름으로 핸드러를 찾아주는 <code>BeanNameUrlHandlerMapping</code>가 실행에 성공하고 핸들러인 <code>MyHttpRequestHandler</code>를 반환한다.</li>
</ol>
</blockquote>
<h4 id="2-핸들러-어댑터-조회">2. 핸들러 어댑터 조회</h4>
<blockquote>
<ol>
<li><code>HandlerAdapter</code>의 <code>supports()</code>를 순서대로 호출한다.</li>
<li><code>HttpRequestHandlerAdapter</code>가 <code>HttpRequestHandler</code> 인터페이스를 지원하도록 대상이 된다.</li>
</ol>
</blockquote>
<h4 id="3-핸들러-어댑터-실행">3. 핸들러 어댑터 실행</h4>
<blockquote>
<ol>
<li>디스패처 서블릿이 조회한 <code>HttpRequestHandlerAdapter</code>를 실행하면서 핸들러 정보도 함께 넘겨준다.</li>
<li><code>HttpRequestHandlerAdapter</code>는 핸들러인 <code>MyHttpRequestHandler</code>를 내부에서 실행하고, 그 결과를 반환한다.</li>
</ol>
</blockquote>
<h3 id="정리">정리</h3>
<blockquote>
<p><strong>HttpRequestHandler 핸들러매핑, 어댑터</strong></p>
</blockquote>
<ul>
<li><code>MyHttpRequestHandler</code>를 실행하면서 사용된 객체<ul>
<li><code>HandlerMapping = BeanUrlHandlerMapping</code></li>
<li><code>HandlerAdapter = HttpRequestHandlerAdapter</code></li>
</ul>
</li>
</ul>
<hr />
<h2 id="뷰-리졸버">뷰 리졸버</h2>
<blockquote>
<ul>
<li>스프링 부트는 <code>InternalResourceViewResolver</code>라는 뷰 리졸버를 자동으로 등록하는데, 이때 <code>application.properties</code>에 등록한 <code>spring.mvc.view.prefix,</code> <code>spring.mvc.view.suffix</code> 설정 정보를 사용해서 등록한다.</li>
</ul>
</blockquote>
<ul>
<li>참고로 권장하지는 않지만 전체경로로 주어도 동작하기는 한다.<ul>
<li><code>return new ModelAndView(&quot;/WEB-INF/views/new-form.jsp&quot;);</code></li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/3d776b60-7dfa-48bf-a185-fa75904d83cc/image.png" /></p>
<table>
<thead>
<tr>
<th>1</th>
<th>BeanNameViewResolver : 빈 이름으로 뷰를 찾아서 반환한다. (ex : excel 파일 생성 기능에 사용)</th>
</tr>
</thead>
<tbody><tr>
<td>2</td>
<td>InternalResourceViewResolver : JSP를 처리할 수 있는 뷰를 반환한다.</td>
</tr>
</tbody></table>
<h4 id="1-핸들러-어댑터-호출">1. 핸들러 어댑터 호출</h4>
<ul>
<li>핸들러 어밷터를 통해 <code>new-form</code>이라는 논리 뷰 이름을 획득</li>
</ul>
<h4 id="2-viewresolver-호출">2. ViewResolver 호출</h4>
<ul>
<li><code>new-form</code>이라는 뷰 이름으로 viewResolver를 순서대로 호출</li>
<li>BeanNameViewResolver는 new-form이라는 이름의 스프링 빈으로 등로고딘 뷰를 찾아야 하는데 없음.</li>
<li>InternalResourceViewResolver가 호출</li>
</ul>
<h4 id="3-internalresourceviewresolver">3. InternalResourceViewResolver</h4>
<ul>
<li>위의 뷰 리졸버는 InternalResourceView를 호출한다.</li>
</ul>
<h4 id="4-뷰---internalresourceview">4. 뷰 - InternalResourceView</h4>
<ul>
<li>InternalResourceView는 JSP처럼 포워드 foward()를 호출해서 처리할 수 있는 경우에 사용</li>
</ul>
<h4 id="5-viewrender">5. view.render()</h4>
<ul>
<li>view.render()가 호출되고 InternalResourceView는 foward()를 사용해서 JSP를 실행</li>
</ul>
<blockquote>
<p><strong>참고</strong></p>
</blockquote>
<ul>
<li><code>InternalResourceViewResolver</code> 는 만약 JSTL 라이브러리가 있으면 <code>InternalResourceView</code>를 상속받은 <code>JstlView</code>를 반환한다. <code>JstlView</code>는 JSTL 태그 사용시 양간의 부가 기능이 추가된다.</li>
</ul>
<hr />
<ul>
<li>다른 뷰는 실제 뷰를 렌더링하지만, JSP의 경우 <code>forward()</code> 통해서 해당 JSP로 이동(실행)해야 렌더링이 된다.</li>
<li>JSP를 제외한 나머지 뷰 템플릿들은 <code>foward()</code> 과정없이 바로 렌더링 된다.</li>
</ul>
<hr />
<ul>
<li><code>Thymeleaf</code> 뷰 템플릿을 사용하면 <code>ThymeleafViewResolver</code>를 등록해야 한다.</li>
<li>최근에는 라이브러리만 추가하면 스프링 부트가 이런 작업들도 모두 자동화해준다.</li>
</ul>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>