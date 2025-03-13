<h2 id="싱글톤-컨테이너">싱글톤 컨테이너</h2>
<ul>
<li>스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서, 객체 인스턴스를 싱글톤으로 관리한다.</li>
<li>스프링 빈은 스프링 컨테이너가 싱글톤으로 관리하는 빈이다.</li>
</ul>
<br />

<blockquote>
<p>스프링 컨테이너</p>
<ul>
<li>스프링 컨테이너는 싱글턴 패턴을 적용하지 않아도, 객체 인스턴스를 싱글톤으로 관리해준다.</li>
<li>스프링 컨테이너는 싱글톤 컨테이너 역할을 한다.</li>
<li>싱글톤 객체를 생성하고 관리하는 기능을 싱글톤 레지스트리라고 한다.</li>
<li>스프링 컨테이너의 이런 기능 덕분에 싱글턴 패턴의 모든 단점을 해결하면서 객체를 싱글톤으로 유지할 수 있다.</li>
</ul>
</blockquote>
<ul>
<li>싱글톤 패턴을 위한 지저분한 코드가 삽입되지 않는다.</li>
<li>DIP, OCP, TEST, private 생성자로 부터 자유롭게 싱글톤을 사용가능하다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/cara1326/post/50176fbb-b60e-4b5f-bfa3-cf66074179a0/image.png" /></p>
<ul>
<li>스프링 컨테이너가 있기 때문에 고객의 요청이 올 때마다 객체를 생성하는 것이 아닌 이미 만들어진 하나의 객체를 공유하여 효율적으로 재사용할 수 있게된다.</li>
</ul>
<h2 id="싱글톤-방식의-주의점">싱글톤 방식의 주의점</h2>
<ul>
<li><p>싱글톤 패턴이든, 스프링 같은 싱글톤 컨테이너를 사용할때 객체 인스턴스 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지 (stateful)하게 설계하면 안된다.</p>
</li>
<li><p>무상태 (stateless)로 설계해야 한다.</p>
<ul>
<li>특정 클라이언트에 의존적인 필드가 존재해서는 안된다.</li>
<li>특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.</li>
<li>가급적 읽기만 가능해야 한다.</li>
<li>필드 대신에 자바에서 공유되지 않는 <code>지역변수, 파라미터, ThreadLocal</code>등을 사용해야 한다.</li>
</ul>
</li>
<li><p>스프링 빈의 필드에 공유 값을 설정하면 매우 큰 장애가 발생할 수 있다.</p>
</li>
</ul>
<pre><code class="language-java">public class StatefulService {
        private int price; //상태를 유지하는 필드

public void order(String name, int price) {
        System.out.println(&quot;name = &quot; + name + &quot; price = &quot; + price);
        this.price = price; //여기가 문제!
       }

 public int getPrice() {
         return price;
    }
 }
</code></pre>
<ul>
<li><p>위와같은 싱글톤 방식으로 코드를 짜게되면 공유 필드인 <code>price</code>를 공유하기 때문에 문제가 발생할 수 있다.</p>
</li>
<li><p>2명의 고객이 주문을 했다고 가정해보자.</p>
<ul>
<li>고객 A는 10000원을 주문 (Thread A)</li>
<li>고객 B는 20000원을 주문 (Thread B)</li>
</ul>
</li>
<li><p>이 경우 고객 A는 10000원을 주문했지만 후에 parameter로 들어온 고객B의 20000원이 고객 A의 price가 된다. 즉 매우 큰 장애가 발생</p>
</li>
<li><p>이렇게 매우 큰 장애가 일어날 수 있기 때문에 <code>공유 필드</code>는 조심해야한다. 스프링 빈은 항상 무상태(stateless)로 설계해야 한다.</p>
</li>
</ul>
<h3 id="📭-참고출처">📭 참고출처</h3>
<ul>
<li><code>인프런 김영한 강사님의 스프링 핵심원리 - 기본편</code></li>
<li>링크 : <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8">https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8</a></li>
</ul>