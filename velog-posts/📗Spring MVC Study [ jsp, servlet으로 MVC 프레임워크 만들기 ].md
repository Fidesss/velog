<h2 id="프론트-컨트롤러">프론트 컨트롤러</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/72b5e93f-819d-4f82-bdf5-5998c26d3f67/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/0825d5cf-73a5-49db-b43e-e008fab449bd/image.png" /></p>
<blockquote>
<p>Front Controller 도입 전에 클라이언트에서 호출을 할때 Controller A, B, C 이렇게 다른 Controller을 호출해도 공통적인 부분이 많아 공통처리가 되지 않아 불편한 점이 많았다. 하지만 도입 후 어떤 것이 유리해졌는지 확인해보자.</p>
</blockquote>
<h3 id="front-controller-특징">Front Controller 특징</h3>
<ul>
<li>프론트 컨트롤러 서블릿 하나로 클라이언트의 요청을 받는다.</li>
<li>프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아서 호출</li>
<li>입구가 하나, 공통 처리 가능</li>
<li>프론트 컨트롤러를 제외한 나머지 컨트롤러는 Servlet을 사용하지 않아도 된다.</li>
</ul>
<h3 id="스프링-웹-mvc와-프론트-컨트롤러">스프링 웹 MVC와 프론트 컨트롤러</h3>
<ul>
<li>스프링 웹 MVC의 핵심도 바로 FrontController이다.</li>
<li>스프링 웹 MVC의 DispatcherServlet이 FrontController 패턴으로 구현되어 있다.</li>
</ul>
<p>지금부터 Spring MVC를 학습하기 전 어떤 방식으로 발전해 왔는지 공부한 것 중 핵심적인 내용만 작성</p>
<hr />
<h2 id="v1-버전">v1 버전</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/75f6e839-5bef-427b-b3e1-04e795385bc3/image.png" /></p>
<pre><code class="language-java">@WebServlet(name = &quot;frontControllerServletV1&quot;, urlPatterns = &quot;/front-controller/v1/*&quot;)
 public class FrontControllerServletV1 extends HttpServlet {
     private Map&lt;String, ControllerV1&gt; controllerMap = new HashMap&lt;&gt;();
     public FrontControllerServletV1() {
        controllerMap.put(&quot;/front-controller/v1/members/new-form&quot;, new 
        MemberFormControllerV1());

        controllerMap.put(&quot;/front-controller/v1/members/save&quot;, new 
        MemberSaveControllerV1());

        controllerMap.put(&quot;/front-controller/v1/members&quot;, new 
        MemberListControllerV1());
    }
    @Override
     protected void service(HttpServletRequest request, HttpServletResponse response)
     throws ServletException, IOException {

     System.out.println(&quot;FrontControllerServletV1.service&quot;);

     String requestURI = request.getRequestURI();
     ControllerV1 controller = controllerMap.get(requestURI);

     if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
             return;
    }
    controller.process(request, response);
  }
}</code></pre>
<h3 id="프론트-컨트롤러-분석">프론트 컨트롤러 분석</h3>
<blockquote>
<p><strong>urlPatterns</strong></p>
</blockquote>
<ul>
<li><code>urlPatterns = &quot;/front-controller/v1/*&quot;</code>  이런식으로 Servlet의 urlPattern을 설정할 경우 <code>/front-controller/v1</code>를 포함한 하위 모든 요청은 이 서블릿에서 받아들이게 된다.</li>
<li>즉 다른 하위 요청을 불렀을 때 무조건 이 서블릿을 거치게 된다는 것.</li>
</ul>
<blockquote>
<p><strong>controllerMap</strong></p>
</blockquote>
<ul>
<li>key : 매핑 URL</li>
<li>value : 호출될 컨트롤러</li>
</ul>
<blockquote>
<p><strong>service()</strong></p>
</blockquote>
<ul>
<li>먼저 <code>requestURL</code>를 조회해서 실제 호출할 컨트롤러를 <code>controllerMap</code>에서 찾는다. 만약 없다면 404(SC_NOT_FOUND) 상태 코드를 반환한다.</li>
<li>컨트롤러를 찾고 <code>controlle.process(request. response);</code>을 호출하여 해당 컨트롤러를 실행</li>
</ul>
<hr />
<h2 id="v2-버전">v2 버전</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/3448520a-7dda-4adb-a71e-ae6589bd2c17/image.png" /></p>
<ul>
<li>모든 컨트롤러에서 뷰로 이동하는 부분에 중복이 있음, 깔끔하지 않기 때문에 이동</li>
</ul>
<pre><code class="language-java"> String viewPath = &quot;/WEB-INF/views/new-form.jsp&quot;;
 RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
 dispatcher.forward(request, response);</code></pre>
<h3 id="v2-버전의-컨트롤러-인터페이스">v2 버전의 컨트롤러 인터페이스</h3>
<pre><code class="language-java">public interface ControllerV2 {
     MyView process(HttpServletRequest request, HttpServletResponse response) 
throws ServletException, IOException;
}</code></pre>
<blockquote>
<p>기존에는 각 컨트롤러에서 복잡한 <code>dispatcher.forward()</code>를 직접 생성했었지만 이제는 호출하지 않아도 된다. 단순히 MyView 객체를 생성하고 거기에 뷰 이름만 넣고 반환하면 되게 코드가 설계 됨</p>
</blockquote>
<blockquote>
<p>결론 : 단순 반복 되는 뷰 로직 분리</p>
</blockquote>
<hr />
<h2 id="v3-버전">v3 버전</h2>
<blockquote>
<p><strong>서블릿 종속성 제거</strong></p>
</blockquote>
<ul>
<li>컨트롤러 입장에서는 <code>HttpServletRequest</code> <code>HttpServletResponse</code>가 꼭 필요할까?</li>
<li>요청 파라미터 정보는 자바의 Map으로 대신 넘기도록 하면 지금 구조에서는 컨트롤러가 서블릿 기술을 몰라도 동작할 수 있다.</li>
<li>request 객체를 Model로 사용하는 대신에 별도의 Model 객체를 만들어서 반환하면 된다.</li>
<li>우리가 구현하는 컨트롤러가 서블릿 기술을 전혀 사용하지 않도록 변경해보자.</li>
</ul>
<blockquote>
<p><strong>뷰 이름 중복 제거</strong></p>
</blockquote>
<ul>
<li>컨트롤러에서 지정하는 뷰 이름에 중복이 있는 것을 확인</li>
<li>컨트롤러는 <strong>뷰의 논리 이름</strong>을 반환하고, 실제 물리 위치의 이름은 프론트 컨트롤러에서 처리하도록 단순화 하자.</li>
<li>이렇게 해둘 경우 향후 뷰 폴더 위치가 함께 이동해도 프론트 컨트롤러만 고치면 된다.</li>
<li><code>/WEB-INF/views/new-form.jsp</code> -&gt; new-form</li>
<li><code>/WEB-INF/views/save-result.jsp</code> -&gt; save-result</li>
<li><code>/WEB-INF/views/members.jsp</code> -&gt; members</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/7e2dc51a-4599-4c0e-8688-f1c87b40ae50/image.png" /></p>
<blockquote>
<p>지금까지 컨트롤러에서 서블릿에 종속적인 HttpServletRequest를 사용, 그리고 Model도 <code>request.setAttribute</code>를 통해 데이터를 저장하고 뷰에 전달하였음. 서블릿의 종속성을 제거하기 위해 Model을 직접 만들고, 추가로 View 이름까지 전달하는 객체를 만들기</p>
</blockquote>
<h3 id="뷰-리졸버">뷰 리졸버</h3>
<ul>
<li>컨트롤러가 반환한 논리 뷰 이름을 실제 물리 뷰 경로로 변경</li>
<li>실제 물리 경로가 있는 MyView 객체를 반환</li>
<li>논리 뷰 이름 : <code>members</code></li>
<li>물리 뷰 경로 : <code>/WEB-INF/views/members.jsp</code></li>
</ul>
<blockquote>
<p><code>view.render(mv.getModel(), request, response)</code></p>
</blockquote>
<ul>
<li>뷰 객체를 통해 HTML 화면을 렌더링</li>
<li>뷰 객체의 <code>render()</code>는 모델 정보도 함께 받는다.</li>
<li>JSP는 <code>request.getAttribute()</code>로 데이터를 조회하기 때문에, 모델의 데이터를 꺼내서 <code>request.setAttribute()</code>로 담아둔다.</li>
<li>JSP로 포워드 해서 JSP를 렌더링 한다.</li>
</ul>
<hr />
<h2 id="v4-버전">v4 버전</h2>
<ul>
<li>단순하고 실용적인 컨트롤러</li>
</ul>
<blockquote>
<p>앞서 만든 v3 컨트롤러는 서블릿의 종속성을 제거하고 뷰 경로의 중복을 제거하는 등, 잘 설계된 컨트롤러이다. 그런데 실제 컨트롤러 인터페이스를 구현하는 개발자 입장에서는 항상 ModelView 객체를 생성하고 반환해야 하는 부분이 조금은 번거롭다. 좋은 프레임워크는 아키텍처도 중요하지만, 그와 더불어 실제 개발하는 개발자가 단순하고 편리하게 사용할 수 있어야 한다. -&gt; <code>실용성과 연관</code> </p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/7da9f4b1-3856-4757-9c7f-80365de6319b/image.png" /></p>
<ul>
<li>기본적인 구조는 v3과 같지만, 대신 컨트롤러가 ModelView를 반환하지 않고, ViewName만 반환한다.</li>
</ul>
<blockquote>
<p><strong>v4 정리</strong></p>
</blockquote>
<ul>
<li>이번 버전의 컨트롤러는 매우 단순하고 실용적</li>
<li>기존  구조에서 모델을 파라미터로 넘기고, 뷰의 논리 이름을 반환한다는 작은 아이디어를 적용했을 뿐인데, 컨트롤러를 구현하는 개발자 입장에서 보면 이제 군더더기 없는 코드를 작성할 수 있음.</li>
<li>중요한 사실은 여기까지 발전해 온 것이 한번에 온 것이 아니라 프레임워크가 점진적으로 발전했다는 것.</li>
<li><strong>프레임워크나 공통 기능이 수고로워야 사용하는 개발자가 편리해진다.</strong></li>
</ul>
<hr />
<h2 id="v5-어댑터-패턴">v5 어댑터 패턴</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/8dbf1fbc-5840-4858-b530-313603f78b4c/image.png" /></p>
<h3 id="핸들러-어댑터">핸들러 어댑터</h3>
<blockquote>
<p>중간에 어댑터 역할을 하는 어댑터가 추가되었는데 이름이 핸들러 어댑터이다. 여기서 어댑터 역할을 해주는 덕분에 다양한 종류의 컨트롤러를 호출할 수 있다.</p>
</blockquote>
<h3 id="핸들러">핸들러</h3>
<blockquote>
<p>컨트롤러의 이름을 더 넓은 범위인 핸들러로 변경했다. 그 이유는 이제 어댑터가 있기 때문에 꼭 컨트롤러의 개념 뿐만 아니라 어떠한 것이든 해당하는 종류의 어댑터만 있으면 다 처리할 수 있기 때문이다.</p>
</blockquote>
<p><strong>어댑터는 어떻게 구현해야 할까?</strong></p>
<pre><code class="language-java">public interface MyHandlerAdapter {
    boolean supports(Object handler);
    ModelView handle(HttpServletRequest request, HttpServletResponse response, 
    Object handler) throws ServletException, IOException;
}</code></pre>
<blockquote>
<p><code>boolean supports(Object handler)</code></p>
</blockquote>
<ul>
<li>handler는 컨트롤러를 말한다.</li>
<li>어댑터가 해당 컨트롤러를 처리할 수 있는지 판단하는 메서드다.</li>
</ul>
<blockquote>
<p><code>ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler</code></p>
</blockquote>
<ul>
<li>어댑터는 실제 컨트롤러를 호출하고, 그 결과로 ModelView를 반환해야 한다.</li>
<li>실제 컨트롤러가 ModelView를 반환하지 못하면, 어댑터가 ModelView를 직접 생성해서라도 반환해야한다.</li>
<li>이전에는 프론트 컨트롤러가 실제 컨트롤러를 호출했지만 이제는 이 어댑터를 통해서 실제 컨트롤러가 호출된다.</li>
</ul>
<h3 id="controller-v3를-지원하는-어댑터">Controller V3를 지원하는 어댑터</h3>
<pre><code class="language-java">public class ControllerV3HandlerAdapter implements MyHandlerAdapter {
    @Override
     public boolean supports(Object handler) {
         return (handler instanceof ControllerV3);
    }

    @Override
     public ModelView handle(HttpServletRequest request, HttpServletResponse 
    response, Object handler) {
        ControllerV3 controller = (ControllerV3) handler;
         Map&lt;String, String&gt; paramMap = createParamMap(request);
         ModelView mv = controller.process(paramMap);
         return mv;
    }
     private Map&lt;String, String&gt; createParamMap(HttpServletRequest request) {
         Map&lt;String, String&gt; paramMap = new HashMap&lt;&gt;();
        request.getParameterNames().asIterator()
                .forEachRemaining(paramName -&gt; paramMap.put(paramName, 
        request.getParameter(paramName)));
         return paramMap;
    }
 }</code></pre>
<p>이제 이 어댑터를 분석해보자.</p>
<pre><code class="language-java"> public boolean supports(Object handler) {
     return (handler instanceof ControllerV3);
 }</code></pre>
<ul>
<li>Controller V3를 처리할 수 있는 어댑터</li>
<li>Object 타입인 이유는 서로 다른 타입이 들어와도 그 타입에 맞게 작동할 핸들러를 호출해주는 역할을 하는게 어댑터 핸들러 이기 때문이다.</li>
</ul>
<pre><code class="language-java"> ControllerV3 controller = (ControllerV3) handler;
 Map&lt;String, String&gt; paramMap = createParamMap(request);
 ModelView mv = controller.process(paramMap);
 return mv;</code></pre>
<ul>
<li>handler를 컨트롤러 V3로 변환한 다음에 V3 형식에 맞도록 호출한다.</li>
<li>supports()를 통해 ControllerV3만 지원하기 때문에 타입 변환은 걱정없이 실행해도 된다.</li>
<li>Controller V3는 ModelView를 반환하므로 그대로 ModelView를 반환하면 된다.</li>
</ul>
<h3 id="컨트롤러---핸들러">컨트롤러 -&gt; 핸들러</h3>
<blockquote>
<p><strong>Controller -&gt; Handler</strong></p>
</blockquote>
<ul>
<li>이전에는 컨트롤러를 직접 매핑해서 사용했다.</li>
<li>그런데 이제는 어댑터를 사용하기 때문에, 컨트롤러 뿐만 아니라 어댑터가 지원하기만 하면, 어떤 것이라도 URL에 매핑해서 사용할 수 있다. </li>
<li>그래서 이름을 컨트롤러에서 더 넓은 범위의 핸들러로 변경했다.</li>
</ul>
<hr />
<h2 id="v4를-위한-어댑터까지-추가한-후-제일-중요한-부분">V4를 위한 어댑터까지 추가한 후 제일 중요한 부분</h2>
<h3 id="어댑터-변환">어댑터 변환</h3>
<pre><code class="language-java">ModelView mv = new ModelView(viewName);
mv.setModel(model);

return mv;</code></pre>
<ul>
<li>어댑터에서 이 부분이 단순하지만 중요한 부분이다.</li>
</ul>
<blockquote>
<p>어댑터가 호출하는 <code>ControllerV4</code>는 뷰의 이름을 반환한다. 그런데 어댑터는 뷰의 이름이 아니라 <code>ModelView</code>를 만들어서 반환해야 한다. 여기서 어댑터가 꼭 필요한 이유가 나온다.
ControllerV4는 뷰의 이름을 반환했지만, 어댑터는 이것을 ModelView로 만들어서 형식을 맞추어 반환한다. 마치 110v 전기 콘센트를 220v 전기 콘센트로 변경하는 것 과 같다.</p>
</blockquote>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>