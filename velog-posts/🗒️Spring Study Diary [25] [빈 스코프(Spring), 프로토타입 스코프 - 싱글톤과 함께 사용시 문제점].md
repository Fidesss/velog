<h1 id="빈-스코프-bean-scop">빈 스코프? (Bean Scop)</h1>
<blockquote>
<p>스코프 : 빈이 존재할수 있는 범위</p>
</blockquote>
<h3 id="스프링이-지원하는-스코프-종류">스프링이 지원하는 스코프 종류</h3>
<ul>
<li>싱글톤 : 기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프이다.</li>
<li>프로토타입 : 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프</li>
<li>웹 관련 스코프<ul>
<li>request : 웹 요청이 들어오고 나갈때 까지 유지되는 스코프</li>
<li>session : 웹 세션이 생성되고 종료될 때 까지 유지되는 스코프</li>
<li>application : 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프</li>
</ul>
</li>
</ul>
<hr />
<h2 id="프로토타입-스코프">프로토타입 스코프</h2>
<ul>
<li><code>싱글톤</code> 스코프의 빈을 조회하면 스프링 컨테이너는 항상 같은 인스턴스의 스프링 빈을 반환한다.</li>
<li>프로토타입 스코프를 스프링 컨테이너에 조회하면 스프링 컨테이너는 항상 새로운 인스턴스를 생성하여 반환한다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/52c56788-e79f-4524-8025-547ad790f164/image.png" /></p>
<blockquote>
<ol>
<li>싱글톤 스코프의 빈을 스프링 컨테이너에 요청한다.</li>
<li>스프링 컨테이너는 본인이 관리하는 스프링 빈을 반환한다.</li>
<li>이후에 스프링 컨테이너에 같은 요청이 와도 같은 객체 인스턴스의 스프링 빈을 반환한다.</li>
</ol>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/d8760bf4-3f3c-4630-8155-fd8fe116b5e3/image.png" /></p>
<blockquote>
<ol>
<li>프로토타입 스코프의 빈을 스프링 컨테이너에 요청한다.</li>
<li>스프링 컨테이너는 이 시점에 프로토타입 빈을 생성하고, 필요한 의존관계를 주입한다.</li>
</ol>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/e07c2232-ed0c-4b34-a885-43f7f8cf7cfb/image.png" /></p>
<blockquote>
<ol start="3">
<li>스프링 컨테이너는 생성한 프로토타입 빈을 클라이언트에 반환한다.</li>
<li>이후에 스프링 컨테이너에 같은 요청이 오면 항상 새로운 프로토타입 빈을 생성해서 반환한다.</li>
</ol>
</blockquote>
<h3 id="정리">정리</h3>
<ul>
<li>핵심은 스프링 컨테이너는 프로토타입 빈을 생성하고, 의존관계주입, 초기화 까지만 처리해준다.</li>
<li>클라이언트에게 빈을 반환하고, 이후 스프링 컨테이너는 생성된 프로토타입 빈을 관리하지 않는다.</li>
<li>프로토타입 빈을 관리할 책임은 프로토타입 빈을 받은 클라이언트에 있다. </li>
<li><code>@PreDestroy</code> 같은 종료 메서드가 호출되지 않는다.</li>
</ul>
<h3 id="특징-정리">특징 정리</h3>
<ul>
<li>스프링 컨테이너에 요청할 때 마다 새로 생성된다.</li>
<li>스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입 그리고 초기화까지만 관여한다.</li>
<li>종료 메서드가 호출되지 않는다.</li>
<li>프로토타입 빈은 프로토타입 빈을 조회한 클라이언트가 관리해야 한다. 종료 메서드에 대한 호출도 클라이언트가 직접 해야한다.</li>
</ul>
<hr />
<h2 id="프로토타입-스코프---싱글톤-빈과-함께-사용시-문제점">프로토타입 스코프 - 싱글톤 빈과 함께 사용시 문제점</h2>
<ul>
<li>스프링 컨테이너에 프로토타입 스코프의 빈을 요청하면 항상 새로운 객체 인스턴스를 생성해서 반환한다.</li>
<li>싱글 톤 빈과 함께 사용할 때는 의도한 대로 잘 동작하지 않는다.</li>
</ul>
<h3 id="addcount로-프로토타입-빈을-호출할때마다-count를--1한다고-해보자">addCount()로 프로토타입 빈을 호출할때마다 count를 + 1한다고 해보자</h3>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/15ccce0e-dd25-4cfb-8e0f-f90e6e1588f3/image.png" /></p>
<blockquote>
<ol>
<li>클라이언트 A는 스프링 컨테이너에 프로토타입 빈을 요청한다.</li>
<li>스프링 컨테이너는 프로토타입 빈을 새로 생성해서 반환(x01)한다. 해당 빈의 count 필드 값은 0이다.</li>
<li>클라이언트는 조회한 프로토타입 빈에 addCount()를 호출하면서 count 필드를 +1 한다.
결과적으로 프로토타입 빈(x01)의 count는 1이 된다.</li>
</ol>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/91f7ca17-5c9a-4246-a238-13df5be8e6d8/image.png" /></p>
<blockquote>
<ol>
<li>클라이언트 B는 스프링 컨테이너에 프로토타입 빈을 요청한다.</li>
<li>스프링 컨테이너는 프로토타입 빈을 새로 생성해서 반환(x02)한다. 해당 빈의 count 필드 값은 0이다.</li>
<li>클라이언트는 조회한 프로토타입 빈에 addCount()를 호출하면서 count 필드를 +1 한다.
결과적으로 프로토타입 빈(x02)의 count는 1이 된다.</li>
</ol>
<p><strong>즉 위에서 공부했던 것 처럼 스프링 컨테이너가 프로토타입 빈을 요청하면 항상 새로운 인스턴스를 생성해준다는 것을 확인할 수 있다.</strong></p>
</blockquote>
<hr />
<h3 id="싱글톤-빈에서-프로토타입-빈-사용">싱글톤 빈에서 프로토타입 빈 사용</h3>
<ul>
<li>이번에는 싱글톤 빈이 의존관계 주입을 통해서 프로토타입 빈을 주입받아서 사용하는 예를 공부해보자</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/84ee7108-7e6b-49d5-9541-39a0cfd5326e/image.png" /></p>
<blockquote>
<ul>
<li><code>clientBean</code>은 싱글톤이므로, 보통 스프링 컨테이너 생성 시점에 함께 생성되고, 의존관계 주입도 발생한다.</li>
</ul>
</blockquote>
<ul>
<li><ol>
<li><code>clientBean</code>은 의존관계 자동 주입을 사용한다. 주입 시점에 스프링 컨테이너에 프로토타입 빈을 요청한다.</li>
</ol>
</li>
<li><ol start="2">
<li>스프링 컨테이너는 프로토타입 빈을 생성해서 <code>clientBean</code>에 반환한다. 프로토타입 빈의 count 필드 값은 0이다.</li>
</ol>
</li>
<li>이제 <code>clientBean</code>는 프로토타입 빈을 내부 필드에 보관한다. (정확하게는 참조값을 보관하는 것이다.)</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/c862fbc0-1283-46fb-9b0c-3a34e80e7569/image.png" /></p>
<blockquote>
<ul>
<li>클라이언트 A는 <code>clientBean</code>을 스프링 컨테이너에 요청해서 받는다. 싱글톤이므로 항상 같은 <code>clientBean</code>이 반환된다.</li>
</ul>
</blockquote>
<ul>
<li><ol start="3">
<li>클라이언트 A는 <code>clientBean.login()</code>을 호출한다.</li>
</ol>
</li>
<li><ol start="4">
<li><code>clientBean</code>은 prototypeBean은 <code>addCount()</code>를 호출해서 프로토타입 빈의 count를 증가한다. count값이 1이 된다.</li>
</ol>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/64f6e0dd-a47c-455f-9403-a3feb245571c/image.png" /></p>
<blockquote>
<ul>
<li>클라이언트 B는<code>clientBean</code>을 스프링 컨테이너에 요청해서 받는다. 싱글톤이므로 항상 같은 <code>clientBean</code>이 반환된다.</li>
</ul>
</blockquote>
<ul>
<li><strong>여기서 중요한 점은 <code>clientBean</code>이 내부에 가지고 있는 프로토타입 빈은 이미 과거에 주입이 끝난 빈이다. 주입 시점에 스프링 컨테이너에 요청해서 프로토타입 빈이 새로 생성이 된 것이지, 사용할 때마다 새로 생성되는 것이 아니라는 것이다.</strong></li>
<li><ol start="5">
<li>클라이언트 B는 <code>clientBean.login()</code>을 호출한다.</li>
</ol>
</li>
<li><ol start="6">
<li><code>clientBean</code>은 prototypeBean의 <code>addCount()</code>를 호출해서 프로토타입 빈의 count를 증가한다. 원래 count값이 1이었으므로 2가 된다.</li>
</ol>
</li>
</ul>
<p><code>테스트 코드 아래에 생성예정</code></p>
<blockquote>
<p><strong>문제점</strong>
스프링은 일반적으로 싱글톤 빈을 사용하므로, 싱글톤 빈이 프로토타입 빈을 사용하게 된다. 그런데 싱글톤 빈은 생성 시점에만 의존관계 주입을 받기 때문에, 프로토타입 빈이 새로 생성되기는 하지만, 싱글톤 빈과 함께 계속 유지되는 것이 문제이다.</p>
</blockquote>
<blockquote>
<p><strong>참고</strong></p>
</blockquote>
<ul>
<li>여러 빈에서 같은 프로토타입 빈을 주입 받으면, 주입 받는 시점에 각각 새로운 프로토타입 빈이 생성된다.</li>
<li>예시<ul>
<li>clinet A -&gt; prototypeBean@x01</li>
<li>client B -&gt; prototypeBean@x02</li>
<li>client A, client B가 각각 의존관계 주입을 받으면 각각 다른 인스턴스의 프로토타입 빈을 주입 받는다. </li>
<li>물론 사용할 때마다 새로 생성되지는 않는다.</li>
</ul>
</li>
</ul>
<h4 id="이렇게-문제점에-대해서-알아보았다-다음-포스트에서는-해결법에-대해서-공부해보잠">이렇게 문제점에 대해서 알아보았다. 다음 포스트에서는 해결법에 대해서 공부해보잠.</h4>


<pre><code class="language-java">//테스트 코드
 public class SingletonWithPrototypeTest1 {
 @Test
 void singletonClientUsePrototype() {
     AnnotationConfigApplicationContext ac = new 
    AnnotationConfigApplicationContext(ClientBean.class, PrototypeBean.class);
     ClientBean clientBean1 = ac.getBean(ClientBean.class);
     int count1 = clientBean1.logic();
     assertThat(count1).isEqualTo(1);
     ClientBean clientBean2 = ac.getBean(ClientBean.class);
     int count2 = clientBean2.logic();
     assertThat(count2).isEqualTo(2);
  }

 static class ClientBean {
     private final PrototypeBean prototypeBean;
     @Autowired
     public ClientBean(PrototypeBean prototypeBean) {
        this.prototypeBean = prototypeBean;

    }
     public int logic() {
        prototypeBean.addCount();
         int count = prototypeBean.getCount();
         return count;
    }
}

 @Scope(&quot;prototype&quot;)
 static class PrototypeBean {
     private int count = 0;
     public void addCount() {
            count++;
    }
     public int getCount() {
         return count;
    }
     @PostConstruct
     public void init() {
         System.out.println(&quot;PrototypeBean.init &quot; + this);
    }
    @PreDestroy
     public void destroy() {
         System.out.println(&quot;PrototypeBean.destroy&quot;);
    }
  }
 }</code></pre>
<h3 id="📭-참고출처">📭 참고출처</h3>
<ul>
<li>인프런 김영한 강사님의 스프링 핵심원리 - 기본편</li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8</a></li>
</ul>