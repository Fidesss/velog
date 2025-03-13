<h3 id="커스터마이징">커스터마이징</h3>
<ul>
<li><p>보통 외부 라이브러리를 가져와서 사용하는데 있어서 수정의 작업을 거치지는 않는다. 왜냐하면 버전이 업그레이드되면 내가 수정했던 코드가 먹지 않는 경우가 생기기 때문이다. 이렇기에 우리는 java의 extends(상속)을 사용하여 우리가 원하는 기능을 만들게 된다</p>
</li>
<li><p>현재 접속한 <code>아이디</code>를 가지고있는 칼럼에 있는 <code>이름</code>을 가져    오고 싶어서 커스터마이징을 수행</p>
<pre><code class="language-java">@Getter
@Setter
class CustomUser extends User{
  private String displayName;
  public CustomUser(String username,
                    String password,
                    Collection&lt;? extends GrantedAuthority&gt; authorities
  ) {
      super(username, password, authorities);
  }
}</code></pre>
</li>
<li><p>User 클래스를 상속하여 CustomUser라는 클래스를 생성</p>
</li>
<li><p>displayName을 알고싶기에 private으로 생성</p>
<pre><code class="language-java">var a = new CustomUser(user.getUsername(), user.getPassword(), authorities);
      a.setDisplayName(user.getDisplayName());
      return a;</code></pre>
</li>
<li><p><code>displayName</code>을 현재 로그인한 칼럼의 <code>이름</code>을 가져와 저장</p>
</li>
</ul>
<pre><code class="language-java">@GetMapping(&quot;/my-page&quot;)
    public String myPage(Authentication auth) {
//        System.out.println(auth);                       // auth에는 많은 정보가 들어있다.
        CustomUser result = (CustomUser) auth.getPrincipal();
        System.out.println(auth.getName());             // 현재 로그인한 사람의 이름을 알 수 있다.
        System.out.println(auth.isAuthenticated());     // 현재 로그인 정보 True or false
        System.out.println(result.getDisplayName());
        return &quot;mypage.html&quot;;
    }</code></pre>
<ul>
<li>/my-page 요청을 받았을 때 로그인한 계정정보를 확인하기 위해 <code>auth</code> 를 사용</li>
<li>result 변수를 아까 만들었던 CustomUser 타입으로 캐스팅 하지않으면 displayName을 출력할 수 없음.</li>
<li>즉 displayName을 서버 console에 출력하기 위해서는 CustomUser 타입으로 타입 캐스팅을 진행해 주어야 한다.</li>
<li>Spring security에선 getPrincipal() 사용시 타입 캐스팅하는걸 권장함</li>
</ul>
<h3 id="세션-유지시간-설정-가능">세션 유지시간 설정 가능</h3>
<blockquote>
<ul>
<li>로그인 하면 누가 언제 로그인하였는지 저장해두는 세션 데이터가 하나 생성된다.</li>
<li>세션 데이터를 얼마 동안 유지해줄지도 서버입장에서 마음대로 설정이 가능</li>
<li>application.properties 파일에 접근</li>
</ul>
</blockquote>
<pre><code>server.servlet.session.timeout=20m
server.servlet.session.cookie.max-age=20m 
</code></pre><ul>
<li>이런식으로 사용이 가능하다.</li>
</ul>
<h3 id="db-persist">DB persist</h3>
<ul>
<li>지금까지는 사용자의 세션 데이터를 컴퓨터 메모리 아니면 파일하나 만들어서 임시로 저장되고 있을 것이다.</li>
<li>이러한 경우 불안정하기 때문에 DB에 세션 데이터를 저장해두는게 안정적이다.</li>
<li><code>spring-session-jdbc</code> 라이브러리 설치<ul>
<li><code>implementation 'org.springframework.session:spring-session-jdbc'</code> &gt;&gt; build.gradle에 추가</li>
<li><code>spring.session.store-type=jdbc</code>  &gt;&gt; application.properties에 추가</li>
</ul>
</li>
</ul>