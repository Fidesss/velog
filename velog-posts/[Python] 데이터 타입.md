<h1 id="빌트인-데이터-타입">빌트인 데이터 타입</h1>
<ul>
<li>언어 자체에서 제공하는 데이터 타입과 컬렉션 데이터 타입이 존재</li>
</ul>
<h2 id="기본-데이터-타입">기본 데이터 타입</h2>
<blockquote>
<ol>
<li>정수형</li>
<li>부동소수형</li>
<li>문자열</li>
</ol>
</blockquote>
<h3 id="정수형">정수형</h3>
<ul>
<li>양과 음의정수, 0을 포함<h3 id="부동소수형">부동소수형</h3>
</li>
<li>소수를 저장할 때 사용</li>
<li>엡실론을 포함한 연산에 주의하기 (아래에서 추가적으로 설명)<h3 id="문자열">문자열</h3>
</li>
<li>문자열을 저장하기 위해 사용<pre><code class="language-python">A = &quot;This is String type&quot;</code></pre>
</li>
</ul>
<h2 id="컬렉션-데이터-타입">컬렉션 데이터 타입</h2>
<blockquote>
<ol>
<li>변경할수 있는 객체 (Mutable object)</li>
<li>변경할 수 없는 객체 (Immutable object)</li>
</ol>
</blockquote>
<h3 id="mutable-object">Mutable object</h3>
<ul>
<li>객체 생성 후 객체를 수정할 수 있다.</li>
<li>대표적인 뮤터블 객체로 <strong>리스트, 딕셔너리, 셋</strong>이 존재<h4 id="예시">예시</h4>
<pre><code class="language-python">my_list = [1,2,3,4,5]        # 리스트 객체 생성, [1,2,3,4,5]
my_list[4] = 6                # 리스트 원소 변경
print(my_list)                # [1, 2, 3, 4, 6]</code></pre>
<h3 id="immutable-object">Immutable object</h3>
</li>
<li>객체 생성 후 객체를 수정할 수 없다.</li>
<li>대표적인 이뮤터블 객체는 <strong>정수, 부동소수점, 문자열, 튜플</strong>이 존재</li>
</ul>
<pre><code class="language-python">a = 4            # a = 4
b = a            # a = 4, b = 4 / b는 a가 아닌 a가 참조한 4를 참조한다.
b += 2            # b = 6 / 기존에 참조한 객체를 수정하지 않고 새 객체인 6을 참조
print(a, b)        # 4 6 출력</code></pre>
<h2 id="앱실론">앱실론</h2>
<ul>
<li>부동소수형 데이터를 이진법으로 표현하기 때문에 발생하는 오차범위</li>
<li>'sys.float_info.epsilon'에 저장된 값을 Machine Epsilon이라 한다.</li>
<li>Machine Epsilon 보다 같거나 작으면 두 실수는 값은 값이라 정의
```python
import sys
#엡실론 출력
print(sys.float_info.epsilon) #2.220446049250313e-16
#부동소수점 수의 오차 검사
a = 0.1 + 0.1 + 0.1
b = 0.3
print(a - b) #0이 아닌 5.551115123125783e-17값인 것을 확인할 수 있다.
if abs(a - b) &lt; 'sys.float_info.epsilon':에 저장된 값을 Machine Epsilon이라 한다.</li>
<li>  print(&quot;a와 b는 같은 값 입니다.&quot;)
else:
  print(&quot;a와 b는 다른 값 입니다.&quot;)</li>
</ul>
<pre><code>
### 📜 참고자료
- https://anggeum.tistory.com/entry/Python-%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90Floating-Point-Machine-Epsilon
- **코딩테스트 합격자 되기 파이썬 편**


</code></pre>