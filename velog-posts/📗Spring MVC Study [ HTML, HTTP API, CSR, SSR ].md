<h2 id="정적-리소스">정적 리소스</h2>
<blockquote>
<ul>
<li>고정된 HTML 파일, CSS, JS, 이미지, 영상 등을 제공</li>
</ul>
</blockquote>
<ul>
<li>주로 웹 브라우저
<img alt="" src="https://velog.velcdn.com/images/cara1326/post/1fa2dc4d-3aa9-499d-b6d7-cd6554a20f2e/image.png" /></li>
</ul>
<h2 id="html-페이지">HTML 페이지</h2>
<blockquote>
<ul>
<li>동적으로 필요한 HTML 파일을 생성해서 전달</li>
</ul>
</blockquote>
<ul>
<li>웹 브라우저 : HTML 해석
<img alt="" src="https://velog.velcdn.com/images/cara1326/post/248ad79f-aba9-4aef-b055-711e6bf8e206/image.png" /></li>
</ul>
<h2 id="http-api">HTTP API</h2>
<blockquote>
<ul>
<li>HTML이 아니라 데이터를 전달</li>
</ul>
</blockquote>
<ul>
<li>주로 JSON 형식 사용</li>
<li>다양한 시스템에서 호출</li>
<li>데이터만 주고 받음, UI 화면이 필요하면, 클라이언트가 별도 처리</li>
<li>앱, 웹 클라이언트, 서버 to 서버
<img alt="" src="https://velog.velcdn.com/images/cara1326/post/562c0ac9-4010-438e-9dc2-c2e8bec72c4f/image.png" /></li>
<li>UI 클라이언트 접점<ul>
<li>앱 클라이언트 (아이폰, 안드로이드, PC 앱)</li>
<li>웹 브라우저에서 자바 스크립트를 통한 HTTP API 호출</li>
<li>React, Vue.js 같은 웹 클라이언트</li>
</ul>
</li>
<li>서버 to 서버<ul>
<li>주문 서버 -&gt; 결제 서버</li>
<li>기업간 데이터 통신</li>
</ul>
</li>
</ul>
<h2 id="ssr---서버-사이드-렌더링">SSR - 서버 사이드 렌더링</h2>
<h3 id="서버에서-최종-html을-생성해서-클라이언트에-전달">서버에서 최종 HTML을 생성해서 클라이언트에 전달</h3>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/45c6aff5-737e-4c5b-9781-5c73020be72e/image.png" /></p>
<blockquote>
<ul>
<li>HTML 최종 결과를 서버에서 만들어서 웹 브라우저에 전달</li>
</ul>
</blockquote>
<ul>
<li>주로 정적인 화면에 사용</li>
<li>관련 기술 : JSP, Thymeleaf -&gt; 백엔드 개발자</li>
</ul>
<h2 id="csr---클라이언트-사이드-렌더링">CSR - 클라이언트 사이드 렌더링</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/0f36f44b-34cf-4b08-9a34-7ac5340db69e/image.png" /></p>
<blockquote>
<ul>
<li>HTML 결과를 자바스크립트를 사용해 웹 브라우저에서 동적으로 생성해서 적용</li>
</ul>
</blockquote>
<ul>
<li>주로 동적인 화면에 사용, 웹 환경을 마치 앱 처럼 필요한 부분부분 변경할 수 있음.</li>
<li>EX) 구글 지도, Gmail, 구글 캘린더</li>
<li>관련 기술 : React, Vue.js -&gt; 웹 프론트앤드 개발자</li>
</ul>
<h3 id="참고">참고</h3>
<ul>
<li>SSR을 사용하더라도, 자바스크립트를 사용해서 화면 일부를 동적으로 변경 가능하다.</li>
</ul>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>