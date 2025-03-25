<h2 id="http-응답---정적-리소스-뷰-템플릿">HTTP 응답 - 정적 리소스, 뷰 템플릿</h2>
<ul>
<li>정적 리소스<ul>
<li>예) 웹 브라우저에 정적인 HTML, css. js를 제공할 때는, 정적 리소스를 사용</li>
</ul>
</li>
<li>뷰 템플릿 사용<ul>
<li>예) 웹 브라우저에 동적인 HTML을 제공할 때는 뷰 템플릿을 사용</li>
</ul>
</li>
<li>HTTP 메시지 사용<ul>
<li>HTTP API를 제공하는 경우에는 HTML이 아니라 데이터를 전달해야 하므로, HTTP 메시지 바디에 JSON 같은 형식으로 데이터를 실어 보낸다.</li>
</ul>
</li>
</ul>
<h3 id="정적-리소스">정적 리소스</h3>
<ul>
<li>스프링 부트는 클래스패스의 다음 디렉토리에 있는 정적 리소스를 제공한다.</li>
<li><code>/static</code>, <code>/public</code>, <code>/resource</code>, <code>META-INF/resources</code></li>
<li><code>src/main/resources</code>는 리소를 보관하는 곳이고, 또 클래스패스의 시작 경로이다. 따라서 다음 디렉토리에 리소를 넣어두면 스프링 부트가 정적 리소스로 서비스를 제공한다.<h4 id="정적-리소스-경로">정적 리소스 경로</h4>
</li>
<li><code>src/main/resources/static</code> 이다.</li>
<li>다음 경로에 파일이 들어있을 경우<ul>
<li><code>src/main/resources/static/basic/hello-form.html</code></li>
</ul>
</li>
<li>웹 브라이저에서 다음과 같이 실행하면 된다.<ul>
<li><code>http://localhost:8000/basic/hello-form.html</code></li>
</ul>
</li>
<li>정적 리소스는 해당 파일을 변경 없이 그대로 서비스하는 것이다.</li>
</ul>
<h4 id="뷰-템플릿">뷰 템플릿</h4>
<ul>
<li>뷰 템플릿을 거쳐서 HTML이 생성되고, 뷰가 응답을 만들어서 전달한다.</li>
<li>일반적으로 HTML을 동적으로 생성하는 용도를 사용하지만, 다른 것들도 가능하다. 뷰 템플릿이 만들 수 있는 것이라면 뭐든지 가능</li>
<li>스프링 부트는 기본 뷰 템플릿 경로를 제공한다.<ul>
<li>뷰 템플릿 경로 : <code>src/main/resources/templates</code><h4 id="뷰-템플릿을-호출하는-컨트롤러">뷰 템플릿을 호출하는 컨트롤러</h4>
<pre><code class="language-java">@Controller
public class ResponseViewController {
@RequestMapping(&quot;/response-view-v1&quot;)
 public ModelAndView responseViewV1() {
     ModelAndView mav = new ModelAndView(&quot;response/hello&quot;)
            .addObject(&quot;data&quot;, &quot;hello!&quot;);
     return mav;
}
@RequestMapping(&quot;/response-view-v2&quot;)
 public String responseViewV2(Model model) {
    model.addAttribute(&quot;data&quot;, &quot;hello!!&quot;);
     return &quot;response/hello&quot;;
}
@RequestMapping(&quot;/response/hello&quot;)
public void responseViewV3(Model model) {
    model.addAttribute(&quot;data&quot;, &quot;hello!!&quot;);
}
}</code></pre>
<h4 id="string을-반환하는-경우---view-or-http-메시지">String을 반환하는 경우 - View or HTTP 메시지</h4>
</li>
</ul>
</li>
<li><code>@ResponseBody</code>가 없으면 <code>response/hello</code>로 뷰 리졸버가 실행되어서 뷰를 찾고, 렌더링 한다.</li>
<li><code>@ResponseBody</code>가 있으면 뷰 리졸버를 실행하지 않고, HTTP 메시지 바디에 직접 <code>response/hello</code>라는 문자가 입력된다.</li>
<li>여기서는 뷰의 논리 이름인 <code>response/hello</code>를 반호나하면 다음 경로의 뷰 템플릿이 렌더링 되는 것을 확인할 수 있다.<ul>
<li>실행 : <code>templates/response/hello.html</code><h4 id="void를-반환하는-경우">Void를 반환하는 경우</h4>
</li>
</ul>
</li>
<li><code>@Controller</code>를 사용하고, <code>HttpServletResponse</code>, <code>OutputStream(Writer)</code>같은 HTTP 메시지 바디를 처리하는 파리미터가 없으면 요청 URL을 참고해서 논리 뷰 이름으로 사용<ul>
<li>요청 URL : <code>/response/hello</code></li>
<li>실행 : <code>templates/response/hello.html</code></li>
</ul>
</li>
<li>참고롤 이 방식은 명시성이 너무 떨어지고 이렇게 딱 맞는 경우도 많이 없어서, 권장 X</li>
</ul>
<h4 id="http-메시지">HTTP 메시지</h4>
<ul>
<li><code>@ResponseBody</code>, <code>HttpEntity</code>를 사용하면, 뷰 템플릿을 사용하는 것이 아니라, HTTP 메시지 바디에 직접 응답 데이터를 출력할 수 있다.</li>
</ul>
<h2 id="http-응답-http-api-메시지-바디에-직접-입력">HTTP 응답 HTTP API, 메시지 바디에 직접 입력</h2>
<ul>
<li>HTTP API를 제공하는 경우에는 HTML이 아니라 데이터를 전달해야 하므로, HTTP 메시지 바디에 JSON 같은 형식으로 데이터를 실어 보낸다.<blockquote>
<p><strong>참고</strong></p>
<ul>
<li>HTML이나 뷰 템플릿을 사용해도 HTTP 응답 메시지 바디에 HTML 데이터가 담겨서 전달된다. 여기서 설명하는 내용은 정적 리소스나 뷰 템플릿을 거치지 않고, 직접 HTTP 응답 메시지를 전달하는 경우이다.</li>
</ul>
</blockquote>
</li>
</ul>
<h3 id="rescontroller">@ResController</h3>
<ul>
<li><code>@Controller</code> 대신에 <code>@ResController</code> 애노테이션을 사용하면, 해당 컨트롤러에 모두 <code>@ResponseBody</code>가 적용되는 효과가 있다. 따라서 뷰 템플릿을 사용하는 것이 아니라, HTTP 메시지 바디에 직접 데이터를 입력한다. 이름 그대로 Rest API(HTTP API)를 만들 때 사용하는 컨트롤러이다.</li>
<li><code>@ResponseBody</code>는 클래스 레벨에 두면 전체 메서드에 적용된다.</li>
<li><code>@RestController</code> 애노테이션 안에 <code>@ResponseBody</code>가 적용되어 있다.</li>
</ul>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>