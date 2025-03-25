<h1 id="요청-매핑-헨들러-어뎁터">요청 매핑 헨들러 어뎁터</h1>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/4b464a80-e863-46f1-b881-8dece23718ac/image.png" /></p>
<ul>
<li>과연 HTTP 메시지 컨버터는 스프링 MVC 구조 중 어디에서 사용될까?</li>
<li>모든 의문은 애노테이션 기반의 컨트롤러 즉 <code>@RequestMapping</code>을 처리하는 핸들러 어뎁터인 <code>@RequestMappingHandlerAdapter</code>(요청 매핑 헨들러 어뎁터)에 있다.</li>
</ul>
<h3 id="requestmaapinghandleradapter-동작-방식">RequestMaapingHandlerAdapter 동작 방식</h3>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/af36a3af-ac31-4419-ad8a-131cd44c5bfc/image.png" /></p>
<h4 id="arguemntresolver">ArguemntResolver</h4>
<blockquote>
<p><strong>ArgumentResolver</strong> 덕분에 우리는 애노테이션 기반의 컨트롤러 -&gt; 매우 다양한 파라미터 사용가능</p>
</blockquote>
<ul>
<li>ex) <code>HttpServletRequest</code>, <code>Model</code>, <code>@RequestParam</code>, <code>@ModelAttriburte</code>같은 애노테이션, <code>@RequestBody</code>, <code>HttpEntity</code> 같은 HTTP 메시지를 처리하는 부분</li>
<li>애노테이션 기반 컨트롤러를 처리하는 <code>@RequestMappingAdapter</code>는 바로 이 <code>ArgumentResolver</code>를 호출해서 컨트롤러(핸들러)가 필요로 하는 다양한 파라미터의 값(객체)을 생성한다. 그리고 이렇게 파라미터의 값이 모두 준비되면 컨트롤러를 호출하면서 값을 넘겨준다.</li>
</ul>
<blockquote>
<p>참고</p>
<ul>
<li>가능한 파라미터 목록 공식 메뉴얼</li>
</ul>
</blockquote>
<ul>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/arguments.html">https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/arguments.html</a></li>
</ul>
<hr />
<h3 id="동작-방식">동작 방식</h3>
<p>정확히는 <code>HandlerMethodArgumentResolver</code>인데 줄여서 <code>ArgumentResolver</code>라고 부른다.</p>
<pre><code class="language-java">public interface HandlerMethodArgumentResolver {

    boolean supportsParameter(MethodParameter parameter);
    @Nullable
    Object resolveArgument(MethodParameter parameter, @Nullable 
    ModelAndViewContainer mavContainer,
     NativeWebRequest webRequest, @Nullable WebDataBinderFactory 
    binderFactory) throws Exception;
}</code></pre>
<h4 id="동작방식">동작방식</h4>
<p><code>ArgumentResolver</code>의 <code>supportsParameter()</code>를 호출해서 해당 파라미터를 지원하는지 체크하고, 지원하면 <code>resolverArgument()</code>를 호출해서 실제 객체를 생성한다. 그리고 이렇게 생성된 객체가 컨트롤러 호출시 넘어가는 것.</p>
<h4 id="returnvaluehandler">ReturnValueHandler</h4>
<ul>
<li><code>HandlerMethodReturnValueHandler</code>를 줄여서 <code>ReturnValueHandler</code>라 부른다. <code>ArgumentResolver</code>와 비슷한데, 이것을 응답 값을 변환하고 처리한다.</li>
<li>컨트롤러에서 String으로 뷰 이름을 반환해도, 동작하는 이유가 바로 ReturnValueHandler 때문이다.</li>
</ul>
<blockquote>
<p>참고</p>
</blockquote>
<ul>
<li>가능한 응답 값 목록</li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/return-types.html">https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/return-types.html</a></li>
</ul>
<h1 id="http-메시지-컨버터">HTTP 메시지 컨버터</h1>
<h3 id="http-메시지-컨버터-위치">HTTP 메시지 컨버터 위치</h3>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/7e689b96-c448-4f31-963b-55b2fa93fb53/image.png" /></p>
<p>HTTP 메시지 컨버터는 어디쯤에 위치할까?</p>
<ul>
<li>HTTP 메시지 컨버터는 사용하는 <code>@RequestBody</code>도 컨트롤러가 필요로 하는 파라미터의 값에 사용된다.</li>
<li><code>@ResponseBody</code>의 경우도 컨트롤러의 반환 값을 이용한다.</li>
</ul>
<blockquote>
<p><strong>요청의 경우</strong> <code>@RequestBody</code>를 처리하는 <code>ArgumentResolver</code>가 있고, <code>HttpEntity</code>를 처리하는 <code>ArgumentResolver</code>가 있다. 이 <code>ArgumentResolver</code>들이 HTTP 메시지 컨버터를 사용해서 필요한 객체를 생성하는 것</p>
</blockquote>
<blockquote>
<p><strong>응답의 경우</strong> <code>@ResponseBody</code>와 <code>HttpEntity</code>를 처리하는 <code>ReturnValueHandler</code>가 있다. 그리고 여기에서  HTTP메시지 컨버터를 호출해서 응답 결과를 만든다.</p>
</blockquote>
<blockquote>
<p>스프링 MVC는 <code>@RequestBody</code> <code>@ResponseBody</code>가 있으면 <code>RequestResponseBodyMethodProcessor(ArgumentResolver, ReturnValueHandler 둘다 구현) HttpEntity</code>가 있으면  <code>HttpEntityMethodProcessor(ArgumentResolver, ReturnValueHandler 둘다 구현)</code>를 사용한다.</p>
</blockquote>
<blockquote>
<p>참고</p>
</blockquote>
<ul>
<li><code>HttpMessageConverter</code>를 구현한 클래스를 한번 확인해보기</li>
</ul>
<h3 id="확장">확장</h3>
<ul>
<li>스프링은 다음을 모두 인터페이스로 제공, 따라서 필요하면 언제든지 기능을 확장할 수 있다.<ul>
<li><code>HandlerMethodArgumentResolver</code></li>
<li><code>HandlerMethodReturnValueHandler</code></li>
<li><code>HttpMessageConverter</code></li>
</ul>
</li>
</ul>
<blockquote>
<ul>
<li>사실상 스프링이 필요한 대부분을 제공함.</li>
</ul>
</blockquote>
<ul>
<li>기능 확장은 <code>WebMvcConfigurer</code>를 상속 받아서 스프링 빈으로 등록</li>
<li>실제 자주 사용하지는 않으니 실제 기능 확장이 필요하면 <code>WebMvcConfigurer</code>검색해보자.</li>
</ul>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>