<h2 id="dto">DTO</h2>
<ul>
<li>Data Transfer Object</li>
<li>주로 서로 다른 계층 (ex : controller, Service, View) 간의 데이터 교환을 위해서 사용한다.</li>
<li>object를 다른 형식으로 변환해서 보내고 싶을때 사용</li>
</ul>
<pre><code class="language-java">class Data01 {
  public String username;
  public String displayName;
}</code></pre>
<pre><code class="language-java">@GetMapping(&quot;/user/1&quot;)
@ResponseBody
public Data01 user() {
  var a = memberRepository.findById(1L);
  var data = new Data01();
  data.username = a.get().getUsername();
  data.displayName = a.get().getDisplayName();
  return data;
} </code></pre>
<ul>
<li>이런식으로 데이터를 거쳐서 넘기고 싶을때도 사용이 가능하다.<pre><code class="language-java">class Data {
public String username;
public String displayName;
Data(String a, String b){
  this.username = a;
  this.displayName = b;
}
}</code></pre>
</li>
<li>이런식으로 생성자를 이용하여 만드는 경우도 있다.</li>
</ul>
<h3 id="특징">특징</h3>
<ul>
<li>DTO는 비즈니스 로직을 포함하지 않는다.</li>
<li>주로 데이터의 저장, 검색, 직렬화, 역직렬화르 위한 메서드만을 가진다.</li>
<li>일반적으로 getter와 setter 메서드만을 포함하는 단순한 객체이다.</li>
</ul>
<h3 id="장점">장점</h3>
<ul>
<li>타입체크가 쉽다. (클래스라서 정확히 어떤 내용이 들어가있는지 알기 때문), (map으로 만들면 여기에 무슨 자료가 들어있는지 파악이 어렵다.)</li>
<li>재사용이 쉽다. (클래스로 만들어두면 나중에 비슷한 클래스가 하나 더 필요할 때 재사용하기 쉽다.<ul>
<li>만약 id와, name만 보내주는 api를 만들었을때 이 api를 업그레이드한다고 하자. 기존 api를 수정하면 되겠지만 급격하게 기존 api를 수정하면 프론트엔드 측에서 무언가가 고장나는 경우도 존재한다. 그래서 새로운 api를 하나 더 만드는 것이 좋게되는데 비슷한 기능을하는 api를 하나 더 만드는데 DTO를 사용하면 기존 코드를 재활용 할 수 있기 때문에 장점이다. (생성자를 이용해도 되고 extends로 기존 클래스 복사해도 됨)</li>
</ul>
</li>
</ul>
<h3 id="다른곳에서도-사용가능">다른곳에서도 사용가능</h3>
<ul>
<li>DTO는 유저에게 데이터를 보낼 때만 사용하는 것이 아니라 데이터를 특정 object형태로 변환할 때 전부 사용해도 된다.</li>
<li>ex) Serivce에 있는 코드에서 DB에서 찾은 무언가를 반환할때 DTO로 원하는 변수만 담아서 반환해줄 수도 있다. </li>
</ul>
<hr />
<h3 id="dto-대신쓰는-record-키워드">DTO 대신쓰는 record 키워드</h3>
<ul>
<li>DTO를 만드는 것이 귀찮으면 record라는 키워드도 존재한다. </li>
<li>클래스를 하나 빠르게 만들어달라는 의미를 가지고 있다.<pre><code class="language-java">public record people(String name, Integer age){}</code></pre>
</li>
<li>이렇게 될 경우 name, age라는 속성을 가진 Person 클래스를 하나 생성해준다.</li>
<li>그런데 constructor, getter, toString()등도 자동생성</li>
<li>하지만 setter 이러한 값변경은 안된다.</li>
</ul>