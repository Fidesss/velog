<h2 id="프로토타입-스코프---싱글톤-빈과-함께-사용시-문제-해결-방법">프로토타입 스코프 - 싱글톤 빈과 함께 사용시 문제 해결 방법</h2>
<ul>
<li>싱글톤 빈과 프로토타입 빈을 함께 사용할 때, 어떻게 하면 사용할 때 마다 항상 새로운 프로토 타입 빈을 생성할 수 있을까?</li>
</ul>
<h3 id="스프링-컨테이너에-요청">스프링 컨테이너에 요청</h3>
<ul>
<li>가장 간단한 방법은 싱글톤 빈이 프로토타입을 사용할 때 마다 스프링 컨테이너에 새로 요청하는 것</li>
</ul>
<h4 id="핵심-코드">핵심 코드</h4>
<pre><code class="language-java">@Autowired
private ApplicationContext ac;
public int logic() {
     PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);
    prototypeBean.addCount();
     int count = prototypeBean.getCount();
     return count;
 }</code></pre>
<blockquote>
<ul>
<li><code>ac.getBean()</code>을 통해서 항상 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있다.</li>
</ul>
</blockquote>
<ul>
<li>의존관계를 외부에서 주입(DI) 받는게 아니라 이렇게 직접 필요한 의존관계를 찾는 것을 <code>Dependency Lookup</code>(DL) 의조노간계 조회(탐색) 이라한다.</li>
<li>하지만 이렇게 스프링의 애플리케이션 컨텍스트 전체를 주입받게 되면, 스프링 컨테이너에 종속적인 코드가 되고, 단위 테스트도 어려워진다.</li>
<li>지금 필요한 기능은 지정한 프로토타입 빈을 컨테이너에서 대신 찾아주는 DL 정도의 기능만 제공하는 무언가이다. 그렇기 때문에 이 방법은 좋은 방법이라고는 할 수 없을 것 같다.</li>
</ul>
<blockquote>
<blockquote>
<p><strong>스프링에는 이미 모든게 준비되어 있음.</strong></p>
</blockquote>
</blockquote>
<hr />
<h3 id="objectfactory-objectprovider">ObjectFactory, ObjectProvider</h3>
<ul>
<li>지정한 빈을 컨테이너에서 대신 찾아주는 DL 서비스를 제공하는 것이 바로 <code>ObjectProvider</code>이다. </li>
<li>tmi -&gt; 과거에는 <code>ObjectFactory</code>가 있었는데 여기에 편의 기능이 추가되어서 <code>ObjectProvider</code>가 만들어졌다.</li>
</ul>
<pre><code class="language-java">@Autowired
 private ObjectProvider&lt;PrototypeBean&gt; prototypeBeanProvider;
 public int logic() {
     PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
    prototypeBean.addCount();
     int count = prototypeBean.getCount();
     return count;
 }</code></pre>
<blockquote>
<ul>
<li>실행 -&gt; <code>prototypeBeanProvider.getObject()</code>을 통해서 항상 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있다.</li>
</ul>
</blockquote>
<ul>
<li><code>ObjectProvider</code>의 <code>getObject()</code>를 호출하면 내부에서는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환한다. (DL)</li>
<li>스프링이 제공하는 기능을 사용하지만, 기능이 단순하므로 단위테스트를 만들거나 mock 코드를 만들기는 훨씬 쉬워진다.</li>
<li><code>ObjectProvider</code>는 지금 딱 필요한 DL 정도의 기능만 제공</li>
</ul>
<h4 id="특징">특징</h4>
<ul>
<li><code>ObjectFactory</code> : 기능 단순, 별도의 라이브러리 필요 없음, 스프링에 의존</li>
<li><code>ObjectProvider: ObjectFactory</code> 상속, 옵션, 스트림 처리등 편의 기능이 많고, 별도의 라이브러리 필요 없음, 스프링에 의존</li>
</ul>
<hr />
<h3 id="jsr---330-provider">JSR - 330 Provider</h3>
<ul>
<li><code>javax.inject.Provider</code>라는 JSR-330 자바 표준 사용 방법</li>
<li>스프링 부트 3.0은 <code>jakarta.inject.Provider</code> 사용</li>
</ul>
<h4 id="이-방법은-라이브러리를-gradle에-추가해야-한다">이 방법은 라이브러리를 gradle에 추가해야 한다.</h4>
<pre><code class="language-java"> @Autowired
 private Provider&lt;PrototypeBean&gt; provider;

 public int logic() {
     PrototypeBean prototypeBean = provider.get();
    prototypeBean.addCount();
     int count = prototypeBean.getCount();
     return count;
 }</code></pre>
<blockquote>
<ul>
<li>실행해보면 <code>provider.get()</code>을 통해 항상 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있다.</li>
</ul>
</blockquote>
<ul>
<li><code>provider</code>의 <code>get()</code>을 호출하면 내부에서는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환한다. (DL)</li>
<li>자바 표준이고, 기능이 단순하므로 단위 테스트를 만들거나 mock 코드를 만들기는 훨씬 쉬워진다.</li>
<li><code>Provider</code>는 지금 딱 필요한 DL 정도의 기능만 제공한다.</li>
</ul>
<h4 id="정리">정리</h4>
<ul>
<li>그러면 프로토타입 빈을 언제 사용할까? 매번 사용할 때 마다 의존관계 주입이 완료된 새로운 객체가 필요하면 사
용하면 된다. 그런데 실무에서 웹 애플리케이션을 개발해보면, 싱글톤 빈으로 대부분의 문제를 해결할 수 있기 때
문에 프로토타입 빈을 직접적으로 사용하는 일은 매우 드물다고 한다.</li>
</ul>


<h3 id="📭-참고출처">📭 참고출처</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 핵심원리 - 기본편</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8</a></li>
</ul>