---
layout: post
title:  "JString Design[3]"
date:   2019-04-27 09:00:00
categories: LINK(OICW)
---

# [_jcode mini-project] JString Design(3) (작성중)


> 필자는 공군 작전정보통신단 체계개발실에서 복무('17~'19)하였습니다. 이 포스트는 작전정보통신단 병사 **프로그래밍 동아리(LINK)** 에서의 활동을 바탕으로 작성한 내용입니다.

## JString(3) 인터페이스를 만들자

이제 본격적으로 JString 클래스의 동작을 정의해 줄 때입니다. 우선 at() 부터 만들기로 합시다.

## ① at()

함수를 만들어 봅시다. 레퍼런스를 보면, 

```
(이미지)
```

> "accesses the specified character with bounds checking" 

이라고 쓰여 있네요. 특정 문자를 범위를 검사하면서 리턴하는 함수입니다. 어차피 Buffer는 C 스타일 배열이니 단순히 리턴만 해 주면 되겠군요. 구현은 쉽습니다. 

```cpp
// interfaces
const char _jcode::JString::at(const int& argIdx) const noexcept {

	if (preventAccess())
		return -0x01;

	if (Length - 1 >= argIdx && argIdx >= 0) {
		
		return Buffer[argIdx];

	} else
		return -0x01; // Regarded as an index error.
};
```

접근이 가능한지, 즉 동적할당이 되어 있는 상태인지 검사하는 함수는 preventAccess()입니다. 이 또한 간단하게 아래와 같이 구현해 두면 여러 군데에서 써먹을 수 있습니다. 

```cpp
// inner
const bool _jcode::JStringItor::preventAccess() const noexcept {

	// nullptr of LocationAddr means that
		// this instance does not know the existance of the JString instance.
	return LocationAddr == nullptr ? true : false;
};
```

테스트를 해 봅시다. 아래는 제가 좋아하는 노래인 Rascal Flatts의 'Life is a highway'라는 곡의 가사 일부입니다. 이 JString 문자열들은 아래에서의 테스트에서 계속 사용하겠습니다. 

```cpp
namespace jc = _jcode;

// This is my favorite song.
jc::JString life_is_a_highway_lyrics_1("#1: Life's like a road that you travel on,");
jc::JString life_is_a_highway_lyrics_2("#2 : When there's one day here, and the next day gone.");
jc::JString life_is_a_highway_lyrics_3("#3: We won't hesitate, break down the garden gate");
jc::JString life_is_a_highway_lyrics_4("#4: there's not much time left today~");
// These guys calls their constructors, JString(const char*).
```

아래 코드를 돌려보면, 

```cpp
// interface testing...
	// at
	
PRINT_NORMAL_MSG(life_is_a_highway_lyrics_2.at(1));
PRINT_NORMAL_MSG(life_is_a_highway_lyrics_2.at(2);
PRINT_NORMAL_MSG(life_is_a_highway_lyrics_2.at(3);
```

짜자자잔, 정상 작동 확인 완료했습니다. 

## ② operator[] ()

  STL의 vector에서는 멤버에 접근할 때 at() 뿐만 아니라 vector_thing[45]처럼 사용하기도 합니다. JString도 이렇게 사용하고 싶네요. 예를 들어, 

```cpp
JString ExampleStr("Hello world!!");
std::cout << ExampleStr[3]; // ‘l’ 이 프린트 되겠네요
```

이렇게 말이죠. 그러면 아까와 같이 연산자 오버로딩을 해 봅시다. 

```cpp
const char _jcode::JString::operator[](const int& argIdx) const noexcept {

	return this->at(argIdx);
}
```

보통 [] 연산자 안에는 int형 정수가 들어갑니다. 디자인하기 나름이지만 만일 something["this?"]와 같이 map의 key처럼 작동하는 연산자를 만들고 싶다면 인자가 std::string이나 const char*가 들어가도 되겠지요. JString은 인덱스로 받을 것이기 때문에 const int형을 인자로 받습니다.

여기서 참고할 점은, 내부에 아주 잘 만들어진 인터페이스나 동작이 이미 있다면 갖다 쓰면 됩니다. 다른 클래스를 디자인할 때도 마찬가지이지만 동일한 동작인데 같은 코드를 적어 넣을 필요가 없습니다. operator[]의 경우 at()과 같은 동작을 하므로 단순히 at(argIdx)를 리턴시키면 됩니다. 

추가적으로, C++ 개발자는 const 키워드를 사랑해야 합니다. const는 남발하면 남발할수록 더 안전한 코드를 작성하는 틀을 만들어주기 때문이죠. const를 더 썼다고 하여 작동 방식에 크게 영항을 주지 않으며 오히려 컴파일 타임에 개발자가 실수한 부분을 잡아주기도 합니다.

위의 operator[] 함수를 예로 들어 디자인 방향을 봅시다. 우선 (1) operator[] 함수는 내부 멤버를 수정하지 않습니다. 따라서 함수 정의에 const를 붙여 멤버 값 수정을 막아야 합니다. (2) operator[] 함수에 쓰이는 인자는 절대 수정될 일이 없습니다. 어떤 인덱스 값을 읽겠다고 했는데 함수 내부에서 파라미터가 수정되어 버리면 다른 인덱스를 읽어 버리겠죠. 마지막으로 (3) operator[] 함수의 반환값은 어디까지나 그냥 값입니다. 그냥 값이라 함은 수정될 일도 없을 뿐더러 호출부에서 필요가 없다면 임시값으로 처리하여 버려버릴겁니다. operator[] 함수 입장에서는 어차피 넘겨준 값을 어떻게 쓰든 알 바 아니겠죠. 따라서 리턴값도 const로 처리하면 좋습니다. 

테스트 해 볼까요? 잘 되는군요!!

```
(이미지)
```

## ③ front(), back()

간단합니다. 가장 앞의 문자를 반환하고 가장 뒤에 있는 문자를 반환하는 함수입니다. 

```cpp
const char _jcode::JString::front() const noexcept {

	if (preventAccess())
		return -0x01;
	
	else
		return Buffer[0];
};


const char _jcode::JString::back() const noexcept {

	if (preventAccess())
		return -0x01;

	else
		return Buffer[Length - 1];
};

```

vector 내부의 함수라면 설정한 템플릿 인자의 레퍼런스를 리턴 하지만 문자열은 꼴랑 1 byte 밖에 안되는 문자 하나를 반환하기 때문에 레퍼런스 까지는 필요 없습니다. 따라서 일반 값인 const char를 넘겨주는 것으로 합니다. 

```cpp
PRINT_NORMAL_MSG(life_is_a_highway_lyrics_1.front());
PRINT_NORMAL_MSG(life_is_a_highway_lyrics_1.back());
```

테스트 해 봅시다. 잘 나옵니다. 참고로 맨 앞 글자는 공백입니다.

## ③ data(), c_str()

data() 함수를 봅시다. std::string 레퍼런스를 보면 "returns a pointer to ther first character of a string"이라고 쓰여 있군요. 가장 첫 글자의 포인터를 돌려준다는 이야기이네요. c_str()도 비슷합니다. const char* 형태를 리턴하지만 C 스타일의 배열 형태로 돌려준다고 쓰여 있습니다. 

```
(이미지)
```

중요한 이야기는 아니지만 알고 넘어가도록 하죠. C++11 이전에는 data()가 null 문자를 자동으로 추가하지 않았습니다. C++11부터 data()와 c_str()은 사실상 같은 멤버 함수가 되었는데, 그렇다고 둘 중 하나가 폐기되지는 않았습니다. data()의 현재 서명은 const 문자_형식_포인터 data() const(상수 C 문자열을 돌려주는 상수 멤버 함수)인테, C++17에서는 또 다른 버전인 문자_형식_포인터 data()(수정 가능한 C 문자열을 돌려주는 비상수 멤버 함수)가 추가되었습니다. 

*(여기서 문자_형식 이라고 함은 문자에 char 형만 존재하는 것이 아닌 char16_t, char32_t 등 std::string, std::wstring과 같은 유니코드를 지원하는 클래스가 존재하기 때문에 표기한 방법입니다.)*

뭐가 어찌 됐든 data()나 c_str()나 안에 있는 내용물을 뱉어내는 함수라는 거지요. 따라서 어렵게 생각할 것 없이 그냥 C++11 이후처럼 동일한 동작을 하게끔 만들어 봅시다. 그러면 아래와 같이 만들어 주면 되겠지요. 

```cpp
const char* _jcode::JString::data() const noexcept {
	PRINT_FUNCTION_CALL("JString::data()");

	return Buffer == nullptr ? "" : Buffer;
};


const char* _jcode::JString::c_str() const noexcept {
	PRINT_FUNCTION_CALL("JString::c_str()");

	return this->data();
};
```

먼저 data()를 정성들여 만들어 줍니다. 동적 할당 되어있지 않다면 뱉어낼 문자열이 없지요. 그렇다고 nullptr로 초기화 되어 있는 것을 던져주기는 좀 그러니 그냥 빈 문자열을 리턴하도록 했습니다. c_str()은 data()와 동작이 완전히 동일하니 정성들여 만들어 둔 data()를 그냥 호출해 주면 되겠죠. 테스트 하기에는 너무 싱거운 코드지만 한번 해 봅시다. 

```cpp
PRINT_NORMAL_MSG(life_is_a_highway_lyrics_1.data());

_jcode::JString Blank;
PRINT_NORMAL_MSG(Blank.c_str());
```
```
(이미지)
```

잘 동작합니다!!

## ④ empty()

레퍼런스를 봅시다. std::string의 empty() 함수는 문자열을 가지고 있는지 확인하는 함수네요. 그렇지만 JString에서는 조금 동작을 달리 하고 싶습니다. JString에서의 empty() 함수는 말 그대로 문자열을 비우는 동작을 하도록 고안하겠습니다. 단, 메모리 크기는 동일하게 유지하는 상태입니다. 개인적으로 is_empty()라는 이름이었다면 동일하게 동작을 구현했겠지만 empty()와 clear() 요 두 놈이 굉장히 헷갈립니다. 마음에 안 드네요. 따라서 여기서는 말 그대로 empty 하도록 구현하겠습니다. 

처음에는 0으로 값을 채울까도 생각했으나 0도 어쨌든 값입니다. 따라서 쓰레기 값 일지언정 동일한 크기로 다시 메모리를 할당 받도록 하죠, 구현은 간단하니 아래와 같이 작성하면 될 듯 합니다. 

```cpp
const bool _jcode::JString::empty() noexcept {
	PRINT_FUNCTION_CALL("JString::empty()");

	if(preventAccess()) return false;
	
	free(Buffer);
	Buffer = (char*)malloc(sizeof(char) * Length);
	
	return true;
}
```

테스트가 기대되는 군요. 어떤 쓰레기 값이 들어가려나...

```cpp
life_is_a_highway_lyrics_3.empty();
PRINT_NORMAL_MSG(life_is_a_highway_lyrics_3);
```
```
(이미지)
```

袴袴袴袴袴袴袴袴袴袴袴袴袴袴袴袴袴袴袴袴袴袴袴袴袴羲羲硼??N 찾아보면, 

```
(이미지)
```

“사타구니사타구니사타구니사타구니사타구니사타구니사타구니사타구니사타구니사타구니사타구니사타구니사타구니숨결소리커”

그렇다네요.

## ⑤ sizelength() 

직관적인 이름입니다. 레퍼런스를 볼 것도 없이 문자열의 크기를 리턴하는 메서드네요. 애초에 멤버로 길이 정보를 가지고 있는 변수가 있었으니, 바로 들어갑시다. 

```cpp
const int _jcode::JString::sizelength() const noexcept {

	return this->Length;
}
```

테스트 할 것도 없어 보입니다. 






