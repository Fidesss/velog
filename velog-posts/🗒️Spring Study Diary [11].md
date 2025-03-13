<h2 id="코드분리하기">코드분리하기</h2>
<ul>
<li>함수 하나에는 한가지 기능만 담는것을 권장</li>
<li>하나의 클래스에는 비슷한 기능의 함수들만 보관하는것이 좋음</li>
<li>Ex) Data 입출력 기능, html 파일로 보내주는 기능 등등..<h2 id="service-레이어">@Service 레이어</h2>
</li>
<li><h3 id="service란">Service란?</h3>
<blockquote>
<p><code>검사</code>, <code>DB입출력</code> 등을 하는 것을 <code>비즈니스 로직</code>이라 한다.
<code>비즈니스로직</code>을 담는 클래스를 Service라고 부른다.</p>
</blockquote>
</li>
</ul>
<pre><code class="language-java">//[itemService.java]
@Service
@RequiredArgsConstructor
public class ItemService {
    private final ItemRepository itemRepository;

    public void saveItem(String title, Integer price) {
        Item item = new Item();
        item.setTitle(title);
        item.setPrice(price);
        itemRepository.save(item);
    }
}</code></pre>
<ul>
<li>이런식으로 Service 파일을 만들어 비슷한 기능을 하는 함수들을 모아서 관리<h3 id="주의할-점">주의할 점</h3>
</li>
<li><code>java</code>에서는 다른 클래스의 함수를 사용할 때 <code>new 키워드</code>를 사용하여 object를 생성하여 접근해 함수를 사용한다. 하지만 예를들어 <code>/add</code> 요청이 들어올 때마다 새로운 object를 사용하면 부하가 걸리기 마련이다. 그렇기 때문에 <code>@Service, @Repository, @Component</code>등의 <code>annotaion</code>을 사용하는 클래스를 이용하면 <code>new 키워드</code>로 새로운 <code>object</code>를 생성할 필요가 없어진다.</li>
<li>즉 <code>object</code>를 한번만 생성하고 그걸 변수에 저장해놓았다가 계속 재사용하는식으로 사용하는 것 <code>아래 코드와 같이</code><pre><code class="language-java">//[Controller file]
private final ItemService itemService;
@PostMapping(&quot;/add&quot;)
  String addPost(@RequestParam String title, @RequestParam Integer price) {
      itemService.saveItem(title, price);
      return &quot;redirect:/list&quot;;
  }</code></pre>
</li>
</ul>
<h2 id="dependency-injection">Dependency injection</h2>
<ul>
<li>Dependency Injection (DI)은 객체 간의 의존 관계를 외부에서 결정하고 주입하는 디자인 패턴이다. 이는 객체의 생성과 사용의 관심사를 분리하는 프로그래밍 설계 방식으로, 코드의 결합도를 낮추고 유연성을 높이는 데 도움을 준다.</li>
<li><strong>장점</strong><ul>
<li>코드 재사용성 증가</li>
<li>테스트 용이성 향상</li>
<li>객체 간 결합도 감소 (<code>클래스간 커플링</code>)</li>
<li>유지보수성 개선</li>
</ul>
</li>
</ul>
<h2 id="spring-ioc-container">Spring IOC Container</h2>
<ul>
<li><p>Spring IoC (Inversion of Control) Container는 객체의 생성과 의존성 관리를 담당하는 핵심 컴포넌트 이다. 이 컨테이너는 Spring Framework의 중요한 요소로, 애플리케이션에서 객체(주로 Bean)의 생명주기를 관리한다.</p>
</li>
<li><p>IoC Container의 역할</p>
<blockquote>
</blockquote>
<p>객체 생성 및 관리: IoC Container는 Bean 객체의 생성, 초기화, 소멸을 책임집니다. 개발자는 직접 객체를 생성하는 대신, 설정 파일(XML)이나 어노테이션을 통해 Bean을 정의하고 IoC Container에 의해 관리된다.</p>
</li>
<li><p>의존성 주입</p>
<blockquote>
<p>IoC Container는 의존성 주입(Dependency Injection)을 통해 객체들 간의 관계를 설정합니다. 이를 통해 개발자는 객체 간의 의존성을 명시적으로 관리할 필요가 없으며, 컨테이너가 이를 자동으로 처리합니다34.</p>
</blockquote>
<blockquote>
<p>BeanFactory와 ApplicationContext: IoC Container는 두 가지 주요 구현체인 BeanFactory와 ApplicationContext로 나뉨 기본적인 Bean 관리 기능을 제공하며, ApplicationContext는 BeanFactory의 확장으로 추가적인 기능(예: 이벤트 전파, 국제화 지원 등)을 포함한다.</p>
</blockquote>
</li>
<li><h3 id="정리">정리</h3>
<blockquote>
<ul>
<li>Spring에서 object를 하나 뽑아서 알아서 집어서 넣어준다. 그러면 spring이 object를 뽑았으면 보관하는 Container를 Container or IOC Container라고 부른다. </li>
<li>@Service, @Component ... 등등의 annotation을 클래스에 붙여놓으면 이 클래스의 object를 spring이 자동으로 뽑아서 Container에 담아놓으라는 뜻이다.<ul>
<li>spring이 뽑아준 그 object를 Bean 이라고 부르는 것이다.</li>
</ul>
</li>
</ul>
</blockquote>
</li>
</ul>