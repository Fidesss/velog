<h1 id="http-메시지-컨버터">HTTP 메시지 컨버터</h1>
<ul>
<li>뷰 템플릿으로 HTML을 생성해서 응답하는 것이 아니라, HTTP API처럼 JSON 데이터를 HTTP 메시지 바디에서 직접 읽거나 쓰는 경우 HTTP 메시지 컨버터를 사용하면 편리.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/8692608e-b77b-4f65-9e75-99372a140446/image.png" /></p>
<blockquote>
<p><code>@ResponseBody</code>를 사용</p>
<ul>
<li>HTTP의 BODY에 문자 내용을 직접 반환</li>
</ul>
</blockquote>
<ul>
<li><code>viewResolver</code> 대신에 <code>HttpMessageConverter</code>가 동작</li>
<li>기본 문자처리 : <code>StringHttpMessageConverter</code></li>
<li>기본 객체처리 : <code>MappingJackson2HttpMessageConverter</code></li>
<li>byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음</li>
</ul>
<blockquote>
<p><strong>참고</strong></p>
</blockquote>
<ul>
<li>응답의 경우 클라이언트의 HTTP Aceept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서 <code>HttpMessageConverter</code>가 선택된다.</li>
</ul>
<h3 id="스프링-mvc는-다음의-경우에는-http-메시지-컨버터를-적용한다">스프링 MVC는 다음의 경우에는 HTTP 메시지 컨버터를 적용한다.</h3>
<ul>
<li>HTTP 요청 : <code>@RequestBody</code>, <code>HttpEntity(RequestEntity)</code>,</li>
<li>HTTP 응답 : <code>@ResponseBody</code>, <code>HttpEntity(ResponseEntity)</code></li>
</ul>
<h4 id="http-메시지-컨버터-인터페이스">HTTP 메시지 컨버터 인터페이스</h4>
<pre><code class="language-java"> public interface HttpMessageConverter&lt;T&gt; {
     boolean canRead(Class&lt;?&gt; clazz, @Nullable MediaType mediaType);
     boolean canWrite(Class&lt;?&gt; clazz, @Nullable MediaType mediaType);
     List&lt;MediaType&gt; getSupportedMediaTypes();
     T read(Class&lt;? extends T&gt; clazz, HttpInputMessage inputMessage)
     throws IOException, HttpMessageNotReadableException;
     void write(T t, @Nullable MediaType contentType, HttpOutputMessage 
    outputMessage)
     throws IOException, HttpMessageNotWritableException;
 }</code></pre>
<blockquote>
<p>HTTP 메시지 컨버터는 HTTP 요청, HTTP 응답 둘 다 사용된다.</p>
</blockquote>
<ul>
<li><code>canRead()</code>, <code>canWrite()</code> : 메시지 컨버터가 해당 클래스, 미디어 타입을 지원하는지 체크</li>
<li><code>read()</code>, <code>write()</code> : 메시지 컨버터를 통해서 메시지를 읽고 쓰는 기능</li>
</ul>
<h4 id="스프링-부트-기본-메시지-컨버터">스프링 부트 기본 메시지 컨버터</h4>
<table>
<thead>
<tr>
<th>0</th>
<th>ByteArrayHttpMessageConverter</th>
</tr>
</thead>
<tbody><tr>
<td>1</td>
<td>StringHttpMessageConverter</td>
</tr>
<tr>
<td>2</td>
<td>MappingJackson2HttpMessageConverter</td>
</tr>
</tbody></table>
<ul>
<li>스프링 부트는 다양한 메시지 컨버터를 제공하는데, 대상 클래스 타입과 미디어 타입 둘을 체크해서 사용여부를 결정한다. 만약 만족하지 않으면 다음 메시지 컨버터로 우선순위가 넘어간다.</li>
</ul>
<blockquote>
<p>몇가지 주요한 메시지 컨버터를 알아보자.</p>
</blockquote>
<ul>
<li><code>ByteArrayHttpMessageConverter</code>: <code>byte[]</code> 데이터를 처리한다.<ul>
<li>클래스 타입 : <code>byte[]</code>, 미디어 타입: <code>*/*</code>,</li>
<li>요청 예) <code>@RequestBody byte[] data</code></li>
<li>응답 예) <code>@ResponseBody return byte[]</code> 쓰기 미디어타입 <code>application/octet-stream</code></li>
</ul>
</li>
<li><code>StringHttpMessageConverter</code>:<code>String</code>문자로 데이터를 처리한다.<ul>
<li>클래스 타입 : <code>String</code>, 미디어 타입: <code>*/*</code></li>
<li>요청 예) <code>@RequestBody String data</code></li>
<li>응답 예) <code>@ResponseBody return &quot;ok&quot;</code>쓰기 미디어타입 <code>text/plain</code></li>
</ul>
</li>
<li><code>MappingJackson2HttpMessageConverter</code>: application/json<ul>
<li>클래스 타입 : 객체 또는 <code>HashMap</code>, 미디어타입 <code>application/json</code> 관련</li>
<li>요청 예) <code>@RequestBody HelloData data</code></li>
<li>응답 예) <code>@ResponseBody return helloData</code> 쓰기 미디어타입 <code>application/json</code>관련</li>
</ul>
</li>
</ul>
<h4 id="stringhttpmessageconverter">StringHttpMessageConverter</h4>
<pre><code>content-type: application/json

@RequestMapping
void hello(@RequestBody String data) {}</code></pre><h4 id="mappingjackson2httpmessageconverter">MappingJackson2HttpMessageConverter</h4>
<pre><code>content-type: application/json

@RequestMapping
void hello(@RequestBody HelloData data) {}</code></pre><p>?</p>
<pre><code>content-type: text/html

@RequestMapping
void hello(@RequestBody HelloData data) {}</code></pre><h4 id="http-요청-데이터-읽기">HTTP 요청 데이터 읽기</h4>
<ul>
<li>HTTP 요청이 오고, 컨트롤러에서 <code>@RequestBody</code>, <code>HttpEntity</code> 파라미터를 사용한다.</li>
<li>메시지 컨버터가 메시지를 읽을 수 있는지 확인하기 위해 <code>canRead()</code>를 호출한다.<ul>
<li>대상 클래스 타입 지원하는가.<ul>
<li>예) <code>@RequestBody</code>의 대상 클래스 (<code>byte[]</code>, <code>String</code>, <code>HelloData</code>)</li>
</ul>
</li>
<li>HTTP 요청의 Content-Type 미디어 타입을 지원하는가.<ul>
<li>예) <code>text/plain</code>, <code>application/json</code>, <code>*/*</code></li>
</ul>
</li>
</ul>
</li>
<li><code>canRead()</code> 조건을 만족하면 <code>read()</code>를 호출해서 객체 생성하고, 반환한다.</li>
</ul>
<h4 id="http-응답-데이터-생성">HTTP 응답 데이터 생성</h4>
<ul>
<li>컨트롤러에서 <code>@ResponseBody</code>, <code>HttpEntity</code>로 값이 반환된다.</li>
<li>메시지 컨버터가 메시지를 쓸 수 있는지 확인하기 위해 <code>canWrite()</code>를 호출한다.<ul>
<li>대상 클래스 타입을 지원하는가.<ul>
<li>예) return의 대상 클래스 (<code>byte[]</code>, <code>String</code>, <code>HelloData</code>)</li>
</ul>
</li>
<li>HTTP 요청의 Accept 미디어 타입을 지원하는가. (더 정확히는 <code>@RequestMapping</code>의 <code>produces</code>)<ul>
<li>예) <code>text/plain</code>, <code>application/json</code>, <code>*/*</code></li>
</ul>
</li>
</ul>
</li>
<li><code>canWrite()</code> 조건을 만족하면 <code>write()</code>를 호출해서 HTTP 응답 메시지 바디에 데이터를 생성한다.</li>
</ul>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>