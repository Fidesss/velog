<h2 id="쓰레드-thread">쓰레드 (Thread)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/a2da8c4e-be24-4f8f-84cb-634008d4c72f/image.png" /></p>
<ul>
<li><p>서블릿 객체를 호출하는 것이 Thread 이다.</p>
</li>
<li><p>애플리케이션 코드를 하나하나 순차적으로 실행하는 것은 <code>Thread</code> 이다.</p>
</li>
<li><p>자바 메인 메서드를 생각해보면 처음 실행할때 main이란 이름의 Thread가 실행하는 것을 확인할 수 있음.</p>
</li>
<li><p>Thread가 없다면 자바 애플리케이션 실행이 불가능하다.</p>
</li>
<li><p>Thread는 한번에 하나의 코드 라인만 수행</p>
</li>
<li><p>동시 처리가 필요하면 쓰레드를 추가로 생성하는 방법을 고려해볼 수 있음</p>
</li>
</ul>
<h2 id="요청마다-쓰레드-생성">요청마다 쓰레드 생성</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/d628a6fd-791f-4dd1-a81c-cf53935d0ec9/image.png" /></p>
<h3 id="장단점">장단점</h3>
<blockquote>
<p><strong>장점</strong></p>
</blockquote>
<ul>
<li>동시 요청을 처리할 수 있다.</li>
<li>리소스(CPU, 메모리)가 허용할 때 까지 처리가능</li>
<li>하나의 Thread가 지연되어도, 나머지 Thread는 정상 동작</li>
</ul>
<blockquote>
<p><strong>단점</strong></p>
</blockquote>
<ul>
<li>쓰레드 생성 비용은 머무 비싸다.<ul>
<li>생성할때 cpu도 사용함</li>
<li>Thread 생성하는데 시간이 걸리기 때문에 request에 대한 response의 응답 속도가 느리다.</li>
</ul>
</li>
<li>고객의 요청이 올 때마다 Thread를 생성하면 응답속도가 늦어진다.</li>
<li>쓰레드는 컨텍스트 스위칭 비용이 발생한다.<ul>
<li>만약 cpu 코어가 1개인 서비스에서 Thread 2개를 동시에 사용한다고 가정할때 사실은 동시에 수행하는 것이 아니고 Thread 1개의 수행이 끝나고 나머지 1개의 수행을 진행하는 것이다.</li>
<li>즉 컨텍스트 스위칭 비용은 Thread 2개를 스위칭 할 때 발생하는 비용이다.</li>
</ul>
</li>
<li>Thread 생성에 제한이 없다.</li>
<li>고객 요청이 너무 많이 오면, cpu 메모리 임계점을 넘어서 서버가 죽을 수도 있다.</li>
</ul>
<h2 id="thread-풀">Thread 풀</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/ae468e42-3000-4313-a876-0fd24a93628d/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/5d199e33-e113-42f4-9a90-bea8a96e4616/image.png" /></p>
<blockquote>
<p>Thread 풀 안에 미리 Thread를 생성해 놓고 요청이 오면 갖다 쓰는 방식, Thread를 만약 다 사용했다면 Thread를 종료시키는 것이 아니라 Thread풀에 다시 반납 하는 방식이다.</p>
</blockquote>
<hr />
<p>만약 Thread 풀에 Thread가 200개 존재하고, Thread 200개가 모두 사용중 이라면 새로운 요청에 대해서 Thread가 반납되기를 기다리는 대기, 거절 로직 구현 가능</p>
<h3 id="thread-풀-장점">Thread 풀 장점</h3>
<blockquote>
</blockquote>
<ul>
<li>Thread가 미리 생성되어 있으므로, Thread를 생성하고 종료하는 비용(cpu)가 절약되고, 응답 시간이 빠르다.</li>
<li>생성 가능한 쓰레드의 최대치가 있으므로 너무 많은 요청이 들어와도 기존 요청은 안전하게 처리할 수 있다.<ul>
<li>너무 많은 요청이 들어오면? -&gt; 서버 증설</li>
</ul>
</li>
</ul>
<h3 id="thread-풀-실무-팁">Thread 풀 (실무 팁)</h3>
<blockquote>
<ul>
<li>was의 주요 튜닝 포인트는 최대 Thread (max Thread)의 수</li>
</ul>
</blockquote>
<ul>
<li>이 값을 너무 낮게 설정하면?
<img alt="업로드중.." src="blob:https://velog.io/7d40b997-f70b-4983-9f72-192114b0b194" /><ul>
<li>동시 요청이 많으면, 서버 리소스는 여유롭지만, 클라이언트는 금방 응답이 지연된다. </li>
</ul>
</li>
<li>이 값을 너무 높게 설정하면?<ul>
<li>동시 요청이 많으면, cpu, 메모리 리소스 임계점 초과로 서버 다운   </li>
</ul>
</li>
<li>장애 발생 시<ul>
<li>클라우드일때 일단 서버부터 늘리고 후에 튜닝</li>
<li>클라우드가 아니라면 열심히 튜닝해야함.</li>
</ul>
</li>
</ul>
<h3 id="thread-풀의-적정-숫자">Thread 풀의 적정 숫자</h3>
<ul>
<li>애플리케이션 로직의 복잡도 (나의 서비스가 db를 많이 조회하는가 등등 ), cpu, 메모리, io 리소스 상황에 따라 모두 다르다.</li>
<li>성능 테스트<ul>
<li>최대한 실제 서비스와 유사하게 성능 테스트 시도</li>
<li>툴 : 아파치, ab, 제이미터 nGrinder (네이버 오픈소스) 등</li>
</ul>
</li>
</ul>
<h3 id="핵심">핵심</h3>
<blockquote>
<p>WAS의 멀티 쓰레드 지원</p>
</blockquote>
<ul>
<li>멀티 Thread에 대한 부분은 WAS가 처리</li>
<li>개발자가 멀티 Thread 관련 코드를 신경쓰지 않아도 됨</li>
<li>개발자는 마치 <code>싱글 Thread 프로그래밍을 하듯이 편리하게 소스 코드를 개발</code></li>
<li>멀티 Thread 환경이므로 싱글톤 객체 (Servlet, Spring Bean)는 주의해서 사용</li>
</ul>
<h3 id="📭-reference">📭 Reference</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 MVC</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1</a></li>
</ul>