<h2 id="객체지향-solid-개념-숙지">객체지향 SOLID 개념 숙지</h2>
<hr />
<h3 id="1-srp-단일-책임-원칙">1. SRP 단일 책임 원칙</h3>
<ul>
<li>하나의 클래스는 하나의 책임만 가져야 한다.</li>
<li>중요한 기준은 <code>변경</code>, 변경이 있을 때 파급 효과가 적으면 단일 책임 원칙을 잘 따른 것 이다.

</li>
</ul>
<h3 id="2-ocp-개방---폐쇄-원칙">2. OCP 개방 - 폐쇄 원칙</h3>
<ul>
<li><p>소프트웨어 요소는 확장에는 열려있으나 변경에는 닫혀있어야 한다.</p>
</li>
<li><p>소프트웨어가 기존 코드를 변경하지 않는 것</p>
</li>
<li><p>아래코드 예시</p>
<pre><code class="language-java">public class MemberServiceImpl implements MemberService {

  private final MemberRepository memberRepository = new MemoryMemberRepository();
}</code></pre>
</li>
<li><p>위와같은 코드에서 <code>memberRepository</code>라는 인터페이스와 <code>MemoryMemberRepository</code>라는 구현체 둘다에 의존하고 있는 상황이다.</p>
</li>
<li><p><code>MemoryMemberRepository</code>에서 <code>JDBCMemberRepository</code>로 바꾼다고 기획이 변경되었다고 가정하면  현재 구조에서는 아래코드로 변경될 수 밖에 없다.</p>

```java
public class MemberServiceImpl implements MemberService {

<p>  //private final MemberRepository memberRepository = new MemoryMemberRepository();
  private final MemberRepository memberRepository = new JDBCMemberRepository();
}
```</p>
</li>
<li><p>이러한 상황에서 소프트웨어가 기존 코드를 변경했기 때문에 OCP 원칙을 지키지 못했다고 할 수 있다.</p>


</li>
</ul>
<h3 id="3-lsp-리스코프-치환-원칙">3. LSP 리스코프 치환 원칙</h3>
<ul>
<li>프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.
### 4. ISP 인터페이스 분리 원칙</li>
<li>특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.<blockquote>
<p><strong>예시</strong></p>
<ul>
<li>자동차 인터페이스 -&gt; 운전 인터페이스, 정비 인터페이스</li>
<li>사용자 클라이언트 -&gt; 운전자 클라이언트, 정비사 클라이언트</li>
</ul>
</blockquote>
</li>
</ul>


<h3 id="5-dip-의존-관계-역전-원칙">5. DIP 의존 관계 역전 원칙</h3>
<ul>
<li><code>프로그래머는 추상화에 의존해야지, 구체화에 의존하면 안된다.</code></li>
<li>쉽게 이야기 하여 구현 클래스에 의존하지 말고 인터페이스에 의존하라.</li>
<li><code>의존 이란?</code> : 내가 어떤 구현체의 코드를 알고있다면? &gt; 의존하고 있다고 할수 있다.

</li>
</ul>
<h3 id="정리">정리</h3>
<blockquote>
<p>좋은 객체 지향 설계를 위한 5가지 원칙 <code>SOLID</code>에 대해서 공부하였다. 이러한 규칙을 지키면서 하는 것이 재사용성 측면에서 매우 좋다. 하지만 재사용가능성이 없는 상황에서는 이러한 원칙을 지키면서 했을 경우 개발자의 비용이 높아질 수 있으므로 사용할때 장점이 단점보다 클 때 사용해보도록 하자.</p>
</blockquote>


<h3 id="📭-참고출처">📭 참고출처</h3>
<ul>
<li>김영한 강사님의 <code>스프링 핵심원리 기본편</code></li>
<li><a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8</a></li>
</ul>