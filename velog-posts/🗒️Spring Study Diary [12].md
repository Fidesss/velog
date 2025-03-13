<h2 id="ajax">Ajax</h2>
<ul>
<li><h4 id="ajaxasynchronous-javascript-and-xml는-웹-개발-기술의-조합으로-웹-페이지의-일부분을-동적으로-갱신할-수-있게-해주는-비동기-통신-방식"><code>AJAX(Asynchronous JavaScript and XML)</code>는 웹 개발 기술의 조합으로, 웹 페이지의 일부분을 동적으로 갱신할 수 있게 해주는 <code>비동기 통신 방식</code></h4>
</li>
<li>비동기 통신<ul>
<li>비동기 통신 : 전체 페이지를 새로 고치지 않고 서버로부터 데이터를 요청하고 받아올 수 있다.</li>
<li>부분 갱신 : 페이지의 특정 부분만 업데이트 할 수 있어 사용자 경험 향상<pre><code class="language-javascript">&lt;script&gt;
document.querySelector('.btn').addEventListener('click', function(){fetch('/test01',
    {method : 'POST',
    headers : { 'Content-Type': 'application/json' },
    body : JSON.stringify({title : 'kim', price : 1000}
    )})})
&lt;/script&gt;</code></pre>
</li>
</ul>
</li>
<li>class가 <code>btn</code> 인 버튼을 눌렀을때 function()이 실행되는 코드</li>
<li>body에 데이터를 삽입하여 서버에 보낼 수 있다.</li>
</ul>
<h2 id="query-string">query string</h2>
<ul>
<li>URL에 데이터를 같이 보내는 방식이다.</li>
<li>단순한 데이터를 보낼 때 사용한다.</li>
<li>한가지 데이터를 보낼때<pre><code class="language-javascript">&lt;script&gt;
fetch('/abc?데이터이름=데이터값') 
&lt;/script&gt;</code></pre>
</li>
<li>여러가지 데이터를 보낼때<pre><code class="language-javascript">&lt;script&gt;
fetch('/abc?데이터이름=데이터값&amp;데이터이름2=데이터값2') 
&lt;/script&gt;</code></pre>
</li>
</ul>
<h2 id="삭제기능">삭제기능</h2>
<h3 id="jpa를-이용한-삭제기능">Jpa를 이용한 삭제기능</h3>
<ul>
<li>@Controller<pre><code class="language-java">@PostMapping(&quot;/delete&quot;)
  String delete(@RequestParam Long id) {
      System.out.println(&quot;delete!!!!!!!!!!!function&quot;);
      System.out.println(id);
      itemService.deleteItem(id);
      return &quot;redirect:/list&quot;;
  }</code></pre>
</li>
<li>@Service<pre><code class="language-java">public void deleteItem(Long id) {
      Item item = itemRepository.findById(id)
              .orElseThrow(() -&gt; new IllegalArgumentException(&quot;해당 ID의 아이템이 없습니다: &quot; + id));
      itemRepository.delete(item);
  }</code></pre>
</li>
<li>list.html<pre><code class="language-html">&lt;form th:action=&quot;@{/delete}&quot; method=&quot;post&quot;&gt;
          &lt;input type=&quot;hidden&quot; name=&quot;id&quot; th:value=&quot;${i.id}&quot;&gt;
          &lt;button type=&quot;submit&quot; class=&quot;btn btn-primary&quot;&gt;삭제&lt;/button&gt;
      &lt;/form&gt;</code></pre>
</li>
</ul>
<h3 id="ajax를-이용한-삭제기능">ajax를 이용한 삭제기능</h3>
<pre><code class="language-html">&lt;button class=&quot;btn btn-primary&quot;  th:onclick=&quot;fetch('/delete?id=[[${i.id}]]', { method : 'DELETE' } )&quot;&gt;🗑️&lt;/button&gt;</code></pre>
<ul>
<li>button 태그를 이용하여 버튼을 눌렀을 때 삭제되게 구현<pre><code class="language-html">&lt;span th:onclick=&quot;fetch('/delete?id=[[${i.id}]]', { method : 'DELETE' } )&quot;&gt;🗑️&lt;/span&gt;</code></pre>
</li>
<li>button 태그가 아닌 html tag에서도 사용가능하다.</li>
<li>js에서 thymeleaf 문법을 사용하려면 이 환경에서는<code>th:onclick</code>를 이용하여 현재 id를 클릭했을때 서버에 넘겨주는 형식으로 구성</li>
</ul>
<pre><code class="language-html">&lt;button class=&quot;btn btn-primary&quot; th:onclick=&quot;fetch('/delete?id=[[${i.id}]]', { method : 'DELETE' } )
            .then(r =&gt; r.text())
            .then(() =&gt; {
                location.reload();
            })
&quot;&gt;🗑️
        &lt;/button&gt;</code></pre>
<ul>
<li><p>버튼을 누르면 <code>location.reload()</code>를 통해 서버에서 응답이 오면 현재 페이지를 reload한다. 이렇게 될 경우 성공 응답 뿐만 아니라 에러 응답이 와도 현재 페이지를 reload하기 때문에 사용할 때 주의해야 한다.</p>
</li>
<li><p>서버가 보낸 데이터를 출력해보고 싶을 때 <code>.then((a) =&gt; { console.log(a)})</code> -&gt; 웹브라우저의 개발자 도구에 들어가 console탭을 확인해보면 서버에서 보낸 데이터를 확인할 수 있다.</p>
</li>
</ul>
<h2 id="이번에-새로-배운-것">이번에 새로 배운 것</h2>
<ol>
<li>AJAX를 사용하면 새로고침 없이 요청을 날리고 데이터를 받아올 수 있다.</li>
<li>query string, url parameter(서버로 데이터 전송 가능한 방법)</li>
<li>Js안에 Thymeleaf 변수 넣는 것이 가능하다.</li>
</ol>