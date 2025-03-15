<h2 id="httpservletrequest-역할">HttpServletRequest 역할</h2>
<ul>
<li>HTTP 요청 메시지를 개발자가 직접 파싱해서 사용해도 되지만, 매우 불편함.</li>
<li>서블릿은 개발자가 HTTP 요청 메시지를 편리하게 사용할 수 있도록 개발자 대신에 HTTP 요청 메시지를 파싱한다.</li>
<li>결과를 <code>HttpServletRequest</code> 객체에 담아서 제공한다.</li>
</ul>
<p>즉 . HttpServletRequest를 사용하면 HTTP 요청 메시지를 편리하게 조회할 수 있다.</p>
<h3 id="http-요청-메시지">HTTP 요청 메시지</h3>
<ul>
<li>START LINE<ul>
<li>HTTP 메서드</li>
<li>URL</li>
<li>쿼리 스트링</li>
<li>스키마, 프로토콜</li>
</ul>
</li>
<li>헤더<ul>
<li>헤더 조회</li>
</ul>
</li>
<li>바디<ul>
<li>form 파라미터 형식 조회</li>
<li>message body 데이터 직접 조회</li>
</ul>
</li>
</ul>
<p><strong>HttpServletRequest 객체는 추가로 여러가지 부가기능도 함께 제공한다.</strong></p>
<h3 id="임시-저장소-기능">임시 저장소 기능</h3>
<ul>
<li>해당 HTTP 요청이 시작부터 끝날 때 까지 유지되는 임시 저장소 기능<ul>
<li>저장 : <code>request.setAttribute(name, value)</code></li>
<li>조회 : <code>request.getAttribute(name)</code></li>
</ul>
</li>
<li>세션 관리 기능<ul>
<li><code>request.getSession(crate : true)</code></li>
</ul>
</li>
</ul>
<h2 id="📭-reference">📭 Reference</h2>
<p>인프런 김영한 강사님의 스프링 MVC
링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></p>