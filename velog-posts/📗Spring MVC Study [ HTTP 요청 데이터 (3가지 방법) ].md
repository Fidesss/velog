<h2 id="http-요청-데이터--3가지-방법-">HTTP 요청 데이터 [ 3가지 방법 ]</h2>
<h3 id="1-get---쿼리-파라미터">1. GET - 쿼리 파라미터</h3>
<ul>
<li><code>/url?username=hello&amp;age=20</code></li>
<li>메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달</li>
<li>EX) 검색, 필터, 페이징등에서 많이 사용하는 방식</li>
</ul>
<h3 id="2-post---html-form">2. POST - HTML Form</h3>
<ul>
<li><code>content-type : application/x-www-form-urlencoded</code></li>
<li>메시지 바디에 쿼리 파라미터 형식으로 전달 username-hello&amp;age=20</li>
<li>EX) 회원 가입, 상품 주문, HTML Form 사용</li>
</ul>
<h3 id="3-http-message-body에-데이터를-직접-담아서-요청">3. HTTP message body에 데이터를 직접 담아서 요청</h3>
<ul>
<li>HTTP API에서 주로 사용, JSON, XML, TEXT</li>
<li>데이터 형식은 주로 JSON 사용<ul>
<li>POST, PUT, PATCH</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/2e0e3c01-5a87-454b-ba8d-11ef513501c6/image.png" /></p>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>