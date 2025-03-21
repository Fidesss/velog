<h1 id="요청-매핑">요청 매핑</h1>
<h2 id="매핑-정보">매핑 정보</h2>
<blockquote>
<p> <strong>@RestController</strong></p>
</blockquote>
<ul>
<li>@Controller는 반환 값이 String이면 뷰 이름으로 인식된다. 그래서 뷰를 찾고 뷰가 렌더링 ㅗ딘다.</li>
<li>@RestController는 반환 값으로 뷰를 찾는 것이 아니라 ,HTTP 메시지 바디에 바로 입력한다.</li>
</ul>
<hr />
<p><strong>@RequestMapping(&quot;/hello-basic&quot;)</strong></p>
<ul>
<li>/hello-basic : URL 호출이 오면 이 메서드가 실행되도록 매핑한다.</li>
<li>대부분의 속성을 <code>배열[]</code> 로 제공하기 때문에 다중 설정이 가능하다. <code>[&quot;/hello-basic&quot;, &quot;/hello-go&quot;]</code></li>
</ul>
<p>참고 </p>
<blockquote>
<p>스프링 부트 3.0 이전</p>
</blockquote>
<ul>
<li>스프링은 다음 URL 요청들을 같은 요청으로 매핑</li>
<li>매핑 : <code>/hello-basic</code></li>
<li>URL 요청 : <code>/hello-basic, /hello-basic/</code></li>
</ul>
<hr />
<p>스프링 부트 3.0 이후</p>
<ul>
<li>마지막의 <code>/</code>를 유지한다.</li>
<li>매핑 : <code>/hello-basic</code> -&gt; URL 요청 : <code>/hello-basic</code></li>
<li>매핑 : <code>/hello-basic/</code> -&gt; URL 요청 : <code>/hello-basic/</code></li>
</ul>
<h3 id="http-메서드">HTTP 메서드</h3>
<ul>
<li><code>@RequestMapping</code>에 method 속성으로 HTTP 메서드를 지정하지 않으면 HTTP 메서드와 무관하게 호출된다.</li>
<li>모두허용 : GET, HEAD, POST, PUT, PATCH, DELETE</li>
</ul>
<h4 id="http-메서드-매핑">HTTP 메서드 매핑</h4>
<pre><code class="language-java">/**
 * method 특정 HTTP 메서드 요청만 허용
 * GET, HEAD, POST, PUT, PATCH, DELETE
 */
 @RequestMapping(value = &quot;/mapping-get-v1&quot;, method = RequestMethod.GET)
 public String mappingGetV1() {
    log.info(&quot;mappingGetV1&quot;);
     return &quot;ok&quot;;
 }</code></pre>
<ul>
<li>만약 여기에 POST 요청을 하면 스프링 MVC는 HTTP 405 상태코드 (Method Not Allowed)를 반환</li>
</ul>
<h4 id="http-메서드-매핑-축약">HTTP 메서드 매핑 축약</h4>
<pre><code class="language-java"> /**
 * 편리한 축약 애노테이션 (코드보기)
 * @GetMapping
 * @PostMapping
 * @PutMapping
 * @DeleteMapping
 * @PatchMapping
 */
 @GetMapping(value = &quot;/mapping-get-v2&quot;)
 public String mappingGetV2() {
    log.info(&quot;mapping-get-v2&quot;);
     return &quot;ok&quot;;
 }</code></pre>
<ul>
<li>HTTP 메서드를 축약한 애노테이션을 사용하는 것이 더 직관적이다. 위의 코드를 확인해보면 <code>@RequestMapping</code>과 <code>method</code>를 지정해서 사용하는 것을 확인할 수 있다.</li>
</ul>
<h4 id="pathvariable경로-변수-사용">PathVariable(경로 변수) 사용</h4>
<pre><code class="language-java"> /**
 * PathVariable 사용
 * 변수명이 같으면 생략 가능
 * @PathVariable(&quot;userId&quot;) String userId -&gt; @PathVariable String userId
 */
 @GetMapping(&quot;/mapping/{userId}&quot;)
 public String mappingPath(@PathVariable(&quot;userId&quot;) String data) {
    log.info(&quot;mappingPath userId={}&quot;, data);
     return &quot;ok&quot;;
 }</code></pre>
<ul>
<li>최근 HTTP API는 다음과 같은 리소스 경로에 식별자를 넣는 스타일을 선호<ul>
<li><code>/mapping/userA</code></li>
<li><code>/users/1</code></li>
<li><code>@RequestMapping</code>은 URL 경로를 템플릿화 할 수 있는데, <code>@PathVariable</code>을 사용하면 매칭 되는 부분을 편리하게 조회할 수 있다.</li>
<li><code>@PathVariable</code>의 이름과 파라미터 이름이 같으면 생략 가능</li>
</ul>
</li>
</ul>
<h4 id="pathvariable-사용---다중">PathVariable 사용 - 다중</h4>
<pre><code class="language-java">/**
 * PathVariable 사용 다중
 */
 @GetMapping(&quot;/mapping/users/{userId}/orders/{orderId}&quot;)
 public String mappingPath(@PathVariable String userId, @PathVariable Long 
orderId) {
    log.info(&quot;mappingPath userId={}, orderId={}&quot;, userId, orderId);
     return &quot;ok&quot;;
 }</code></pre>
<ul>
<li>url에서 넘어온 PathVariable을 위와같이 다중으로 사용해서 받아올 수 있다.</li>
</ul>
<h4 id="특정-파라미터-조건-매핑">특정 파라미터 조건 매핑</h4>
<pre><code class="language-java">/**
 * 파라미터로 추가 매핑
 * params=&quot;mode&quot;,
 * params=&quot;!mode&quot;
 * params=&quot;mode=debug&quot;
 * params=&quot;mode!=debug&quot; (! = )
 * params = {&quot;mode=debug&quot;,&quot;data=good&quot;}
 */
 @GetMapping(value = &quot;/mapping-param&quot;, params = &quot;mode=debug&quot;)
 public String mappingParam() {
    log.info(&quot;mappingParam&quot;);
     return &quot;ok&quot;;
 }</code></pre>
<ul>
<li>특정 파라미턱가 있거나 없는 조건을 추가할 수 있다. 잘 사용하지느 않음 -&gt; 공부용으로 알고만 있자.</li>
</ul>
<h4 id="특정-헤더-조건-매핑">특정 헤더 조건 매핑</h4>
<pre><code class="language-java"> /**
 * 특정 헤더로 추가 매핑
 * headers=&quot;mode&quot;,
 * headers=&quot;!mode&quot;
 * headers=&quot;mode=debug&quot;
 * headers=&quot;mode!=debug&quot; (! = )
 */
 @GetMapping(value = &quot;/mapping-header&quot;, headers = &quot;mode=debug&quot;)
 public String mappingHeader() {
    log.info(&quot;mappingHeader&quot;);
     return &quot;ok&quot;;
 }</code></pre>
<ul>
<li>파라미터 매핑과 비슷하지만, HTTP 헤더를 사용</li>
<li><strong>POSTMAN</strong>으로 테스트할 경우 편함. (아래의 코드도 모두 postman에서 테스트)&gt; POSTMAN headers 정보에서 mode &gt; debug를 추가하면 정상적으로 log가 찍히는 것을 확인할 수 있다.</li>
</ul>
<h4 id="미디어-타입-조건-매핑---http-요청-content-type-consume">미디어 타입 조건 매핑 - HTTP 요청 Content-type, consume</h4>
<pre><code class="language-java">/**
 * Content-Type 헤더 기반 추가 매핑 Media Type
 * consumes=&quot;application/json&quot;
 * consumes=&quot;!application/json&quot;
 * consumes=&quot;application/*&quot;
 * consumes=&quot;*\/*&quot;
 * MediaType.APPLICATION_JSON_VALUE
 */
 @PostMapping(value = &quot;/mapping-consume&quot;, consumes = &quot;application/json&quot;)
 public String mappingConsumes() {
    log.info(&quot;mappingConsumes&quot;);
     return &quot;ok&quot;;
 }</code></pre>
<ul>
<li>HTTP 요청의 Content-Type 헤더를 기반으로 미디어 타입으로 매핑한다.</li>
<li>만약 맞지 않으면 HTTP 415 상태코드 (Unsupported Media Type)을 반환한다.</li>
</ul>
<p>예시</p>
<blockquote>
<p>consumes = &quot;text/plain&quot;
consumes = {&quot;text/plain&quot; &quot;application/*&quot;}
consumes = MediaType.TEXT_PLAIN_VALUE</p>
</blockquote>
<h4 id="미디어-타입-조건-매핑---http-요청-accept-produce">미디어 타입 조건 매핑 - HTTP 요청 Accept, produce</h4>
<pre><code class="language-java"> /**
 * Accept 헤더 기반 Media Type
 * produces = &quot;text/html&quot;
 * produces = &quot;!text/html&quot;
 * produces = &quot;text/*&quot;
 * produces = &quot;*\/*&quot;
 */
 @PostMapping(value = &quot;/mapping-produce&quot;, produces = &quot;text/html&quot;)
 public String mappingProduces() {
    log.info(&quot;mappingProduces&quot;);
     return &quot;ok&quot;;
 }</code></pre>
<ul>
<li>HTTP 요청의 Accept 헤더를 기반으로 미디어 타입으로 매핑한다.</li>
<li>만약 맞지 않으면 HTTP 406 상태코드 (Not Acceptable)을 반환한다.</li>
</ul>
<p>예시</p>
<blockquote>
<p> produces = &quot;text/plain&quot;
produces = {&quot;text/plain&quot;, &quot;application/*&quot;}
produces = MediaType.TEXT_PLAIN_VALUE
 produces = &quot;text/plain;charset=UTF-8&quot;</p>
</blockquote>
<hr />
<h3 id="요청-매핑---api-예시">요청 매핑 - API 예시</h3>
<blockquote>
<ul>
<li>회원 관리 API(예시)</li>
</ul>
</blockquote>
<table>
<thead>
<tr>
<th>API 목록</th>
<th>HTTP METHOD</th>
<th>URL</th>
</tr>
</thead>
<tbody><tr>
<td>회원 목록 조회</td>
<td>GET</td>
<td>/users</td>
</tr>
<tr>
<td>회원 등록</td>
<td>POST</td>
<td>/users</td>
</tr>
<tr>
<td>회원 조회</td>
<td>GET</td>
<td>/users/{userId}</td>
</tr>
<tr>
<td>회원 수정</td>
<td>PATCH</td>
<td>/users/{userId}</td>
</tr>
<tr>
<td>회원 삭제</td>
<td>DELETE</td>
<td>/users/{userId}</td>
</tr>
</tbody></table>
<pre><code class="language-java">@RestController
@RequestMapping(&quot;/mapping/users&quot;)
public class MappingClassController {

    @GetMapping
    public String user() {
        return &quot;get users&quot;;
    }

    @PostMapping
    public String addUser() {
        return &quot;post user&quot;;
    }

    @GetMapping(&quot;/{userId}&quot;)
    public String findUser(@PathVariable String userId) {
        return &quot;get userId = &quot; + userId;
    }

    @PatchMapping(&quot;/{userId}&quot;)
    public String updateUser(@PathVariable String userId) {
        return &quot;update userId = &quot; + userId;
    }

    @DeleteMapping(&quot;/{userId}&quot;)
    public String deleteUser(@PathVariable String userId) {
        return &quot;delete userId = &quot; + userId;
    }
}</code></pre>
<blockquote>
<p>이렇게 Spring에서 http 요청을 받았을때 어떻게 request요청을 받는지 그리고 request에 어떠한 제약을 부여하고 제약이 맞았을 경우에만 요청을 받아들일수 있는지, 기본적인 형태의 HTTP METHOD 요청이 오면 작동하는 API를 가볍게 만들어 보았다.</p>
</blockquote>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>