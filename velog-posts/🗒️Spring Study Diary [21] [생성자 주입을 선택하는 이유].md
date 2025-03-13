<h2 id="옵션-처리">옵션 처리</h2>
<ul>
<li>주입할 스프링 빈이 없어도 동작해야할 때가 존재</li>
<li><code>@Autowired</code>만 사용하면 required 옵션의 기본값이 true로 되어있기 때문에 자동 주입 대상이 없으면 오류가 발생<h3 id="자동-주입-대상을-옵션으로-처리하는-방법">자동 주입 대상을 옵션으로 처리하는 방법</h3>
</li>
<li><code>@Autowired(required = false)</code> : 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출이 안된다.</li>
<li><code>org.springframework.lang.@Nullable</code> : 자동 주입할 대상이 없으면 null값이 입력되게 하는 방법.</li>
<li><code>Optional&lt;&gt;</code> : 자동 주입할 대상이 없으면 <code>Optional.empty</code>가 입력된다.</li>
</ul>
<pre><code class="language-java"> //호출 안됨
@Autowired(required = false)
 public void setNoBean1(Member member) {
    System.out.println(&quot;setNoBean1 = &quot; + member);
 }

 //null호출
 @Autowired
 public void setNoBean2(@Nullable Member member) {
    System.out.println(&quot;setNoBean2 = &quot; + member);
 }

 //Optional.empty 호출
@Autowired(required = false)
 public void setNoBean3(Optional&lt;Member&gt; member) {
     System.out.println(&quot;setNoBean3 = &quot; + member);
 } </code></pre>
<ul>
<li>위 코드에서 <code>Member는 스프링 빈이 아니다.</code></li>
<li>setNoBean1()은 @Autowired(required = false) 이므로 호출 자체가 안된다.</li>
</ul>
<blockquote>
<p><strong>참고</strong></p>
</blockquote>
<ul>
<li>@Nullable, Optional은 스프링 전반에 걸쳐서 지원된다. 예를 들어 생성자 자동 주입에서 특정 필드에서만 사용해도 된다.</li>
</ul>
<hr />
<h2 id="생성자-주입-선택하기">생성자 주입 선택하기</h2>
<ul>
<li>이전 포스트에서 DI 방법에는 여러가지가 있다는 것을 배웠다. </li>
<li>스프링을 포함한 DI 프레임워크 대부분은 생성자 주입을 권장한다. 나도 생성자 주입을 기본으로 선택하자</li>
</ul>
<h3 id="생성자-주입을-선택하는-이유">생성자 주입을 선택하는 이유</h3>
<ul>
<li><strong>불변</strong><blockquote>
<ul>
<li>대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료시점까지 의존관계를 변경할 일이 없다. 오히려 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안된다. (불변해야 한다.) </li>
<li>수정자 주입을 사용하면, setXxx 메서드를 public으로 열어두어야 한다. </li>
<li>누군가 실수로 변경할 수 도 있고, 변경하면 안되는 메서드를 열어두는 것은 좋은 설계 방법이 아니다. </li>
<li>생성자 주입은 객체를 생성할 때 딱 1번만 호출되므로 이후에 호출되는 일이 없다. 따라서 불변하게 설계할 수 있다.</li>
</ul>
</blockquote>
</li>
</ul>
<ul>
<li><strong>누락</strong><ul>
<li>프레임워크 없이 순수한 자바 코드를 단위 테스트 하는 경우에 (생성자로 DI 선택)</li>
<li><code>@Autowired</code>가 프레임워크 안에서 동작할 때는 의존관계가 없으면 오류가 발생하지만, 지금은 프레임워크 없이 순수한 자바 코드로 단위 테스트를 한다고 할때 테스트를 수행하면 실행은 된다.</li>
<li>그러나 막상 실행을 하게되면 실행 결과는 NPE(Null Point Exception)이 발생한다.</li>
<li>생성자 주입을 사용하면 주입 데이터가 누락 했기에 <strong>컴파일 오류 발생</strong>


</li>
</ul>
</li>
</ul>
<h3 id="final-키워드">final 키워드</h3>
<ul>
<li><p>생성자 주입을 사용하면 필드에 final 키워드를 사용할 수 있다. 그래서 생성자에서 혹시라도 값이 설정되지 않는 오류를 컴파일 시점에 막아준다.</p>
<pre><code class="language-java">@Component
public class OrderServiceImpl implements OrderService {
   private final MemberRepository memberRepository;
  private final DiscountPolicy discountPolicy;
  @Autowired
   public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
       this.memberRepository = memberRepository;
  }
//...
}</code></pre>
</li>
<li><p>위의 코드에서 필수 필드인 discountPolicy에 값을 설정해야 하는데, 이 부분이 누락되었다. 자바는 컴파일 시점에 다음 오류를 발생 시킨다.</p>
</li>
<li><p><code>java : variable discountPolicy might not have been initialized</code></p>
</li>
<li><p><strong>컴파일 오류는 오류중에서 제일 빠르고 좋은 오류인 것을 기억하자</strong></p>

## **정리**</li>
<li><p>생성자 주입 방식을 선택하는 이유는 프레임워크에 의존하지 않고, 순수한 자바 언어의 특징을 잘 살리는 방법이다.</p>
</li>
<li><p>기본으로 생성자 주입을 선택하고, 필수 값이 아닌 경우에는 수정자 주입 방식을 옵션으로 부여하자, 생성자 주입과 수정자 주입을 동시에 사용할 수 있다.</p>
</li>
<li><p>항상 생성자 주입을 선택하자 ! 그리고 가끔 옵션이 필요하면 수정자 주입을 선택하고 필드주입은 사용하지 말자</p>


### 📭 참고출처</li>
<li><p>인프런 김영한 강사님의 스프링 핵심원리 - 기본편</p>
</li>
<li><p>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8</a></p>
</li>
</ul>