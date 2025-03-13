<h2 id="1-api-예외처리">1. api 예외처리</h2>
<ul>
<li>try-catch 문법<ul>
<li>에러가 일어나지 않았을때 실행될 코드</li>
<li>에러가 생겼을때 실행할 코드</li>
<li>개발할때 보통 사용</li>
<li>error코드를 기입해주면 에러원인을 쉽게 파악할 수 있다.<pre><code class="language-java">try{
//실행할 코드
}
catch{Exception e}{
//error가 생겼을 경우 실행할 코드
}</code></pre>
</li>
</ul>
</li>
</ul>
<h2 id="2-rest-api-예외처리">2. Rest api 예외처리</h2>
<ul>
<li>rest api는 html파일을 넘겨주지 않기 때문에 redirect와 같은 문법은 작동되지 않는다.</li>
<li><code>@Exception Handler 함수</code>를 생성할 경우 모든 에러를 캐치해준다.</li>
<li><code>@ControllerAdvice</code>는 예외 처리가 난 모든 api에서 이 파일을 실행한다.</li>
<li>특정 예외에서만 작동하는 코드를 생성할 수도 있다. (아래코드 참조)</li>
<li>하나의 예외 관련 파일을 만들어서 관리 (아래 코드 참조)<pre><code class="language-java">package com.baeksoo.shop;
</code></pre>
</li>
</ul>
<p>import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.method.annotation.MethodArgumentTypeMismatchException;</p>
<p>@ControllerAdvice
public class MyExceptionHandler {
    //특정 예외에서만 작동하는 코드를 만들고 싶을때
    @ExceptionHandler (MethodArgumentTypeMismatchException.class)
    public ResponseEntity handler01() {
        return ResponseEntity.status(400).body(&quot;에러발생&quot;);
    }</p>
<pre><code>// parameter에 Exception e가 들어가면 모든 에러에 대해서 이 코드가 실행된다.
@ExceptionHandler
public ResponseEntity&lt;String&gt; handler02(Exception e) {
    return ResponseEntity.status(400).body(&quot;에러발생&quot;);
}</code></pre><p>}</p>
<p>```</p>