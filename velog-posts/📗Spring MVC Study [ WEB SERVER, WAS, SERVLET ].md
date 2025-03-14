<h2 id="웹-서버-web-server">웹 서버 (Web Server)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/0c483e51-8f77-4495-ba31-01c85898229f/image.png" /></p>
<blockquote>
<ul>
<li>Http 기반으로 동작</li>
</ul>
</blockquote>
<ul>
<li>정적 리소스 제공, 기타 부가기능</li>
<li>정적 리소스란?<ul>
<li>서버에서 사용자가 요청할 때 마다 실시간으로 생성되거나 변경되지 않는, 미리 준비된 파일이나 데이터를 의미한다.)</li>
<li>즉 내용이 고정되어 있어 사용자마다 동일한 형태로 제공되는 자원을 말한다.</li>
<li>웹 개발에서의 예시<ul>
<li>HTML파일, CSS파일, JS파일, Image파일, PDF 파일 등이 있다.</li>
</ul>
</li>
</ul>
</li>
<li>웹 서버에는 nginx, apache 등이 있음.</li>
</ul>
<h2 id="웹-애플리케이션-서버-web-application-server-was">웹 애플리케이션 서버 (Web Application Server, WAS)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/8f1d8509-d294-482f-a48b-acf856ec912e/image.png" /></p>
<blockquote>
<ul>
<li>Http 기반으로 동작</li>
</ul>
</blockquote>
<ul>
<li>웹 서버의 기능을 포함 + 정적 리소스 제공 기능</li>
<li>프로그램 코드를 실행해서 애플리케이션 로직 수행<ul>
<li>동적 HTML, HTTP API (JSON)</li>
<li>서블릿, JSP, 스프링 MVC</li>
</ul>
</li>
<li>WAS에는 Tomcat, Jetty, Undertow 등이 있다.</li>
</ul>
<h2 id="web-server-was-차이점">Web Server, WAS 차이점</h2>
<blockquote>
<ul>
<li>웹 서버는 정적 리소스(파일), WAS는 애플리케이션 로직</li>
</ul>
</blockquote>
<ul>
<li>사실 둘의 용어도 경계도 모호하다.<ul>
<li>웹 서버도 프로그램을 실행하는 기능을 포함하기도 함</li>
<li>웹 애플리케이션 서버도 웹 서버의 기능을 제공함</li>
</ul>
</li>
<li>자바는 서블릿 컨테이너 기능을 제공하면 WAS<ul>
<li>서블릿 없이 자바 코드를 실행하는 서버 프레임워크도 있다.</li>
</ul>
</li>
<li>WAS는 애플리케이션 코드를 실행하는데 더 특화 되어있다.</li>
</ul>
<table>
<thead>
<tr>
<th>구분</th>
<th>Webserver</th>
<th>WAS</th>
</tr>
</thead>
<tbody><tr>
<td>처리 대상</td>
<td>정적 콘텐츠 (HTML, 이미지 등)</td>
<td>동적 콘텐츠 + 비즈니스 로직</td>
</tr>
<tr>
<td>기능</td>
<td>단순 요청/응답 처리</td>
<td>애플리케이션 실행 및 관리</td>
</tr>
<tr>
<td>복잡성</td>
<td>상대적으로 단순</td>
<td>복잡한 로직 처리 가능</td>
</tr>
<tr>
<td>예시 소프트웨어</td>
<td>Nginx, Apache HTTP Server</td>
<td>Tomcat, WebSphere</td>
</tr>
<tr>
<td>사용 목적</td>
<td>빠른 정적 파일 제공</td>
<td>웹 애플리케이션 운영</td>
</tr>
</tbody></table>
<h2 id="웹-시스템-구성---was-db">웹 시스템 구성 - WAS, DB</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/0a9e9820-6655-4fce-9294-c0d65b595e91/image.png" /></p>
<ul>
<li>WAS, DB 만으로 시스템 구성이 가능하다.</li>
<li>WAS는 정적 리소스, 애플리케이션 로직 모두 제공 가능</li>
<li>WAS가 너무 많은 역할을 담당, 서버 과부하 우려</li>
<li>가장 비싼 애플리케이션 로직이 정적 리소스 때문에 수행이 어려울 수 있다.</li>
<li>WAS 장애시 오류 화면도 노출 불가능</li>
</ul>
<h2 id="웹-시스템-구성---web-was-db">웹 시스템 구성 - WEB, WAS, DB</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/7825d49b-77e0-4dd2-bb58-c17d23fddb52/image.png" /></p>
<ul>
<li>효율적인 리소스 관리<ul>
<li>정적 리소스가 많이 사용되면 Web 서버 증설</li>
<li>애플리케이션 리소스가 많이 사용되면 WAS 증설</li>
</ul>
</li>
<li>정적 리소스만 제공하는 웹 서버는 잘 죽지 않는다.</li>
<li>애플리케이션 로직이 동작하는 WAS 서버는 잘 죽음</li>
<li>WAS, DB 장애시 WEB 서버가 오류 화면 제공 가능<ul>
<li>WAS + DB 로만 구성되어있는 웹 시스템은 WAS 서버가 죽을 경우 오류 페이지도 보여주지 못한다.   </li>
</ul>
</li>
</ul>
<hr />
<h3 id="일반적으로-사용하는-방식">일반적으로 사용하는 방식</h3>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/81a87fa4-00c8-4335-8c48-7db0fcd6bace/image.png" /></p>
<blockquote>
<ul>
<li>정적 리소스를 앞에 두고, 동적 리소스가 필요하면 WAS로 넘기고 DB에 접근후 다시 역순으로 동작</li>
</ul>
</blockquote>
<ul>
<li>웹 서버는 애플리케이션 로직 같은 동적인 처리가 필요하면 WAS에 요청을 위임</li>
<li>WAS는 중요한 애플리케이션 로직 처리 전담</li>
</ul>
<hr />
<ul>
<li>REST API와같이 회사 간의 서버에서 데이터만 주고 받는 경우에는 WAS + DB 방식으로 동작하는 것이 더 유리하다.</li>
</ul>
<hr />
<h2 id="서블릿-이전">서블릿 이전</h2>
<ul>
<li>웹 애플리케이션을 직접 구현할 경우 아래의 그림과 같은 내용을 모두 직접 구현해야 한다.</li>
<li>하지만 서블릿이 도입되고 난 후 <code>비즈니스 로직 실행</code> 부분을 제외한 모든 작업을 서블릿이 대신해주게 된다.</li>
</ul>
<h3 id="서버에서-처리해야-하는-업무">서버에서 처리해야 하는 업무</h3>
<ul>
<li>웹 애플리케이션 서버 직접 구현</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/00e322a3-73bd-4cb3-97bf-b7cdf3aa49f2/image.png" /></p>
<h2 id="서블릿-servlet">서블릿 (Servlet)</h2>
<blockquote>
<p><strong>서블릿 이란?</strong></p>
</blockquote>
<ul>
<li>서블릿은 <code>JAVA</code>를 기반으로 한 서버 측 프로그램으로, 주로 웹 애플리케이션에서 동적인 콘텐츠를 생성하거나 클라이언트 (EX:웹 브라우저)의 요청을 처리하는 데 사용됩니다. 서블릿은 Java EE(Java Enterprise Edition)의 일부로, HTTP 프로토콜을 통해 요청과 응답을 처리하는데 특화되어 있다.</li>
</ul>
<h3 id="서블릿-동작-방식">서블릿 동작 방식</h3>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/4122ded7-9c9b-4e86-849f-8c91cd9fbb93/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/fefc329c-1ff6-4809-86b6-b9b9d585582c/image.png" /></p>
<blockquote>
<p><code>Http 요청, 응답 흐름</code></p>
</blockquote>
<ul>
<li>Http 요청, 응답 흐름</li>
<li>Http 요청 시<ul>
<li>was는 request, response 객체를 새로 만들어서 서블릿 객체 호출</li>
<li>개발자는 request 객체에서 http 요청 정보를 편리하게 꺼내서 사용</li>
<li>개발자는 response 객체에 http 응답 정보를 편리하게 입력</li>
<li>was는 response 객체에 담겨있는 내용으로 http 응답 정보를 생성</li>
</ul>
</li>
</ul>
<hr />
<p><code>서블릿 컨테이너 Servlet Container</code></p>
<ul>
<li><p>톰캣 처럼 서블릿을 지원하는 was를 서블릿 컨테이너라고 한다.</p>
</li>
<li><p>서블릿 컨테이너는 서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기를 관리한다.</p>
</li>
<li><p>서블릿 객체는 <code>싱글톤</code>으로 관리한다.</p>
<ul>
<li><p>고객의 요청이 올 때 마다 계속 객체를 생성하는 것은 비효율</p>
</li>
<li><p>최초 로딩 시점에 서블릿 객체를 미리 만들어두고 재활용</p>
</li>
<li><p>모든 고객 요청은 동일한 서블릿 객체 인스턴스에 접근</p>
</li>
<li><p><code>공유 변수 사용 주의</code> -&gt; 공유변수를 잘못 사용할 경우 로그인을 했는데 다른 사용자의 이름이 보이는 등의 오류가 발생할 수 있음. &gt; 그러니 주의하여 사용하자</p>
</li>
<li><p>서블릿 컨테이너 종료시 함께 종료 </p>


</li>
</ul>
</li>
</ul>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>