<h2 id="다양한-의존관계-주입-방법">다양한 의존관계 주입 방법</h2>
<ul>
<li>의존관계 주입은 크게 4가지 방법이 있음.<ul>
<li>생성자 주입</li>
<li>수정자 주입 (setter 주입)</li>
<li>필드 주입</li>
<li>일반 메서드 주입</li>
</ul>
</li>
</ul>
<h3 id="생성자-주입">생성자 주입</h3>
<ul>
<li><p>이름 그대로 생성자를 통해서 의존 관계를 주입 받는 방법이다.</p>
</li>
<li><p>특징</p>
<ul>
<li><p>생성자 호출 시점에 딱 1번만 호출되는 것이 보장된다.</p>
</li>
<li><p><strong>불변, 필수</strong> 의존관계에 사용</p>
<pre><code class="language-java">@Component
public class OrderServiceImpl implements OrderService {
private final MemberRepository memberRepository;
private final DiscountPolicy discountPolicy;

@Autowired
public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy){
 this.memberRepository = memberRepository;
 this.discountPolicy = discountPolicy;
}
}</code></pre>
</li>
</ul>
</li>
<li><p><strong>중요</strong> : 생성자가 딱 1개만 있으면 @Autowired를 생략해도 자동 주입 된다. 물론 스프링 빈에만 해당한다.</p>
</li>
</ul>
<pre><code class="language-java"> @Component
 public class OrderServiceImpl implements OrderService {
     private final MemberRepository memberRepository;
     private final DiscountPolicy discountPolicy;

 public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
     this.memberRepository = memberRepository;
     this.discountPolicy = discountPolicy;
    }
 }</code></pre>
<h3 id="수정자-주입setter-주입">수정자 주입(setter 주입)</h3>
<ul>
<li><p>setter라 불리는 필드의 값을 변경하는 수정자 메서드를 통해서 의존관계를 주입하는 방법이다.</p>
</li>
<li><p>특징</p>
<ul>
<li><p><strong>선택, 변경</strong> 가능성이 있는 의존관계에 사용</p>
</li>
<li><p>Java Beam Property 규약의 수정자 메서드 방식을 사용하는 방법이다.</p>
<pre><code class="language-java">@Component
public class OrderServiceImpl implements OrderService {
private MemberRepository memberRepository;
private DiscountPolicy discountPolicy;

@Autowired
public void setMemberRepository(MemberRepository memberRepository) {
  this.memberRepository = memberRepository;
}

@Autowired
public void setDiscountPolicy(DiscountPolicy discountPolicy) {
  this.discountPolicy = discountPolicy
}
}</code></pre>
</li>
</ul>
</li>
</ul>
<blockquote>
<p>참고</p>
</blockquote>
<ul>
<li><code>@Autowired</code>의 기본 동작은 주입할 대상이 없으면 오류가 발생한다. 주입할 대상이 없어도 동작하게 하려면 <code>@Autowired(required = false)</code> 로 지정하면 된다.</li>
<li>자비빈 프로퍼티, 자바에서는 과거부터 필드의 값을 직접 변경하지 않고, setXxx, getXxx 라는 메서드를 통해서 값을 읽거나 수정하는 규칙을 만들었는데, 그것이 자바빈 프로퍼티 규약이다.</li>
</ul>
<h3 id="필드-주입">필드 주입</h3>
<ul>
<li>이름 그대로 필드에 바로 주입하는 방법</li>
<li>특징<ul>
<li>코드가 간결해서 많은 개발자들을 유혹함, 외부에서 변경이 불가능해서 테스트 하기 힘들다는 치명적인 단점이 존재한다.</li>
<li>DI 프레임워크가 없으면 아무것도 할 수 없다.</li>
<li>웬만하면 사용하지 말자<ul>
<li>애플리케이션의 실제 코드와 관계 없는 테스트 코드</li>
<li>스프링 설정을 목적으로 하는 @Configuration 같은 곳에서만 특별한 용도로 사용</li>
</ul>
</li>
</ul>
</li>
</ul>
<pre><code class="language-java">@Component
public class OrderServiceImpl implements OrderService {
    @Autowired
    private MemberRepository memberRepository;

    @Autowired
    private DiscountPolicy discountPolicy;
}</code></pre>
<blockquote>
<p>참고</p>
<ul>
<li>순수한 자바 테스트 코드에는 당연히 @Autowired가 동작하지 않는다. <code>@SpringBootTest</code>처럼 스프링 컨테이너를 테스트에 통합한 경우에만 가능하다.</li>
</ul>
</blockquote>
<p>``아래 코드와 같이 @Bean에서 파라미터에 의존관계는 자동 주입된다. 수동 등록시 자동 등록된 빈의 의존관계가 필요할 때 문제를 해결할 수 있다.</p>
<pre><code class="language-java">@Bean
OrderService orderService(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
    return new OrderServiceImpl(memberRepository, discountPolicy);
}</code></pre>
<h3 id="일반-메서드-주입">일반 메서드 주입</h3>
<ul>
<li>일반 메서드를 통해서 주입 받을 수 있다.</li>
<li>특징<ul>
<li>한번에 여러 필드를 주입 받을 수 있다.</li>
<li>일반적으로 잘 사용하지 않는다.</li>
</ul>
</li>
</ul>
<pre><code class="language-java">@Component
public class OrderServiceImple implement OrderService {
    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;

    @Autowired
    public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy){
        this.memberRepository = memberRepository;
        this.discountPolicy = disco;untPolicy;
    }
}</code></pre>
<blockquote>
<p><strong>참고</strong></p>
</blockquote>
<ul>
<li>의존관계 자동 주입은 스프링 컨테이너가 관리하는 스프링 빈이어야 동작한다. 스프링 빈이 아닌 <code>Member</code> 같은 클래스에서 <code>@Autowired</code> 코드를 적용해도 아무 기능도 동작하지 않는다.</li>
</ul>


<h3 id="📭-참고출처">📭 참고출처</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 핵심원리 - 기본편</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8</a></li>
</ul>