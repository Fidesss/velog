<h2 id="기본-헤더-조회">기본, 헤더 조회</h2>
<ul>
<li>애노테이션 기반의 스프링 컨트롤러는 다양한 파라미터를 지원한다.</li>
</ul>
<pre><code class="language-java">@Slf4j
@RestController
public class RestHeaderController {

    @RequestMapping(&quot;/headers&quot;)
    public String headers(HttpServletRequest request, HttpServletResponse response,
                          HttpMethod httpMethod,
                          Locale locale,
                          @RequestHeader MultiValueMap&lt;String, String&gt; headerMap,
                          @RequestHeader(&quot;host&quot;) String host,
                          @CookieValue(value = &quot;myCookie&quot;, required = false) String cookie) {

        log.info(&quot;request={}&quot;, request);
        log.info(&quot;response={}&quot;, response);
        log.info(&quot;httpMethod={}&quot;, httpMethod);
        log.info(&quot;locale={}&quot;, locale);
        log.info(&quot;headerMap={}&quot;, headerMap);
        log.info(&quot;header host={}&quot;, host);
        log.info(&quot;myCookie={}&quot;, cookie);
        return &quot;ok&quot;;

    }
}</code></pre>
<blockquote>
<ul>
<li>HttpServletRequest</li>
</ul>
</blockquote>
<ul>
<li><code>HttpServletResponse</code></li>
<li><code>HttpMethod</code> : HTTP 메서드를 조회한다. <code>org.springframework.http.HttpMethod</code></li>
<li><code>Locale</code> : Locale 정보를 조회한다.</li>
<li><code>@RequestHeader MultiValueMap&lt;String, String&gt; headerMap</code><ul>
<li>모든 HTTP 헤더를 <code>MultiValueMap</code> 형식으로 조회한다.</li>
</ul>
</li>
<li><code>@RequestHeader(&quot;host&quot;) String host</code><ul>
<li>특정 HTTP 헤더를 조회한다.</li>
<li>속성<ul>
<li>필수 값 여부 : <code>required</code></li>
<li>기본 값 속성 : <code>defaultValue</code></li>
</ul>
</li>
</ul>
</li>
<li><code>@CookieValue(value = &quot;myCookie&quot;, required = false) String cookie</code><ul>
<li>특정 쿠키를 조회한다.</li>
<li>속성<ul>
<li>필수 값 여부 : <code>required</code></li>
<li>기본 값 : <code>defaultValue</code></li>
</ul>
</li>
</ul>
</li>
</ul>
<h3 id="multivaluemap">MultiValueMap</h3>
<ul>
<li>Map과 유사한데, 하나의 키에 여러 값을 받을 수 있다.</li>
<li>HTTP header, HTTP 쿼리 파라미터와 같이 하나의 키에 여러 값을 받을 때 사용한다.<ul>
<li><code>keyA=value1&amp;keyA=value2</code> &gt; key A에 2개의 value가 담김</li>
</ul>
</li>
</ul>
<pre><code class="language-java"> MultiValueMap&lt;String, String&gt; map = new LinkedMultiValueMap();
 map.add(&quot;keyA&quot;, &quot;value1&quot;);
 map.add(&quot;keyA&quot;, &quot;value2&quot;);
 //[value1,value2]
 List&lt;String&gt; values = map.get(&quot;keyA&quot;);</code></pre>
<h3 id="slf4j">@Slf4j</h3>
<ul>
<li>이전 포스트에서 자세히 작성했었지만 쉽게 이야기하면 개발자가 편리하기 <code>log</code>를 사용하게 해주는 것을 도와준다.</li>
<li>아래 코드를 자동으로 생성하여 로그를 선언해준다.</li>
</ul>
<pre><code class="language-java">private static final org.slf4j.Logger log = 
org.slf4j.LoggerFactory.getLogger(RequestHeaderController.class);</code></pre>
<blockquote>
<p><strong>참고</strong></p>
</blockquote>
<ul>
<li><code>@Controller</code>의 사용 가능한 파라미터 목록은 아래의 공식 메뉴얼 확인</li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/arguments.html">https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/arguments.html</a></li>
</ul>
<hr />
<h2 id="쿼리-파라미터-html-form">쿼리 파라미터, HTML Form</h2>
<h3 id="http-요청-데이터-조회---개요">HTTP 요청 데이터 조회 - 개요</h3>
<blockquote>
<ul>
<li><strong>클라이언트에서 서버로 요청 데이터를 전달할 때는 주로 다음 3가지 방법을 사용</strong></li>
</ul>
</blockquote>
<ul>
<li>/url<strong>?username=hello&amp;age=20</strong></li>
<li>메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달</li>
<li>EX) 검색, 필터, 페이징등에서 많이 사용하는 방식<ul>
<li><strong>POST - HTML Form</strong></li>
</ul>
</li>
<li>content-type : application/x-www-form-urlencoded</li>
<li>메시지 바디에 쿼리 파라미터 형식으로 전달 username=hello&amp;age=20</li>
<li>예) 회원 가입, 상품 주문, HTML Form 사용<ul>
<li><strong>HTTP message body</strong>에 데이터를 직접 담아서 요청</li>
</ul>
</li>
<li>HTTP API에서 주로 사용, JSON, XML, TEXT</li>
<li>데이터 형식은 주로 JSON 사용</li>
<li>POST, PUT, PATCH</li>
</ul>
<p>HttpServletRequest의 request.getParameter()를 사용하면 다음 두가지 요청 파라미터를 조회할 수 있다.

GET, 쿼리 파라미터 전송</p>
<ul>
<li><code>http://localhost:8080/request-param?username=hello&amp;age=20</code></li>
</ul>
<p>POST, HTML Form 전송</p>
<ul>
<li><pre><code>POST /request-param ...
content-type: application/x-www-form-urlencoded
username=hello&amp;age=20 </code></pre></li>
</ul>
<p>GET 쿼리 파라미터 전송 방식이든, POST HTML Form 전송 방식이든 둘다 형식이 같으므로 구분없이 조회할 수 있다.</p>
<ul>
<li>이것을 간단하게 <strong>요청 파라미터 (request parameter) 조회</strong>라고 한다.</li>
</ul>
<hr />
<h3 id="이제부터-스프링으로-요청-파라미터를-조회해-보자">이제부터 스프링으로 요청 파라미터를 조회해 보자.</h3>


<pre><code class="language-java">/**
 * 반환 타입이 없으면서 이렇게 응답에 값을 직접 넣으면, view 조회 X
 */
@RequestMapping(&quot;/request-param-v1&quot;)
public void requestParamV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
    String username = request.getParameter(&quot;username&quot;);
    int age = Integer.parseInt(request.getParameter(&quot;age&quot;));

    log.info(&quot;username:{}, age:{}&quot;, username, age);

    response.getWriter().write(&quot;ok&quot;);
}</code></pre>
<ul>
<li>request.getParameter()<ul>
<li>여기서는 단순히 HttpServletRequest가 제공하는 방식으로 요청 파라미터를 조회</li>
</ul>
</li>
</ul>
<hr />
<pre><code class="language-java">/**
 * @RequestParam 사용
 * - 파라미터 이름으로 바인딩
 * @ResponseBody 추가
 * - View 조회를 무시하고, HTTP message body에 직접 해당 내용 입력
 */

@ResponseBody
@RequestMapping(&quot;/request-param-v2&quot;)
public String requestParamV2(@RequestParam(&quot;username&quot;) String memberName, @RequestParam(&quot;age&quot;) int memberAge) {
        log.info(&quot;username={}, age={}&quot;, memberName, memberAge);
        return &quot;ok&quot;;
    }</code></pre>
<ul>
<li><code>@RequestParam</code> : 파라미터 이름으로 바인딩</li>
<li><code>@ResponseBody</code> : View 조회를 무시하고, HTTP message body에 직접 해당 내용 입력</li>
</ul>
<p><strong>RequestParam의 name(value) 속성이 파라미터 이름으로 사용</strong></p>
<ul>
<li>@RequestParam(&quot;username&quot;) String memberName</li>
<li>-&gt; request.getParameter(&quot;username&quot;)</li>
</ul>
<hr />
<pre><code class="language-java">/**
 * @RequestParam 사용
 * HTTP 파라미터 이름이 변수 이름과 같으면 @RequestParam(name=&quot;xx&quot;) 생략 가능
*/
@ResponseBody
@RequestMapping(&quot;/request-param-v3&quot;)
public String requestParamV3(
    @RequestParam String username,
    @RequestParam int age) {
    log.info(&quot;username={}, age={}&quot;, username, age);
    return &quot;ok&quot;;
}</code></pre>
<ul>
<li>HTTP 파라미터 이름이 변수 이름과 같으면 <code>@RequestParam(name=&quot;xx&quot;)</code>생략 가능</li>
</ul>
<hr />
<pre><code class="language-java">/**
 * @RequestParam 사용
 * String, int 등의 단순 타입이면 @RequestParam 도 생략 가능
 */
@ResponseBody
@RequestMapping(&quot;/request-param-v4&quot;)
public String requestParamV4(String username, int age) {
    log.info(&quot;username={}, age={}&quot;, username, age);
    return &quot;ok&quot;;
}</code></pre>
<ul>
<li><code>String</code>, <code>int</code>, <code>Integer</code> 등의 단순 타입이면 <code>@RequestParam</code> 도 생략 가능</li>
</ul>
<blockquote>
<p><strong>주의</strong></p>
</blockquote>
<ul>
<li><code>@RequestParam</code> 애노테이션을 생략하면 스프링 MVC는 내부에서 <code>required=false</code>를 적용한다.</li>
</ul>
<hr />
<p><strong>참고</strong></p>
<ul>
<li>애노테이션을 완전히 생략해도 상관은 없지만, 너무 없는 것도 약간 과하다는 이야기를 들었다. </li>
<li><code>@RequestParam</code>이 있으면 명확하게 요청 파라미터에서 데이터를 읽는 다는 것을 파악할 수 있다.</li>
<li>스프링에 익숙하지 않은 사람일 경우 보자마자 이해를 하지못할 수도 있으니 주의해서 사용하자.</li>
</ul>
<hr />
<h3 id="파라미터-필수-여부---requestparamrequired">파라미터 필수 여부 - requestParamRequired</h3>
<pre><code class="language-java"> /**
  * @RequestParam.required /request-param-required -&gt; username이 없으므로 예외
  * &lt;p&gt;
  * 주의!
  * /request-param-required?username= -&gt; 빈문자로 통과
  * &lt;p&gt;
  * 주의!
  * /request-param-required
  * int age -&gt; null을 int에 입력하는 것은 불가능, 따라서 Integer 변경해야 함(또는 다음에 나오는
  * defaultValue 사용)
  */

@ResponseBody
@RequestMapping(&quot;/request-param-required&quot;)
public String requestParamRequired(
    @RequestParam(required = true) String username,
    @RequestParam(required = false) Integer age) {
    log.info(&quot;username={}, age={}&quot;, username, age);
    return &quot;ok&quot;;
}</code></pre>
<ul>
<li><p><code>@RequestParam.required</code></p>
<ul>
<li>파라미터 필수 여부</li>
<li>기본 값이 파라미터 필수(true)이다.</li>
</ul>
</li>
<li><p><code>/request-param-required 요청</code></p>
<ul>
<li>username이 없으므로 400예외 발생</li>
</ul>
</li>
</ul>
<p><strong>주의! 파라미터 이름만 사용</strong></p>
<ul>
<li><code>/request-param-required?username=</code></li>
<li>파라미터 이름만 있고 값이 없는 경우 -&gt; 빈 문자로 통과</li>
</ul>
<p><strong>주의! - 기본형(primitive)</strong>에 null 입력</p>
<ul>
<li><code>/request-param</code>요청</li>
<li><code>@RequestParam(required = false)</code> int age</li>
<li><code>null</code>을 <code>int</code>에 이볅하는 것은 불가능 (500 예외 발생) 따라서 <code>null</code>을 받을 수 있는 <code>Integer</code>로 변경하거나, 또는 <code>defaultValue</code>를 사용하자.</li>
</ul>
<pre><code class="language-java">/**
 * @RequestParam
 * - defaultValue 사용
 *
 * 참고: defaultValue는 빈 문자의 경우에도 적용
 * /request-param-default?username=
 */
 @ResponseBody
 @RequestMapping(&quot;/request-param-default&quot;)
 public String requestParamDefault(
        @RequestParam(required = true, defaultValue = &quot;guest&quot;) String username,
        @RequestParam(required = false, defaultValue = &quot;-1&quot;) int age) {

        log.info(&quot;username={}, age={}&quot;, username, age);
         return &quot;ok&quot;;
 }</code></pre>
<blockquote>
<p>파라미터에 값이 없는 경우 <code>defaultValue</code>를 사용하면 기본 값을 적용할 수 있다. 이미 기본 값이 있기 때문에 required는 의미가 없다. <code>defaultValue</code>는 빈 문자의 경우에도 설정한 기본 값이 적용된다. <code>/request-param-default?username</code> 이런식으로 보내면 default값이 적용되어 <code>request</code>를 보내게 된다.</p>
</blockquote>
<h3 id="파라미터를-map으로-조회하기---requestparammap">파라미터를 Map으로 조회하기 - requestParamMap</h3>
<pre><code class="language-java"> /**
 * @RequestParam Map, MultiValueMap
 * Map(key=value)
 * MultiValueMap(key=[value1, value2, ...]) ex) (key=userIds, value=[id1, id2])
 */
 @ResponseBody
 @RequestMapping(&quot;/request-param-map&quot;)
 public String requestParamMap(@RequestParam Map&lt;String, Object&gt; paramMap) {
    log.info(&quot;username={}, age={}&quot;, paramMap.get(&quot;username&quot;), 
    paramMap.get(&quot;age&quot;));
     return &quot;ok&quot;;
 }</code></pre>
<ul>
<li>파라미터를 Map, MultiValueMap으로 조회할 수 있다.<ul>
<li><code>@RequestParam Map</code>,<ul>
<li><code>Map(key=value)</code></li>
</ul>
</li>
<li><code>@RequestParam MultiValueMap</code><ul>
<li><code>MultiValueMap(key=[value1, value2, ... ] ex) (key=userIds, value=[id1, id2])</code></li>
</ul>
</li>
</ul>
</li>
</ul>
<p>파라미터의 값이 1개가 확실하다면 <code>Map</code>을 사용해도 되지만, 그렇지 않다면<code>MultiValueMap</code>을 사용하자. 하지만 거의 1개일 경우가 많기 때문에 <code>Map</code>을 주로 사용한다.</p>
<hr />
<h2 id="http-요청-파라미터---modelattribute">HTTP 요청 파라미터 - @ModelAttribute</h2>
<ul>
<li>실제 개발을 하게 될 경우 요청 파라미터를 받아서 필요한 객체를 만들고 객체에 값을 넣어주어야 한다.</li>
<li>스프링은 이 과정을 완전히 자동화 해주는 <code>@ModelAttribute</code> 기능을 제공한다.</li>
</ul>
<p>실습에 앞서 요청 파라미터를 바인딩 받을 객체 생성</p>
<pre><code class="language-java">//HelloData
import lombok.Data;
@Data
public class HelloData {
    private String username;
    private int age;
 }</code></pre>
<ul>
<li>롬복 <code>@Data</code><ul>
<li><code>@Getter</code>, <code>@Setter</code>,<code>@ToString</code>, <code>@EqualsAndHashCode</code>, <code>@RequiredArgsConstructor</code> 를 자동으로 적용해준다.</li>
</ul>
</li>
</ul>
<h4 id="modelattribute-적용---modelattributev1">@ModelAttribute 적용 - modelAttributeV1</h4>
<pre><code class="language-java">/**
 * @ModelAttribute 사용
 * 참고: model.addAttribute(helloData) 코드도 함께 자동 적용됨, 뒤에 model을 설명할 때 자세히
 설명
 */
@ResponseBody
@RequestMapping(&quot;/model-attribute-v1&quot;)
public String modelAttributeV1(@ModelAttribute HelloData helloData) {
    log.info(&quot;username ={}, age={}&quot;, helloData.getUsername(), helloData.getAge());
    return &quot;ok&quot;;
}</code></pre>
<ul>
<li><p>실행해보면 자동으로 <code>HelloData</code> 객체가 생성되고, 요청 파라미터의 값도 모두 들어가 있는것을 확인할 수 있다.</p>
</li>
<li><p>스프링 MVC는 <code>@ModelAttribute</code>가 있으면 다음을 실행한다.</p>
<ul>
<li><code>HelloData</code>객체를 생성한다.</li>
<li>요청 파라미터의 이름으로 <code>HelloData</code>객체의 프로퍼티를 찾는다. 그리고 해당 프로퍼티의 setter를 호출해서 파라미터의 값을 입력(바인딩)한다.</li>
<li>EX) 파라미터의 이름이 <code>username</code>이면 <code>setUsername()</code> 메서드를 찾아서 호출하면서 값을 입력</li>
</ul>
</li>
</ul>
<h3 id="프로퍼티">프로퍼티</h3>
<ul>
<li>객체에 <code>getUsername()</code>, <code>setUsername()</code> 메서드가 있으면, 이 객체는<code>username</code> 이라는 프로퍼티를 가지고 있음.</li>
<li><code>username</code> 프로퍼티 값을 변경하면 <code>setUsername()</code>이 호출되고, 조회하면 <code>getUsername()</code>이 호출된다.<pre><code>class HelloData{
  getUsername();
  setUsername();
}</code></pre></li>
</ul>
<h4 id="바인딩-오류">바인딩 오류</h4>
<ul>
<li><code>age=abc</code> 처럼 숫자가 들어가야 할 곳에 문자를 넣으면 <code>BindException</code>발생</li>
</ul>
<h4 id="modelattribute-생략---modelattributev2">@ModelAttribute 생략 - modelAttributeV2</h4>
<pre><code class="language-java">/**
 * @ModelAttribute 생략 가능
 * String, int 같은 단순 타입 = @RequestParam
 * argument resolver 로 지정해둔 타입 외 = @ModelAttribute
 */
@ResponseBody
@RequestMapping(&quot;/model-attribute-v2&quot;)
public String modelAttributeV2(HelloData helloData) {
    log.info(&quot;username={}, age={}&quot;, helloData.getUsername(), 
    helloData.getAge());
    return &quot;ok&quot;;
}</code></pre>
<ul>
<li>@ModelAttribute는 생략할 수 있다.</li>
<li>그런데 @RequestParam도 생략할 수 있으니 혼란이 발생할 수 있다.</li>
</ul>
<p>스프링은 해당 생략시 다음과 같은 규칙을 적용</p>
<ul>
<li><code>String</code>, <code>int</code>, <code>Integer</code> 같은 단순 타입 = <code>@RequestParam</code></li>
<li>나머지 = <code>@ModelAttribute</code> (argument resolver로 지정해둔 타입 외)</li>
</ul>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>