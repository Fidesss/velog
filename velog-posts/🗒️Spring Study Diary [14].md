<h2 id="login-페이지-만들기">login 페이지 만들기</h2>
<ul>
<li><p>로그인 페이지를 만들때 <code>input</code>태그에 <code>name</code>속성을 DB에 있는 id, pw와 같게 만들어주어야 한다.</p>
</li>
<li><p>Spring Security에서는 form태그의 action이나 method방식, input태그의 name속성 등을 중요하게 생각한다. (잘 맞춰주자)</p>
</li>
<li><p>만약 form에서 로그인을 할 경우 (아래 코드)</p>
<pre><code class="language-java">//[SecurityConfig.java]
http.formLogin((formLogin) -&gt; formLogin.loginPage(&quot;/login&quot;)
.defaultSuccessUrl(&quot;/&quot;)
.failureUrl(&quot;/fail&quot;)
); </code></pre>
</li>
<li><p>실패시 현재 위의 코드는 /fail로 이동한다고 설정이 되어있는데 이걸 지우면 default경로로 <code>querystring</code> 처럼 <code>/login?error</code>로 이동한다.</p>
</li>
</ul>
<h3 id="thymeleaf타임리프-새로운-문법">Thymeleaf(타임리프) 새로운 문법</h3>
<ul>
<li>현재 url의 querystring에 있던 데이터를 가져다가 사용하는 방법<pre><code class="language-html">&lt;div th:if=&quot;${param.error}&quot;&gt;
  &lt;h4&gt;아이디나 비밀번호가 틀렸습니다. &lt;/h4&gt;
&lt;/div&gt;</code></pre>
</li>
<li>thymeleaf 기본 변수 중 param은 현재 url의 querystring에 있던 데이터를 가져다가 사용할 수 있다.</li>
<li>아이디나 비밀번호중 어떤 것이 틀렸는지는 보안상 좋지 않기 때문에 둘중에 하나가 틀렸을때 <code>아이디나 비밀번호가 틀렸습니다.</code>와 같이 알려주는 것이 좋다.</li>
</ul>
<h3 id="db에서-내가원하는-데이터-찾아오는-함수-생성">DB에서 내가원하는 데이터 찾아오는 함수 생성</h3>
<p>MemberRepository 인터페이스에서 <code>생성</code></p>
<ul>
<li>강의에서 배운 코드를 따라적어보면<pre><code class="language-java">Optional&lt;Member&gt; findByUsername(String username);</code></pre>
</li>
<li>여기서 By &gt; 1개만 , All &gt; 모든 곳에서 찾아라 이러한 이야기이다.</li>
</ul>
<h4 id="derived-query-methods">Derived query methods</h4>
<ul>
<li>and, or 조건주기</li>
<li>특정문자 포함되었는지 검색</li>
<li>특정 숫자 이상/이하인거 검색</li>
<li>정렬</li>
</ul>
<h4 id="필요한-것">필요한 것</h4>
<ul>
<li>@RequiredArgsConstructor</li>
</ul>
<h3 id="로그인-기능-2">로그인 기능 2</h3>
<ul>
<li>Optional 자료형은 if문과 함께 써야 안전하다.
그렇기에 if문에서 자료가 비어있다면 exception을 강제로 발생시키고
아니면 그냥 출력하는 그런 코드가 안전하다.</li>
</ul>
<pre><code class="language-java">if (result.isEmpty()) {
            throw new UsernameNotFoundException(&quot;say no id&quot;);
        }</code></pre>
<ul>
<li><p>웹에서 이사람이 일반유저인지 관리자 유저인지도 여기서 만든것으로 파악할 수 있다. (아래코드)</p>
<pre><code class="language-java">List&lt;GrantedAuthority&gt; 권한 = new ArrayList&lt;&gt;();
      권한.add(new SimpleGrantedAuthority(&quot;common user&quot;));</code></pre>
</li>
<li><p>thymeleaf 라이브러리를 사용하는 경우에 굳이 서버에서 데이터를 받아올 필요가 없다.</p>
</li>
<li><p>sec:authentication을 사용하면 현재 접속해있는 아이디의 정보를 알 수 있다.</p>
</li>
</ul>
<ol>
<li>```<div>```
<!-- 이 유저가 일반유저일 경우에만 보여준다. (div태그에 적혀있는 텍스트를?)--></li>
<li>```<div>```
 <!-- 로그인한 사람만 html을 보여주는 코드이다.--></li>
<li><code>isAnonymous()</code> &gt; True or False를 반환하면 보통 로그인한 사람과 로그인하지않은 사람을 구별해야할 상황에 사용한다.</li>
</ol>