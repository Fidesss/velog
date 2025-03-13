<h2 id="롬복과-최신-트렌드">롬복과 최신 트렌드</h2>
<ul>
<li>막상 대부분이 모두 불변이고 그래서 필드에 final 키워드를 사용하게 된다. 그런데 생성자도 생성해야하고 주입 받은 값을 대입하는 코드도 만들어야하고 반복 작업에 긴 코드를 써야하기 때문에 필드 주입처럼 편리하게 사용하는 방법은 없을까? -&gt; 해결법은 롬복이다.</li>
</ul>
<pre><code class="language-java">public class OrderServiceImpl implements OrderService {
     private final MemberRepository memberRepository;
     private final DiscountPolicy discountPolicy;

 @Autowired
 public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
     this.memberRepository = memberRepository;
     this.discountPolicy = discountPolicy;
    }
 }</code></pre>
<h3 id="생성자가-딱-1개만-있을-경우-autowired를-생략할-수-있다">생성자가 딱 1개만 있을 경우 @Autowired를 생략할 수 있다.</h3>
<pre><code class="language-java"> @Component
 public class OrderServiceImpl implements OrderService {
     private final MemberRepository memberRepository;
     private final DiscountPolicy discountPolicy;

 public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
     this.discountPolicy = discountPolicy;
    }
 }</code></pre>
<ul>
<li>lombok library가 제공하는 <code>@RequiredArgsConstructor</code> 기능을 사용하면 final이 붙은 필드를 모아서 생성자를 자동으로 만들어준다. (코드에는 보이지 않지만 실제 호출이 가능하다.)</li>
</ul>
<p><strong>최종 결과 코드</strong></p>
<pre><code class="language-java"> @Component
 @RequiredArgsConstructor
 public class OrderServiceImpl implements OrderService {
     private final MemberRepository memberRepository;
     private final DiscountPolicy discountPolicy;
}</code></pre>
<ul>
<li><p>최종 결과 코드는 이전의 코드와 완전히 동일하다. 롬복이 자바의 annotation processor라는 기능을 이용하여 컴파일 시점에 생성자 코드를 자동으로 생성해준다. 실제로 class를 열어보게 될 경우 아래 코드가 추가되어 있는 것을 확인할 수 있다.</p>
<pre><code class="language-java">public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
   this.memberRepository = memberRepository;
   this.discountPolicy = discountPolicy;
}</code></pre>
<h3 id="롬복-라이브러리-적용-방법">롬복 라이브러리 적용 방법</h3>
</li>
<li><p>build.gradle에 라이브러리 및 환경 추가</p>
<pre><code>configurations {
  compileOnly {
      extendsFrom annotationProcessor
  }
}

dependencies{
    compileOnly 'org.projectlombok:lombok'
  annotationProcessor 'org.projectlombok:lombok'
  testCompileOnly 'org.projectlombok:lombok'
  testAnnotationProcessor 'org.projectlombok:lombok'
}</code></pre></li>
</ul>
<ol>
<li>Preferences(윈도우 File  Settings)  plugin  lombok 검색 설치 실행 (재시작)</li>
<li>Preferences  Annotation Processors 검색  Enable annotation processing 체크 (재시작)</li>
<li>임의의 테스트 클래스를 만들고 @Getter, @Setter 확인</li>
</ol>
<h3 id="정리">정리</h3>
<ul>
<li>최근에는 생성자를 딱 1개두고, <code>@Autowired</code>를 생략하는 방법을 주로 사용한다. 여기에 Lombok 라이브러리의 <code>@RequiredArgsConstructor</code>를 함께 사용하면 기능은 다 제공하면서, 코드는 깔끔하게 사용할 수 있다.</li>
</ul>
<hr />
<h2 id="조회-빈이-2개-이상일-경우">조회 빈이 2개 이상일 경우?</h2>
<ul>
<li><code>@Autowired</code>는 타입(Type)으로 조회한다.<pre><code class="language-java">@Autowired
private DiscountPolicy discountpolicy</code></pre>
</li>
<li>타입으로 조회하기 때문에 마치 아래의 코드와 유사하게 동작<pre><code class="language-java">ac.getBean(DiscountPolicy.class);</code></pre>
</li>
<li>타입으로 조회하게 될 경우 선택된 빈이 2개 이상일 때 문제가 발생한다.
<code>DiscountPolicy</code>의 하위 타입인 <code>FixDiscountPolicy</code> <code>RateDiscountPolicy</code> 둘다 스프링 빈으로 선언해보면<pre><code>@Component
public class FixDiscountPolicy extends DiscountPolicy
</code></pre></li>
</ul>
<p>@Component
public class RateDiscountPolicy extends DiscountPolicy</p>
<pre><code>
- 이렇게 선언가능
```java
@Autowired
private DiscountPolicy discountPolicy</code></pre><ul>
<li>이렇게 의존관계 자동 주입을 실행하면
<code>NoUniqueBeanDefinitionException</code> 이 발생한다. 오류 메시지가 하나의 빈을 기대했는데 2개가 발견되었다고 나올 것임.</li>
<li>이때 하위 타입으로 지정할 수 도 있지만, 하위 타입으로 지정하는 것은 DIP를 위배하고 유연성이 떨어진다. 그리고 이름만 다르고, 완전히 똑같은 타입의 스프링 빈이 2개 있을때 해결이 안된다.</li>
<li>스프링 빈을 수동등록해서 문제를 해결할 수도 있지만, 의존 관계 자동 주입에서 해결하는 여러 방법이 존재한다. 지금부터 알아보자.</li>
</ul>
<hr />
<h2 id="해결방법">해결방법</h2>
<ol>
<li><code>@Autowired</code>필드 명 매칭</li>
<li><code>@Qualifier</code> -&gt; <code>@Qualifier</code>끼리 매칭 -&gt; 빈 이름 매칭</li>
<li><code>@Primary</code> 사용</li>
</ol>
<h3 id="autowired-필드-명-매칭">@Autowired 필드 명 매칭</h3>
<ul>
<li>@Autowired는 타입 매칭을 시도하고, 이때 여러 빈이 있으면 필드 이름, 파라미터 이름으로 빈 이름을 추가 매칭한다.<pre><code class="language-java">//기존 코드
@Autowired
private DiscountPolicy discountPolicy
</code></pre>
</li>
</ul>
<p>//필드 명을 빈 이름으로 변경
private DiscountPolicy rateDiscountPolicy</p>
<pre><code>- 필드 명이 ``rateDiscountPoliccy``이므로 정상 주입된다.
- **필드 명 매칭은 타입 매칭을 시도하고 그 결과에 여러 빈이 있을 때 추가로 동작하는 기능이다.**

#### @Autowired 매칭 정리
1. 타입 매칭
2. 타입 매칭의 결과가 2개 이상일 때 필드 명, 파라미터 명으로 빈 이름 매칭
***

### Qualifier 사용
- ``@Qualifier``는 추가 구분자를 붙여주는 방법이다. 주입시 추가적인 방법을 제공하는 것이지 빈 이름을 변경하는 것은 아니다.

```java
//빈 등록시 @Qualifier
@Component
@Qualifier(&quot;mainDiscountPolicy&quot;)
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
@Qualifier(&quot;fixDiscountPolicy&quot;)
public class FixDiscountPolicy implements DiscountPolicy {}</code></pre><ul>
<li>주입시에 <code>@Qualifier</code>를 붙여주고 등록한 이름을 적어준다.</li>
</ul>
<pre><code class="language-java">//생성자 자동 주입
 @Autowired
 public OrderServiceImpl(MemberRepository memberRepository,
             @Qualifier(&quot;mainDiscountPolicy&quot;) DiscountPolicy discountPolicy) {

     this.memberRepository = memberRepository;
     this.discountPolicy = discountPolicy;
 }

 // 수정자 자동 주입
  public DiscountPolicy setDiscountPolicy(@Qualifier(&quot;mainDiscountPolicy&quot;) 
    DiscountPolicy discountPolicy) {

     this.discountPolicy = discountPolicy;
 }</code></pre>
<p><code>@Qualifier</code>로 주입할 때 <code>@Qualifier(&quot;mainDiscountPolicy&quot;)</code>를 못찾으면 어떻게 될까? 그러면 mainDiscountPolicy라는 이름의 스프링 분을 추가로 찾는다. 하지만 @Qualifier는 @Qualifier를 찾는 용도로만 사용하는 것이 좋다.</p>
<pre><code class="language-java"> @Bean
 @Qualifier(&quot;mainDiscountPolicy&quot;)
 public DiscountPolicy discountPolicy() {
     return new ...
 }</code></pre>
<h3 id="qualifier-정리">Qualifier 정리</h3>
<ol>
<li>@Qualifier끼리 매칭</li>
<li>빈 이름 매칭</li>
<li>NoSuchBeanDefinitionException 예외 발생</li>
</ol>
<hr />
<h3 id="primary-사용-중요">Primary 사용 (중요)</h3>
<ul>
<li><code>@Primary</code>는 우선순위를 정하는 방법, @Autowired 시에 여러 빈이 매칭되면 @Primary가 우선권을 가진다.
<code>rateDiscountPolicy</code>가 우선권을 가지도록 하자</li>
</ul>
<pre><code class="language-java">@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
public class FixDiscountPolicy implements DiscountPolicy {}</code></pre>
<pre><code class="language-java">// 사용 코드
// 생성자
@Autowired
public OrderServiceImpl(MemberRepository memberRepository,
 DiscountPolicy discountPolicy) {
     this.memberRepository = memberRepository;
     this.discountPolicy = discountPolicy;
 }

 //수정자
@Autowired
 public DiscountPolicy setDiscountPolicy(DiscountPolicy discountPolicy) {
     this.discountPolicy = discountPolicy;
 }</code></pre>
<ul>
<li><p>위의 코드를 실행해보면 문제 없이 @Primary가 잘 동작하는 것을 확인할 수 있다.</p>
</li>
<li><p>여기까지 확인해보면 <code>@Primary</code>와 <code>@Qualifier</code>중에 어떤 것을 사용하면 좋을지 고민이 되는데 <code>@Qualifier</code>의 단점은 주입 받을 때 다음과 같이 모든 코드에 <code>@Qualifier</code>를 붙여주어야 한다는 점이 있기 때문에 <code>@Primary</code>가 사용하기 더 쉬워보이긴 한다.</p>
</li>
</ul>
<pre><code class="language-java"> @Autowired
 public OrderServiceImpl(MemberRepository memberRepository,
                        @Qualifier(&quot;mainDiscountPolicy&quot;) DiscountPolicy discountPolicy) {
     this.memberRepository = memberRepository;
     this.discountPolicy = discountPolicy;
 }</code></pre>
<ul>
<li>이렇게 @Qualifier를 붙여서 사용하지 않기 때문에 @Primary가 사용하기 더 편하다.</li>
</ul>
<h3 id="primary-qualifier-활용">@Primary, @Qualifier 활용</h3>
<ul>
<li>코드에서 자주 사용하는 메인 데이터베이스의 커넥션을 획득하는 스프링 빈이 있고, 코드에서 특별한 기능으로 가끔 사용하는 서브 데이터베이스의 커넥션을 획득하는 스프링 빈이 있다고 할때 </li>
<li>메인 데이터베이스의 커넥션을 획득하는 스프링 빈은 @Primary를 적용해서 조회하는 곳에서 @Qualifier 지정 없이 편리하게 조회하고, 서브 데이터베이스 커넥션 빈을 획득할 때는 @Qualifer를 지정해서 명시적으로 획득 하는 방식으로 사용하면 코드를 깔끔하게 유지할 수 있게된다.</li>
</ul>
<h3 id="우선순위">우선순위</h3>
<ul>
<li><code>@Primary</code>는 기본값 처럼 동작하는 것이고, <code>@Qualifer</code>는 매우 상세하게 동작한다. 이런 경우 어떤 것이 우선권을 가져갈까? 스프링 자동보다는 수동이, 넓은 범위의 선택권 보다는 좁은 범위의 선택권이 우선 순위가 높다. 따라서 <code>@Qualifer</code>가 우선권이 높다.
</li>
</ul>
<h3 id="📭-참고출처">📭 참고출처</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 핵심원리 - 기본편</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8</a></li>
</ul>