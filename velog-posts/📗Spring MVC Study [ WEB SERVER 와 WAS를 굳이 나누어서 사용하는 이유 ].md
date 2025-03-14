<h2 id="web-server와-was는-왜-굳이-나누어서-사용하는-걸까">Web Server와 WAS는 왜 굳이 나누어서 사용하는 걸까?</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/288a25d5-3b8f-4af2-a92a-d210f28590a1/image.png" /></p>
<h3 id="웹서버와-was를-나누는-궁극적인-이유">웹서버와 WAS를 나누는 궁극적인 이유</h3>
<ul>
<li><code>효율성</code> </li>
<li><code>보안</code></li>
<li><code>확장성</code></li>
</ul>
<p><strong>이 3가지를 극대화 하기 위해서이다.</strong></p>
<h3 id="1-성능-최적화">1. 성능 최적화</h3>
<blockquote>
<ul>
<li>웹 서버는 정적 콘텐츠를 빠르게 제공하는 데 특화되어있어, 클라이언트 요청 중 정적인 부분을 먼저 처리한다.</li>
</ul>
</blockquote>
<ul>
<li>WAS는 동적 콘텐츠 처리에 집중할 수 있어 불필요한 자원 낭비를 줄인다.</li>
</ul>
<h3 id="2-부하-분산">2. 부하 분산</h3>
<blockquote>
<ul>
<li>웹 서버는 여러 WAS 인스턴스 앞에서 트래픽을 분배하는 역할을 할 수 있다. (Reverse Proxy 기능)</li>
</ul>
</blockquote>
<ul>
<li>사용자가 많아질 때 WAS를 추가로 확장 (Scale-out)하더라도 웹 서버가 트래픽을 적절히 나눠줘 시스템 과부하를 방지한다.</li>
</ul>
<h3 id="3-보안-강화">3. 보안 강화</h3>
<blockquote>
<ul>
<li>웹 서버를 앞단에 두고 WAS를 뒤에 배치하면, 외부에서 WAS에 직접 접근하는 것을 차단할 수 있다.</li>
</ul>
</blockquote>
<ul>
<li>웹 서버에서 SSL 처리, 방화벽 설정, 요청 필터링 등을 통해 보안성을 톺일 수 있다.</li>
<li>WAS가 직접 노출되지 않으니 애플리케이션 로직이나 데이터베이스 연결 정보가 유출될 가능성이 줄어든다.</li>
</ul>
<h3 id="4-유연한-유지보수와-확장성">4. 유연한 유지보수와 확장성</h3>
<blockquote>
<ul>
<li>웹 서버와 WAS를 분리하면 각각 독립적으로 설정을 변경하거나 업그레이드 할 수 있다.</li>
</ul>
</blockquote>
<ul>
<li>예시<ul>
<li>정적 콘텐츠를 CDN(Contents Delivery Network)으로 옮기거나, WAS를 다른 언어/프레임워크로 교체할 때 유연성이 높아진다.</li>
</ul>
</li>
</ul>
<h3 id="5-역할-분담으로-안정성-확보">5. 역할 분담으로 안정성 확보</h3>
<blockquote>
<ul>
<li>WAS가 정적 콘텐츠까지 처리하면 자원이 분산되어 동적 요청 처리 속도가 느려질 수 있다.</li>
</ul>
</blockquote>
<ul>
<li>웹서버가 단순하고 가벼운 요청 말고, WAS가 복잡한 로직에 집중하도록 분리하면 전체 시스템의 안정성이 향상된다.</li>
</ul>
<p><strong>이러한 이유 때문에 WebServer와 WAS로 웹 시스템을 분리하여 사용한다.</strong></p>