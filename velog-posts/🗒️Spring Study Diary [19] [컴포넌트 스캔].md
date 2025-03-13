<h1 id="컴포넌트-스캔">컴포넌트 스캔</h1>
<hr />
<h2 id="컴포넌트-스캔과-의존관계-자동-주입">컴포넌트 스캔과 의존관계 자동 주입</h2>
<ul>
<li>스프링 빈을 등록할때 자바 코드의 @Bean 이나 XML의 <code>&lt;bean&gt;</code> 등을 통해서 설정 정보에 직접 등록할 스프링 빈을 나열함.</li>
<li>만약 이렇게 등록해야 할 스프링 빈이 수십, 수백, 수천 개가 되면 일일이 등록하기 힘들다.</li>
<li>그래서 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 컴포넌트 스캔이라는 기능을 제공</li>
<li>의존관계도 자동으로 주입하는 <code>@Autowired</code>라는 기능도 제공한다.</li>
</ul>
<blockquote>
<p>컴포넌트 스캔을 사용하려면 먼저 @ComponentScan을 설정 정보에 붙인다.
기존의 Config파일과 다르게 @Bean으로 등록한 클래스가 없다.</p>
</blockquote>
<blockquote>
<p><strong>참고</strong></p>
<ul>
<li>컴포넌트 스캔을 사용하면 @Configuration이 붙은 설정 정보도 자동으로 등록된다.</li>
<li>@Configuration 이 컴포넌트 스캔의 대상이 된 이유는 @Configuration 소스코드를 열어보면 @Component annotation이 붙어있기 때문이다.</li>
</ul>
</blockquote>
<ul>
<li><p>컴포넌트 스캔의 대상이 되도록 하려면 대상으로 하고싶은 클래스에 @Component annotation을 붙여주면 된다.</p>
</li>
<li><p><code>@Autowired</code> annotation은 의존관계를 자동으로 주입해준다</p>
<ul>
<li><code>@Autowired</code>를 사용하면 생성자에서 여러 의존관계도 한번에 주입받을 수 있다.<pre><code class="language-java">@Autowired
public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {}</code></pre>
</li>
</ul>
</li>
</ul>
<h3 id="그림으로-살펴보는-컴포넌트-스캔과-자동-의존-관계주입">그림으로 살펴보는 컴포넌트 스캔과 자동 의존 관계주입</h3>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/164a9e01-980e-45a4-8b45-889866a22779/image.png" /></p>
<ul>
<li><code>@ComponentScan</code>은 <code>@Component</code>가 붙은 모든 클래스를 스프링 빈으로 등록한다.</li>
<li>스프링 빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자를 사용한다.<ul>
<li><strong>빈 이름 기본 전략</strong> : 클래스가 MemberServiceImpl 일때 -&gt; 빈 이름은  memberServiceImpl 로 바뀐다.</li>
<li><strong>빈 이름 직접 지정</strong> : 스프링 빈의 이름을 직접 지정하고 싶다면? -&gt; <code>@Component(&quot;memberService2&quot;)</code> -&gt; 이런식으로 이름을 부여할 수 있다.</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/48f26ed4-dee4-4d39-8966-cbf90b5dcd6f/image.png" /></p>
<ul>
<li>생성자에 <code>@Autowired</code>를 지정하면, 스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아서 주입해준다.</li>
<li>기본 조회 전략은 타입이 같은 빈을 찾아서 주입한다.<ul>
<li>getBean(MemberRepository.class)와 동일하다고 생각 하면 됨</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/da164353-9828-46de-bf34-994a3b89ecf0/image.png" /></p>
<ul>
<li>생성자에 파라미터가 많아도 다 찾아서 자동으로 주입한다.</li>
</ul>
<hr />
<h2 id="탐색-위치와-기본-스캔-대상">탐색 위치와 기본 스캔 대상</h2>
<h3 id="탐색할-패키지의-시작-위치-지정">탐색할 패키지의 시작 위치 지정</h3>
<ul>
<li><p>모든 자바 클래스를 다 컴포넌트 스캔하면 시간이 오래걸린다.</p>
<ul>
<li>지금은 실습 프로젝트라 상관이 없지만 실제 실무에서 모든 자바 클래스를 컴포넌트 스캔한다고 가정하면 당연히 시간이 오래 걸릴수 밖에 없다.</li>
</ul>
</li>
</ul>
<pre><code class="language-java"> @ComponentScan(
        basePackages = &quot;시작 위치&quot;,
 }</code></pre>
<ul>
<li>basePackages : 탐색할 패키지의 시작 위치를 지정한다. 이 패키지를 포함하여 하위 패키지를 모두 탐색한다.<ul>
<li><code>basePackages = {&quot;hello.core&quot;, &quot;hello.service&quot;}</code> 이러헥 여러 시작 위치를 지정할 수도 있다.</li>
</ul>
</li>
<li>basePackageClasses : 지정한 클래스의 패키지를 탐색 시작 위치로 지정한다.</li>
<li>Default 값 : <code>@ComponentScan</code>이 붙은 설정 정보 클래스의 패키지가 시작 위치가 된다.</li>
</ul>
<p><strong>권장 방법</strong></p>
<ul>
<li>최근 스프링 부트도 기본으로 제공하는 <strong>설정 정보 클래스의 위치를 프로젝트 최상단에 두는 것이다.</strong></li>
</ul>
<blockquote>
<p>예시</p>
<ul>
<li><code>com.hello</code></li>
</ul>
</blockquote>
<ul>
<li><code>com.hello.service</code></li>
<li><code>com.hello.repository</code></li>
<li>이렇게 프로젝트 구조가 되어있으면 <code>com.hello</code> -&gt; 프로젝트 시작 루트, 여기에 Config 파일과 같은 메인 설정 정보를 두고, <code>@ComponentScan</code> annotation을 붙이고 <code>backPackages</code> 지정은 생략</li>
<li>이렇게 하면 com.hello 를 포함한 하위는 모두 자동으로 컴포넌트 스캔의 대상이 됨. </li>
<li>프로젝트 메인 설정 정보는 프로젝트를 대표하는 정보이기 때문에 프로젝트 시작 루트 위치에 두는 것이 좋다.
참고로 스프링 부트를 사용하면 스프링 부트의 대표 시작 정보인 <code>@SpringBootApllication</code>를 이 프로젝트 시작 루트 위치에 두는 것이 관례이다.</li>
</ul>
<hr />
<h2 id="컴포넌트-스캔-기본-대상">컴포넌트 스캔 기본 대상</h2>
<ul>
<li>컴포넌트 스캔은 <code>@Component</code>뿐만 아니라 아래의 내용도 추가로 대상에 포함된다.</li>
<li><code>@Component</code> : 컴포넌트 스캔에서 사용</li>
<li><code>@Controller</code> : 스프링 MVC 컨트롤러에서 사용</li>
<li><code>@Service</code> : 스피링 비즈니스 로직에서 사용</li>
<li><code>@Repository</code> : 스프링 데이터 접근 계층에서 사용</li>
<li><code>@configuration</code> : 스프링 설정 정보에서 사용</li>
</ul>
<pre><code class="language-java"> @Component
 public @interface Controller {
 }
 @Component
 public @interface Service {
 }
 @Component
 public @interface Configuration {
 }</code></pre>
<ul>
<li><p>해당 클래스의 소스코드를 확인해보면 <code>@Component</code>를 포함하고 있는 것을 확인할 수 있다.</p>
</li>
<li><p>사실 annotation에는 상속관계가 존재하지 않는다. 그렇기에 이렇게 annotation이 특정 annotation을 가지고있는 것을 인식하는 것은 자바언어의 기능이 아니고 스프링이 지원하는 기능이다.</p>
</li>
</ul>
<blockquote>
<p>컴포넌트 스캔의 용도 뿐만 아니라 다음 annotation이 있으면 스프링은 부가 기능을 수행한다.
 <code>@Controller</code> : 스프링 MVC 컨트롤러로 인식
 <code>@Repository</code> : 스프링 데이터 접근 계층으로 인식하고, 데이터 계층의 예외를 스프링 예외로 변환
 <code>@Configuration</code> : 앞서 보았듯이 스프링 설정 정보로 인식하고, 스프링 빈이 싱글톤을 유지하도록 추가 처리를 한다.
 <code>@Service</code> : 사실 Service annotation은 특별한 처리를 하지않는다. 대신 개발자들이 핵심 비즈니스 로직이 여기에 있겠구나 라고 비즈니스 계층을 인식하는데 도움이 된다.</p>
</blockquote>
<hr />
<h2 id="필터">필터</h2>
<ul>
<li><code>includeFilters</code> : 컴포넌트 스캔 대상을 추가로 지정한다.</li>
<li><code>excludeFilters</code> : 컴포넌트 스캔에서 제외할 대상을 지정한다.</li>
</ul>
<h3 id="filtertype-옵션">FilterType 옵션</h3>
<ul>
<li>ANNOTATION : 기본값, annotation을 인식해서 동작</li>
<li>ASSIGNABLE_TYPE: 지정한 타입과 자식 타입을 인식해서 동작</li>
<li>ASPECTJ: AspectJ 패턴 사용</li>
<li>REGEX: 정규 표현식</li>
<li>CUSTOM: TypeFilter라는 인터페이스를 구현해서 처리</li>
</ul>
<hr />
<h2 id="중복-등록과-충돌">중복 등록과 충돌</h2>
<ul>
<li>컴포넌트 스캔에서 같은 빈 이름을 등록한다면?</li>
<li>두가지 상황 발생<ul>
<li><ol>
<li>자동 빈 등록 vs 자동 빈 등록</li>
</ol>
</li>
<li><ol start="2">
<li>수동 빈 등록 vs 자동 빈 등록
### 자동 빈 등록 vs 자동 빈 등록</li>
</ol>
</li>
</ul>
</li>
<li>컴포넌트 스캔에 의해 자동으로 스프링 빈이 등록되는데, 그 이름이 같은 경우 스프링은 오류를 발생시킨다.<ul>
<li><code>ConflictingBeanDefinitionException</code> 예외 발생
### 수동 빈 등록 vs 자동 빈 등록</li>
</ul>
</li>
<li>이렇게 될 경우 수동 빈 등록이 우선권을 가지게 된다. (수동 빈이 자동 빈을 오버라이딩 해버린다.)</li>
</ul>
<hr />


<h3 id="📭-참고출처">📭 참고출처</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 핵심원리 - 기본편</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8</a></li>
</ul>