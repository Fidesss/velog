<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/6641fd1c-ba61-4ec5-9567-347ed00eac23/image.png" /></p>
<ul>
<li>전 시간에 직접 만들어봤던 MVC 프레임워크 구조</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/660f4324-7011-415d-a256-16f2e7dc9b5b/image.png" /></p>
<ul>
<li>Spring MVC 구조</li>
</ul>
<blockquote>
<p><strong>직접 만든 프레임워크 -&gt; 스프링 MVC 비교</strong></p>
</blockquote>
<ul>
<li>FrontController -&gt; DispatcherServlet</li>
<li>handlerMappingMap -&gt; HandlerMapping</li>
<li>MyHandlerAdapter -&gt; HandlerAdapter</li>
<li>ModelView -&gt; ModelAndView</li>
<li>viewResolver -&gt; ViewResolver</li>
<li>MyView -&gt; View</li>
</ul>
<h2 id="dispatcherservlet-구조-살펴보기">DispatcherServlet 구조 살펴보기</h2>
<blockquote>
<p><code>org.springframework.web.servlet.DispatcherServlet</code></p>
</blockquote>
<ul>
<li>spring MVC도 프론트 컨트롤러 패턴으로 구현되어 있다.</li>
<li>spring MVC도 프론트 컨트롤러가 바로 DispatcherServlet이다.</li>
<li>DispatcherServlet이 SpringMVC의 핵심이다.</li>
</ul>
<h3 id="dispatcherservlet-서블릿-등록">DispatcherServlet 서블릿 등록</h3>
<blockquote>
<ul>
<li>DispatcherServlet도 부모 클래스에서 HttpServlet을 상속 받아서 사용하고, 서블릿으로 동작한다.</li>
</ul>
</blockquote>
<ul>
<li>Dispatcher -&gt; FrameworkServlet -&gt; HttpServletBean -&gt; HttpServlet<ul>
<li>스프링 부트는 DispatcherServlet을 서블릿으로 자동 등록하면서 모든 경로 <code>urlPatterns=&quot;/&quot;</code>에 대해서 매핑한다.</li>
</ul>
</li>
<li>참고 : 더 자세한 경로가 우선순위가 높다. 즉 DispatcherServlet은 우선순위가 낮다.</li>
</ul>
<h3 id="요청-흐름">요청 흐름</h3>
<blockquote>
<ul>
<li>서블릿이 호출되면 HttpServlet이 제공하는 service()가 호출된다.</li>
</ul>
</blockquote>
<ul>
<li>스프링 MVC는 DispatcherServlet의 부모인 FrameworkSerlvet에서 service()를 오버라이드 해두었다.</li>
<li>FrameworkSerlvet.service()를 시작으로 여러 메서드가 호출되면서 DispatcherServlet.doDispatch()가 호출된다.</li>
</ul>
<h3 id="dispatcherserlvet-핵심---dodispatch-">DispatcherSerlvet 핵심 [  doDispatch() ]</h3>
<pre><code class="language-java">protected void doDispatch(HttpServletRequest request, HttpServletResponse 
response) throws Exception {
     HttpServletRequest processedRequest = request;
     HandlerExecutionChain mappedHandler = null;
     ModelAndView mv = null;

     // 1. 핸들러 조회
    mappedHandler = getHandler(processedRequest);

     if (mappedHandler == null) {
         noHandlerFound(processedRequest, response);
         return;
     }
     // 2. 핸들러 어댑터 조회 - 핸들러를 처리할 수 있는 어댑터
    HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

     // 3. 핸들러 어댑터 실행 -&gt; 4. 핸들러 어댑터를 통해 핸들러 실행 -&gt; 5. ModelAndView 반환
    mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
     processDispatchResult(processedRequest, response, mappedHandler, mv, 
    dispatchException);
 }
private void processDispatchResult(HttpServletRequest request, 
HttpServletResponse response, HandlerExecutionChain mappedHandler, ModelAndView 
mv, Exception exception) throws Exception {
    // 뷰 렌더링 호출
    render(mv, request, response);
 }
protected void render(ModelAndView mv, HttpServletRequest request, 
HttpServletResponse response) throws Exception {
    View view;
    String viewName = mv.getViewName();
     // 6. 뷰 리졸버를 통해서 뷰 찾기, 7. View 반환
    view = resolveViewName(viewName, mv.getModelInternal(), locale, request);
     // 8. 뷰 렌더링
    view.render(mv.getModelInternal(), request, response);
}</code></pre>
<blockquote>
<p><strong>동작 순서</strong></p>
</blockquote>
<ol>
<li>핸들러 조회 : 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.</li>
<li>핸들러 어댑터 조회 : 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.</li>
<li>핸들러 어댑터 실행 : 핸들러 어댑터를 실행한다.</li>
<li>핸들러 실행 : 핸들러 어댑터가 실제 핸들러를 실행한다.</li>
<li>ModelAndView 반환 : 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서 반환한다.</li>
<li>viewResolver 호출 : 뷰 리졸버를 찾고 실행한다. (jsp의 경우 : InternalResourceViewResolver가 자동 등록되고, 사용된다.)</li>
<li>View 반환 : 뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환한다. (jsp의 경우 : InternalResourceView(JstlView)를 반환하는데 내부에 foward()로직이 있다.)</li>
<li>뷰 렌더링 : 뷰를 통해서 뷰를 렌더링 한다.</li>
</ol>
<h3 id="인터페이스-살펴보기">인터페이스 살펴보기</h3>
<ul>
<li>스프링 MVC의 큰 강점은 <code>DispatcherServlet</code>코드의 변경 없이, 원하는 기능을 변경하거나 확장할 수 있다는 점이다. </li>
<li>인터페이스들만 구현해서 <code>DispatcherServlet</code>에 등록하면 개인만의 컨트롤러를 만들 수 있다.</li>
</ul>
<h3 id="주요-인터페이스-목록">주요 인터페이스 목록</h3>
<blockquote>
<ul>
<li>핸들러 매핑 : <code>org.springframework.web.servlet.HandlerMapping</code></li>
</ul>
</blockquote>
<ul>
<li>핸들러 어댑터 : <code>org.springframework.web.servlet.HandlerAdapter</code></li>
<li>뷰 리졸버 : <code>org.springframework.web.servlet.ViewResolver</code></li>
<li>뷰 : <code>org.springframework.web.servlet.View</code></li>
</ul>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>