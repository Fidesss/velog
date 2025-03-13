<h2 id="문제점">문제점</h2>
<ul>
<li><h3 id="강의-실습-도중-문제-상황">강의 실습 도중 문제 상황</h3>
<ul>
<li>session을 db를 통해서 관리하기 위해 설정</li>
<li>설정 중 register 후 login을 할때 문제 발생<pre><code>org.springframework.jdbc.BadSqlGrammarException:PreparedStatementCallback; 
bad SQL grammar [INSERT INTO SPRING_SESSION (PRIMARY_ID, SESSION_ID, CREATION_TIME, LAST_ACCESS_TIME, MAX_INACTIVE_INTERVAL, EXPIRY_TIME, PRINCIPAL_NAME) VALUES (?, ?, ?, ?, ?, ?, ?) ]</code></pre></li>
</ul>
</li>
<li><p>조회했을때 session 정보를 담기위한 table도 생성되지 않았었다.</p>
</li>
<li><p>인터넷에 있는 해결방법들을 시도 중 table을 수동으로 생성하라는 해결책도 많았지만 어찌된 영문인지 모든 권한이 있는 계정에서 table을 생성할때 deny를 당하는 현상 발생</p>
<h2 id="해결방법">해결방법</h2>
</li>
<li><p>applicaton.properties 파일에 이와 같은 내용을 저장</p>
<pre><code>spring.session.store-type=jdbc 
spring.session.jdbc.initialize-schema= embedded # or always</code></pre></li>
<li><p>build.gradle 파일</p>
<pre><code>implementation 'org.springframework.session:spring-session-jdbc'</code></pre></li>
<li><p>중요한 것은 application.properties 가장 아래있는 줄에 schema를 <code>always</code>로 설정하면 그제서야 자동으로 table이 생기는 것을 확인할 수 있었다.</p>
</li>
<li><p><code>always</code>로 설정하면 조회할때마다 계속 생성한다는 정보를 접한 후 <code>embedded</code>로 변환하였다. &gt; 그대로 오류없이 실행되는 것을 확인할 수 있었다.</p>
</li>
</ul>