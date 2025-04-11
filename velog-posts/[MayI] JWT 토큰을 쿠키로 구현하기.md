<h2 id="로그인-api">로그인 api</h2>
<ul>
<li>프로젝트에서 내가 맡은 부분은 회원가입 및 로그인, 그리고 소셜로그인 까지 로그인에 대한 전반적인 부분을 맡게 되었다. 그러면 어떻게 구성했으며 구현했는지 살펴보도록 하자.<h3 id="로그인은-어떻게-구성하면-좋을까">로그인은 어떻게 구성하면 좋을까?</h3>
<blockquote>
<ul>
<li>로그인에 대한 많은 고민 끝에 jwt 토큰 방식으로 사용해보기로 하였다. </li>
</ul>
</blockquote>
</li>
<li>현재 인터넷에는 JWT토큰에 대해 구현한 많은 방식들이 존재하고 그 중에서 하나를 고르려고 하였지만 한번도 시도해보지 않은 방법, 공부해보지 못한 부분이라 첫 시작부터 어려움이 있었다. </li>
<li>다소 친숙한 쿠키방식을 사용하여 JWT Token 방식을 통한 로그인을 구현하자고 생각하였다.</li>
</ul>
<h3 id="로그인-api-flow">로그인 api flow</h3>
<ol>
<li>Post 요청이 오면 로그인 Rest api를 실행 </li>
<li>service 계층으로 이동하여 service 계층에서 비즈니스로직을 처리</li>
<li>service 계층에서 다시 controller 계층으로 이동</li>
<li>controller 계층에서 로그인에 성공할 경우 <code>return ApiResponse.onSuccess(&quot;로그인 완료되었습니다.&quot;);</code> 를 응답으로 보냄</li>
</ol>
<ul>
<li>간단하게 로그인 flow를 확인해 보았다. 이제 jwt 토큰을 만들고 쿠키에 넣는 flow를 알아보자.</li>
</ul>
<hr />
<h2 id="jwt">JWT?</h2>
<h3 id="jwt-토큰이란">JWT 토큰이란?</h3>
<ul>
<li><p>JWT(Json Web Token)는 웹 환경에서 사용자 인증 및 정보 전달에 자주 사용되는 토큰 기반 인증 방식이다. 토큰은 서버가 클라이언트에게 인증 정보를 안전하게 전달하거나 저장하기 위해 사용된다.</p>
</li>
<li><p>JWT는 세 부분으로 구성된 URL-safe 한 문자열이다. 이 문자열은 .으로 구분된 세 개의 파트로 이루어져 있다.</p>
</li>
<li><p><code>xxxxx.yyyyy.zzzzz</code></p>
</li>
</ul>
<h3 id="jwt-구성">JWT 구성</h3>
<ol>
<li><p>Header (헤더)</p>
<ul>
<li><p>어떤 알고리즘으로 서명했는지를 명시</p>
<pre><code class="language-json">
  {
        &quot;alg&quot;: &quot;HS256&quot;, (알고리즘 명시)
        &quot;typ&quot;: &quot;JWT&quot;
  }</code></pre>
</li>
</ul>
</li>
<li><p>Payload (페이로드)</p>
<ul>
<li><p>토큰에 담을 사용자 정보나 기타 데이터를 담는다. 이 내용은 인코딩만 되고 암호화 되지 않기 때문에 누구나 볼수있다는 특징이 있다.</p>
<pre><code class="language-json">  {
        &quot;sub&quot;: &quot;user123&quot;,
        &quot;role&quot;: &quot;USER&quot;,
       &quot;exp&quot;: 1712758290
  }
</code></pre>
</li>
</ul>
</li>
<li><p>Signature(서명)</p>
<ul>
<li>헤어와 페이로드를 조합한 후 비밀 키로 서명한 값, 이 부분을 통해 토큰의 위조 여부를 확인할 수 있다.<pre><code class="language-scss">  HMACSHA256(
        base64UrlEncode(header) + &quot;.&quot; + base64UrlEncode(payload),
        secret
  )</code></pre>
<h3 id="jwt-특징">JWT 특징</h3>
</li>
</ul>
</li>
</ol>
<ul>
<li>서버가 상태를 유지하지 않음 (Stateless): 클라이언트가 JWT만 들고 있으면 인증된 요청을 할 수 있다.</li>
<li>확장성이 좋다 : 마이크로서비스 구조에 적합하다.</li>
<li>빠름 : 매 요청마다 DB 조회 없이 인증이 가능하다. (서명으로 검증)</li>
</ul>
<h3 id="주의점">주의점</h3>
<ul>
<li>Payload는 암호화되지 않으므로 민감한 정보를 담아서는 안된다.</li>
<li>만료시간(exp)을 반드시 설정하여 보안 취약점을 줄여야 한다.</li>
<li>Refresh Token과 함께 써서 장기적인 세션 유지를 안전하게 해야 한다.</li>
</ul>
<hr />
<h2 id="jwt-토큰-검증">JWT 토큰 검증</h2>
<h3 id="jwt-accesstoken-만료시">JWT AccessToken 만료시</h3>
<blockquote>
<p>AccessToken만료시 쿠키에 남아있는 refreshToken을 먼저 검증하고 난 후 db에 저장되어있는 refreshToken과 맞는지 확인하여 db에 아직 만료되지 않은 유효한 refreshToken이 남아있다면 쿠키에 다시 AccessToken을 발급하고 프론트에 419에러를 보내 다시 방금 전 요청을 다시 불러오도록 하여 유연한 요청 및 응답처리를 구현</p>
</blockquote>
<h3 id="jwt-refreshtoken-만료시">JWT RefreshToken 만료시</h3>
<blockquote>
<p>RefreshToken이 만료되면 프론트에게 401에러를 보내 다시 로그인을 요청하도록 설계</p>
</blockquote>
<pre><code class="language-java">@Override
protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
                                    FilterChain chain
 ) throws ServletException, IOException {

     String accessToken = CookieUtil.getCookieValue(request, &quot;accessToken&quot;);

     // accessToken 검사
     if (accessToken != null &amp;&amp; jwtUtil.validateToken(accessToken)) {
         Authentication authentication = jwtUtil.getAuthentication(accessToken);
         SecurityContextHolder.getContext().setAuthentication(authentication);
         log.info(&quot;accessToken 인증 완료 유저 : {}&quot;, authentication.getPrincipal());
     } else {
         // RefreshToken 검사
         String refreshToken = CookieUtil.getCookieValue(request, &quot;refreshToken&quot;);

         if (refreshToken != null &amp;&amp; jwtUtil.validateToken(refreshToken)) {
             String userEmail = jwtUtil.getUserEmail(refreshToken);

             if (userRepository.findByUserEmail(userEmail).isPresent()) {
                 log.info(&quot;리프레쉬 토큰안의 정보를 통한 이메일이 존재한다&quot;);

                 Optional&lt;User&gt; userOpt = userRepository.findByUserEmail(userEmail);
                 User user = userOpt.get();

                 if (refreshToken.equals(user.getRefreshToken())) {

                     log.info(&quot;리프레쉬토큰이 있어서 accessToken을 새로 발급하여 쿠키에 저장&quot;);

                     UserDetails userDetails = myUserDetailsService.loadUserByUsername(userEmail);
                     Authentication authentication = new UsernamePasswordAuthenticationToken(
                             userDetails, &quot;&quot;, userDetails.getAuthorities()
                     );
                     String newAccessToken = jwtUtil.generateAccessToken(authentication);
                     sendTokenResponse(response, newAccessToken);
                     SecurityContextHolder.getContext().setAuthentication(authentication);
                     sendStatusResponse(response, ErrorStatus._REFRESHED_ACCESS_TOKEN);
                     return;
                 }
             }
         }

         sendStatusResponse(response, ErrorStatus._EXPIRED_REFRESH_TOKEN);
         return;
     }


     chain.doFilter(request, response);
 }</code></pre>
<hr />
<h2 id="jwt와-login-api의-결합">JWT와 Login Api의 결합</h2>
<blockquote>
<p>로그인 API에 JWT 인증방식을 어떻게 사용했는가를 작성해보려고한다.</p>
</blockquote>
<ul>
<li>먼저 JWT 토큰 방식을 구현한 후 로그인을 성공했을시에 쿠키에 accessToken과 refreshToken을 생성한다.</li>
<li>쿠키에 넣은 refreshToken을 db에도 저장한다.</li>
<li>accessToken을 필터를 이용하여 스프링 시큐리티에서 검증하여 로그인된 유저만 접근할수 있는 페이지에 접근할 때 사용자 정보를 가져오고 db를 거치지 않고 로그인을 시키는 방법을 사용한다.</li>
</ul>
<h3 id="로그인-서비스-로직">로그인 서비스 로직</h3>
<pre><code class="language-java">@Transactional
    public void commonLogin(UserDTO.LoginRequestDTO loginRequestDTO, HttpServletResponse response) {

        String userEmail = loginRequestDTO.getUserEmail();
        String userPassword = loginRequestDTO.getUserPassword();

        Optional&lt;User&gt; userOpt = Optional.ofNullable(userRepository.findByUserEmail(userEmail).orElseThrow(() -&gt; new UserHandler(ErrorStatus._NOT_EXIST_EMAIL)));

        User user = userOpt.orElseThrow(() -&gt; new UserHandler(ErrorStatus._NOT_EXIST_USER));

        if (!passwordEncoder.matches(userPassword, user.getUserPassword())) {
            log.error(&quot;비밀번호가 일치하지 않습니다.&quot;);
            throw new UserHandler(ErrorStatus._NOT_MATCH_PASSWORD);
        }

        UsernamePasswordAuthenticationToken authenticationToken =
                new UsernamePasswordAuthenticationToken(userEmail, userPassword);

        Authentication authentication = authenticationManagerBuilder
                .getObject().authenticate(authenticationToken);

        JwtTokenDTO jwtTokenDTO = JwtUtil.generateToken(authentication);

        //login, refreshToken save
        user.updateRefreshToken(jwtTokenDTO.getRefreshToken());
        userRepository.save(user);

        sendTokenResponse(response, jwtTokenDTO);
    }</code></pre>
<h2 id="spring-security와-연결">Spring security와 연결</h2>
<blockquote>
<p>스프링 시큐리티 설정 파일에서 <code>securityFilterChain</code> 메서드에서 권한을 설정할 수있다. 
즉 사용자의 권한이 없거나 내가 설정한 권한이 아니라면 이 페이지를 접속할 수 없게 되는 것이다.</p>
</blockquote>
<pre><code class="language-java">.authorizeHttpRequests(auth -&gt; auth
    .requestMatchers(&quot;/user/test&quot;).hasRole(&quot;USER&quot;) // &quot;USER&quot; 권한테스트
    .anyRequest().permitAll() // 인증 x
)</code></pre>
<ul>
<li>현재 로직은 배포하기전 개발용이기 때문에 테스트를 위해서 /user/test api에만 권한이 있어야 접속할 수 있도록 일시적으로 막아 놓은 것이다.</li>
</ul>
<h2 id="마지막으로">마지막으로</h2>
<blockquote>
<p>프로젝트에서 맡은 부분을 거의 다 끝내고 작성하는 글이라 정리가 잘 되어있지는 않다. 
다음에 혹은 포스트하고나서 나중에 수정할때 다시 정리를 해봐야할 것 같다.
jwt를 처음에 원리를 이해하는 것은 쉬웠지만 직접 구현하려니 내 생각 이상으로 시간이 많이 걸렸다.
기본원리, 기본 동작 원리를 완전히 이해하고 하는 것이 중요함을 깨달았지만
동시에 책에서 배운 원리, 지식 만이 아니라 코딩은 역시 실전에서 부딪혀봐야한다는 생각이 강하게 들었다.</p>
</blockquote>
<h3 id="📗reference">📗Reference</h3>
<ul>
<li><a href="https://brunch.co.kr/@jinyoungchoi95/1">https://brunch.co.kr/@jinyoungchoi95/1</a></li>
</ul>