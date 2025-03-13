<h2 id="annotation-직접-만들어보기">Annotation 직접 만들어보기</h2>
<ul>
<li><code>@Qualifier</code> anntation을 저번 시간에 배웠다. 그런데 <code>@Qualifier(&quot;mainDiscountPolicy&quot;)</code> 이런 식으로 사용할 경우 컴파일 시간에 타입체크가 안된다. 그러면 컴파일 시간에 타입체크가 되게 하려면 어떻게 해야할까?</li>
<li>정답은 annoatation을 만들어서 문제를 해결하는 것이다.<pre><code class="language-java">@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, 
      ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Qualifier(&quot;mainDiscountPolicy&quot;)
public @interface MainDiscountPolicy {
}</code></pre>
</li>
</ul>
<pre><code class="language-java"> @Component
 @MainDiscountPolicy //위에서만든 annotation
 public class RateDiscountPolicy implements DiscountPolicy {}

 //생성자 자동 주입
 @Autowired
 public OrderServiceImpl(MemberRepository memberRepository,
                 @MainDiscountPolicy DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
         this.discountPolicy = discountPolicy;
 }
 //수정자 자동 주입
@Autowired
 public DiscountPolicy setDiscountPolicy(@MainDiscountPolicy DiscountPolicy discountPolicy) {
         this.discountPolicy = discountPolicy;
 }</code></pre>
<ul>
<li>annotation에는 상속이라는 개념이 존재하지 않는다. 이렇게 여러 annotation을 모아서 사용하는 기능은 스프링이 지원해주는 기능이다.</li>
<li><code>@Qualifier</code>뿐만 아니라 다른 annotation들도 함께 조합하여 사용할 수 있다.</li>
</ul>
<hr />
<h2 id="조회한-빈이-모두-필요할때-list-map">조회한 빈이 모두 필요할때 [LIST, MAP]</h2>
<ul>
<li>의도적으로 해당 타입의 스프링 빈이 모두 다 필요한 경우가 있다면 어떻게 해야할까?</li>
<li>강의에서의 예시를 들어보자<ul>
<li>할인서비스를 제공해야한다.</li>
<li>클라이언트가 할인의 종류 (rate, fix)를 선택할 수 있다고 가정해보자</li>
<li>이 경우에 스프링을 사용하면 소위 말하는 전략 패턴을 매우 간단하게 구현할 수 있다고 한다.</li>
<li>실습해보자</li>
</ul>
</li>
</ul>
<pre><code class="language-java"> public class AllBeanTest {

 @Test
 void findAllBean() {
     ApplicationContext ac = new 
    AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);

     DiscountService discountService = ac.getBean(DiscountService.class);
     Member member = new Member(1L, &quot;userA&quot;, Grade.VIP);

 int discountPrice = discountService.discount(member, 10000, &quot;fixDiscountPolicy&quot;);

     assertThat(discountService).isInstanceOf(DiscountService.class);
     assertThat(discountPrice).isEqualTo(1000);
 }

 static class DiscountService {
     private final Map&lt;String, DiscountPolicy&gt; policyMap;
     private final List&lt;DiscountPolicy&gt; policies;

 public DiscountService(Map&lt;String, DiscountPolicy&gt; policyMap, List&lt;DiscountPolicy&gt; policies) {
     this.policyMap = policyMap;
     this.policies = policies;

     System.out.println(&quot;policyMap = &quot; + policyMap);
     System.out.println(&quot;policies = &quot; + policies);
   }

 public int discount(Member member, int price, String discountCode) {
     DiscountPolicy discountPolicy = policyMap.get(discountCode);

     System.out.println(&quot;discountCode = &quot; + discountCode);
     System.out.println(&quot;discountPolicy = &quot; + discountPolicy);

     return discountPolicy.discount(member, price);
        }
    }
 }</code></pre>
<h3 id="로직-분석">로직 분석</h3>
<ul>
<li>DiscountService는 Map으로 모든 DiscountPolicy를 주입받는다. 이때 fixDiscountPolicy, rateDiscountPolicy가 주입된다.</li>
<li>discount ( ) 메서드는 discountCode로 &quot;fixDiscountPolicy&quot;가 넘어오면 map에서 rateDiscountPolicy 스프링 빈을 찾아서 실행한다.</li>
</ul>
<h3 id="주입-분석">주입 분석</h3>
<ul>
<li>Map&lt;String, DiscountPolicy&gt; : map의 키에 스프링 빈의 이름을 넣어주고, 그 값으로 DiscountPolicy 타입으로 조회한 모든 스프링 빈을 담아준다.</li>
<li>List <code>&lt;DiscountPolicy&gt;</code> : DiscountPolicy 타입으로 조회한 모든 스프링 빈을 담아준다.</li>
<li>만약 해당하는 타입의 스프링 빈이 없으면, 빈 컬렉션이나 Map을 주입한다.</li>
</ul>
<p><strong>참고</strong></p>
<blockquote>
<p>스프링 컨테이너를 생성하면서 스프링 빈 등록하기</p>
</blockquote>
<ul>
<li>스프링 컨테이너는 생성자에 클래스 정보를 받는다. 여기에 클래스 정보를 넘기면 해당 클래스가 스프링 빈으로 자동 등록된다.</li>
<li><code>new AnnotationConfigApplicationContext(AutoAppConfig.class,DiscountService.class);</code></li>
<li>이 코드는 다시 2가지로 나누어 이해할 수 있다.<ul>
<li><code>new AnnotationConfigApplicationContext()</code>를 통해 스프링 컨테이너를 생성한다.</li>
<li><code>AutoAppConfig.class</code>,<code>DiscountService.class</code>를 파라미터로 넘기면서 해당 클래스를 자동으로 스프링 빈으로 등록한다.</li>
</ul>
</li>
<li>정리<ul>
<li>스프링 컨테이너를 생성하면서, 해당 컨테이너에 동시에 AutoConfig, DiscountService를 스프링 빈으로 자동 등록한다.</li>
</ul>
</li>
</ul>


<h3 id="📭-참고출처">📭 참고출처</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 핵심원리 - 기본편</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8</a></li>
</ul>