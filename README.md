# Modern C++
 
## C++ 11  

### C++ lvalue, rvalue
value(l-value, r-value)로 나뉨.  
c랑 c++이 lvalue rvalue 정의가 다름  
rvalue : 문장이 끝나면 사라지는 값  

    int main(){  
	    int a = 10;
	    int& ra = a; //lvalue reference  
	
	    std::string hello = f("hello", "world");  
	    std::cout<<hello<<"\n";  //복사가 세번일어남  
    }    
.  
기존 cpp에서는 rvalue를 reference로 넘길 수가 없음  
**rvalue reference는 lvalue다** (문장이 끝나도 안 사라짐)  

### Move Simentics & std::move 
실제 문자열을 복사해오는게 아니라 그 문자열을 가르키는 포인터를 가로챈다.(문자열을 가르치는 포인터? rvalue! )  
rvalue reference는 lvalue이므로 다시 rvalue로 바꿔줘야함.  
이건 `std::move()`가함. => lvalue든 rvalue든 rvalue로 바꿔줌  
`std::move` 막쓰면 안됨.  
RVO(return value optimization) std::move로 리턴하면 RVO나 NRVO막음

### variadic template(가변템플릿)
    void func(T arg1);  
    void func(T1 arg1, T2 arg2);  
    void func(T1 arg1, T2 arg2, T3 arg3);  


가변템플릿은 재귀적으로 동작(그러나 컴파일 타임에 재귀가 풀림)  
그러므로 탈출조건이 필요함 (인자가 하나 남았을때 쓸 함수가 필요함)

### foward Reference
T&& 는 rvalue reference 와 forward reference가 되기도 함
auto랑 같이 쓸때 template 쓸때 forward reference가 됨  
auto 는 js의 var느낌.  
`for(auto iter = a.begin(); iter != a.end(); iter++)` 이것도 귀찮아서  
`for(const auto& a : a)`로 함   
( const가 없으면 복사가 일어나서 const붙이는거 추천)  


forward reference 는 rvalue ,lavalue다 받을수 있다.  
`std::forward<arg?>(arg)` 는 lvalue는 lvalue로 넘겨주고 rvalue는 rvalue로 넘겨줌.  
rvalue 는 `std::move` universal reference는 `std::forward` 쓰면됨.  

### default
(생성자) = default는 얕은복사

### nullptr
nullptr은 type이 이 아님. 클래스.  

### initallize list
vector에 10, 20, 30 을 넣고 싶으면 과거엔 push_back을 써야했다.  
요즘은 `std::vector<int a ?> = {10, 20, 30}` 이렇게 함.  
클래스에서 생성자에 `std::initalizer_list<int?> a` 이런식으로 할 수 있음.

### static_assert
static_assert 는 컴파일 타임에 
assert 는 런타임에

### decltype
    ex1)
    int a = 10, b = 20;
    decltype(a + b) c = a + b;

c의 타입은 a+b의 결과 타입으로 해줌.

    ex2)
    template<typename T1, typename T2>
    auto add(T1 a, T2 b) -> decltype(a + b) {
	return a + b;
    }

이 경우 결과를 출력해 보면  

    int main() {
	    std::cout << add(10, 3.0f) << "\n";
	    std::cout << add(3.0f, 10) << "\n";
    }


### enum class
enum : unscoped enum 현상
enum class : 꼭 enumclass의 이름을 통해서 접근해야한다.  

### constexpr
변수의 값을 컴파일 타임에 결정하여 상수화 하겠다.  
cf) constval (c++20)  

### final specifier
더이상 상속을 못하게 한다.
b에서 final 붙은 함수를 만들면 c에선 오버라이딩 할 수 없다.

### Smart pointer  
해제가 알아서된다.  
RAII (Resoruce Acquisition Is Initalization)

* **unique_ptr**  
주인이 하나의 포인터만 가질 수 있다.  

* **shared_ptr**  
reference count가 있어서 0이 되면 자동으로 해제된다.

* **weak_ptr**  
읽기전용. reference count하지 않는다.

* **atomic_unique_ptr**
* **atomic_shared_ptr(c++20)**  


### array
    std::array<type, 개수>

### tuple
pair 는 2개만 되지만 tuple은 인자개수 제한없다.  
(내부적으로 가변인자 템프릿)  

### lambda
    [my_mod](int v)->int{return v % my_mod;}  
    [개시자/] (인자/) -> 반환타입 {함수 몸통}  
개시자 : 인자를 어떻게 들고 올 것인가.

## C++14
100'000'000

## C++17
### optional
유효하지않은 값을 나타내기 위해서 사용.(return null_opt)  

### folding expression
가변템플릿을 이용해 표현식을 간단하게 구현

### structured bindings
다중반환을 사용할 수 있다.(람다이용)  

    using Coordinate = std::pair<int, int>;    
    Coordinate origin(){   
    return Coordinate{0, 0};  
    }  



    const auto[x, y] = origin();  
    x;  
    y;  '

### constexpr if
static if c++ 버전  
특정 블록만 컴파일 할 수 있음  

    constexpr bool isinteger(){  
        if constexpr(std::is_integral<T?>::value)  
            return true;  
        else  
            return false;  
    }  
value가 true면 true만 남고 false면 false만 남는다.  

### std::any
모든 종류의 정보를 저장 (단, 복사생성자를 지원해야 함)  
`any_cast()`로 원하는 타입으로 반환된 결과를 얻을 수 있음  

### std::variant
type safe한 union을 나타냄  
`boost::any`와는 다르게 지정된 형태로만 될수 있고  
타입 변환시 동적할당하지 않음.  

### std::map::insert_or_assign
    std::map<char, int>mp{{'B', 1256}};  
    mp.insert_or_assign('A', 890);  
mp에 A가 없으면 새로 추가해서 890을 넣어주고 있으면 그냥 890을 넣어줌.

##### cf) vector
`v.resize()` size를 바꿈  
`v.shrink_to_fit()` capacity를 size크기 만큼으로 줄임

### File system
