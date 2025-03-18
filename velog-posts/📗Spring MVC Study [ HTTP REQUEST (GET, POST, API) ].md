<h2 id="get---쿼리-파라미터">GET - 쿼리 파라미터</h2>
<ul>
<li><p>쿼리 파라미터는 URL에 아래와 같이 <code>?</code>를 시작으로 보낼 수 있다.</p>
</li>
<li><p>추가 파라미터는 <code>&amp;</code>으로 구분할 수 있다.</p>
<blockquote>
<p><code>http://localhost:8080/request-param?username=hello&amp;age=20</code></p>
</blockquote>
</li>
<li><p>서버에서는 <code>HttpServletRequest</code>가 제공하는 메서드를 통해 쿼리 파라미터를 편리하게 조회할 수 있다.</p>
<pre><code class="language-java">String username = request.getParameter(&quot;username&quot;); //단일 파라미터 조회
</code></pre>
</li>
</ul>
<p>Enumeration parameterNames = request.getParameterNames(); //파라미터 이름들 모두 조회</p>
<p>Map&lt;String, String[]&gt; parameterMap = request.getParameterMap(); //파라미터를 Map으로 조회</p>
<p>String[] usernames = request.getParameterValues(&quot;username&quot;); //복수 파라미터 조회</p>
<pre><code>
### 복수 파라미터에서 단일 파라미터 조회
&gt; - ``username=hello&amp;username=kim&amp;username=dong``일 경우 어떻게 조회를 해야할까?
- ``request.getParameter``는 하나의 파라미터 이름에 대해서 단 하나의 값만 있을 때 사용해야 한다.
- 중복일 경우 ``request.getParameter()``를 사용하면 ``request.getParameterValues()``의 첫 번째 값을 반환한다.

***
## POST HTML FORM
- 주로 회원 가입이나, 상품 주문 등에서 사용하는 방식이다.
```html
 &lt;!DOCTYPE html&gt;
 &lt;html&gt;
 &lt;head&gt;
    &lt;meta charset=&quot;UTF-8&quot;&gt;
    &lt;title&gt;Title&lt;/title&gt;
 &lt;/head&gt;
 &lt;body&gt;
 &lt;form action=&quot;/request-param&quot; method=&quot;post&quot;&gt;
    username: &lt;input type=&quot;text&quot; name=&quot;username&quot; /&gt;
    age:      &lt;input type=&quot;text&quot; name=&quot;age&quot; /&gt;
    &lt;button type=&quot;submit&quot;&gt;전송&lt;/button&gt;
 &lt;/form&gt;
 &lt;/body&gt;
 &lt;/html&gt;</code></pre><p>특징</p>
<ul>
<li>content-type : <code>application/x-www.form-urlencoded</code></li>
<li>메시지 바디에 쿼리 파라미터 형식으로 데이터를 전달한다. <code>username=hello&amp;age=20</code></li>
</ul>
<p>정리</p>
<ul>
<li><code>request.getParameter()</code>는 GET URL 쿼리 파라미터 형식도 지원, POST HTML Form 형식도 지원한다.</li>
</ul>
<p><strong>참고</strong></p>
<blockquote>
<ul>
<li>content-type은 HTTP 메시지 바디의 데이터 형식을 지정</li>
</ul>
</blockquote>
<ul>
<li>GET URL 쿼리 파라미터 형식으로 클라이언트에서 서버로 데이터를 전달할 때는 HTTP 메시지 바디를 사용하지 않기 때문에 content-type이 없다.</li>
<li>POST HTML Form 형식으로 데이터를 전달하면 HTTP 메시지 바디에 포함된 데이터가 어떤 형식인지 content-type을 꼭 지정해야 한다.</li>
<li>이렇게 폼으로 데이터를 전송하는 형식을 <code>application/x-www-form-urlencoded</code>라고 한다.</li>
</ul>
<hr />
<h2 id="api-메시지-바디">API 메시지 바디</h2>
<h3 id="단순-텍스트를-json-형태로-request-요청">단순 텍스트를 JSON 형태로 request 요청</h3>
<ul>
<li>HTTP message body에 데이터를 직접 담아서 요청<ul>
<li>HTTP API에서 주로 사용, JSON, XML, TEXT</li>
<li>데이터 형식은 주로 JSON사용</li>
<li>POST, PUT, PATCH 형태로 요청</li>
</ul>
</li>
<li>HTTP 메시지 바디의 데이터를 InputStream을 사용해서 직접 읽을 수 있다.<blockquote>
<p><strong>참고</strong></p>
<ul>
<li>inputStream은 byte 코드를 반환한다. byte 코드를 우리가 읽을 수 있는 문자(String)로 보려면 문자표 (Charset)를 지정해주어야 한다. </li>
<li>요즘은 <code>euc-kr</code> 보다 <code>utf-8</code> 형태로 인코딩 하는 경우가 많아서 <code>utf-8</code>을 사용하자.</li>
</ul>
</blockquote>
</li>
</ul>
<h3 id="json-형태로-요청">JSON 형태로 요청</h3>
<blockquote>
<p><strong>JSON 형식 전송</strong></p>
</blockquote>
<ul>
<li>POST <code>http://localhost:8080/request-body-json</code></li>
<li>content-type : application/json</li>
<li>message body : <code>{&quot;username&quot; : &quot;hello&quot;, &quot;age&quot; : 20}</code></li>
<li>결과 : <code>messageBody = {&quot;username&quot; : &quot;hello&quot;, &quot;age&quot; : 20}</code></li>
</ul>
<p><strong>Json 형식 파싱 추가</strong></p>
<ul>
<li>Json 형식으로 파싱할 수 있게 객체를 하나 생성<pre><code class="language-java">import lombok.Getter;
import lombok.Setter;
@Getter @Setter
public class HelloData {
  private String username;
  private int age;
}</code></pre>
</li>
</ul>
<p><strong>Json 형태로 요청이 온것을 확인하려면 inputStream을 사용하면된다.</strong></p>
<pre><code>request를 inputStream으로 받으면 이런식으로 오게된다.
{messageBody = {&quot;username&quot; : &quot;hello&quot;, &quot;age&quot; : 20}}</code></pre><p><strong>Json형식의 요청을 객체의 형태로 받으려면?</strong></p>
<blockquote>
<p><strong>참고</strong></p>
<ul>
<li>JSON 결과를 파싱해서 사용할 수 있는 자바 객체로 변환하려면 Jaskson, Gson같은 JSON 변환 라이브러리를 추가해서 사용해야 한다.</li>
</ul>
</blockquote>
<ul>
<li>스프링 부트로 Spring MVC를 선택하면 기본으로 Jackson 라이브러리(ObjectMapper)를 함께 제공한다. -&gt; 이거 사용하면 자바 객체로 쉽게 변환할 수 있다.</li>
</ul>
<pre><code class="language-java">// Jackson 라이브러리 사용
private ObjectMapper objectMapper = new ObjectMapper();

HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);

System.out.println(&quot;helloData.username: &quot; + helloData.getUsername());
System.out.println(&quot;helloData.age: &quot; + helloData.getAge());

//객체로 변환후 찍어본 결과
helloData.username: hello
helloData.age: 20</code></pre>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>