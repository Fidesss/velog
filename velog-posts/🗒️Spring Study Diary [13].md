<h3 id="csrf-공격">CSRF 공격</h3>
<ul>
<li>사용자가 웹사이트에 로그인한 상태에서 공격자가 의도한 요청을 사용자 모르게 실행하도록 유도하는 공격 기법</li>
<li>보통 사용자가 로그인한 상태에서 특정 사이트에 접근했을 때, 브라우저는 자동으로 쿠키를 포함하여 요청을 전송한다. 이를 악용하여 공격자는 피해자가 원치 않는 작업을 수행하도록 만드는 것</li>
</ul>
<h4 id="해결책">해결책</h4>
<ul>
<li>JWT를 사용하는 경우에 CSRF는 간단하게 예방이 가능</li>
<li>입장권을 쿠키에 넣어서 직접 보내는 것이 아니라 AJAX 요청시 headers 부가정보 기입란에 적어서 보내는 식으로 하면 쉽게 예방이 가능.</li>
</ul>
<h3 id="hashing">Hashing</h3>
<ul>
<li>해싱(Hashing)은 입력 데이터를 <strong>고정된 길이의 고유한 값(해시 값)</strong>으로 변환하는 과정<h4 id="특징">특징</h4>
</li>
<li>같은 문자를 hashing하면 항상 같은 결과가 나온다.(Salt방식 제외)</li>
<li>hashing한 결과값으로 원래 문자 추론이 불가능하다.</li>
</ul>
<h3 id="누가-만들어-놓은-클래스를-di-방식으로-사용하기">누가 만들어 놓은 클래스를 DI 방식으로 사용하기</h3>
<ul>
<li>CLASS에 <code>@Configuration</code> annotation 추가<pre><code class="language-java">@Bean
PasswordEncoder 함수() {
  return new BCryptPasswordEncoder();
}</code></pre>
</li>
<li>이러한 방식으로 사용하면 <code>함수</code>를 부를때 마다 그 자리에 BCryptPasswordEncoder를 생성한다.</li>
</ul>
<blockquote>
<p>쉽게 이야기하기
new BCryptPasswordEncoder를 하면 스프링이 가져가서 Bean으로 만들어준다.
그리고나서 이 함수를 가져다가 사용하면 DI방식으로 만들어준다.</p>
</blockquote>