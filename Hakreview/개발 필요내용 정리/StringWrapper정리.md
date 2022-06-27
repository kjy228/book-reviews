## String
String a = "aaa"; 
String b = new Stirng("aaa");
 두가지 방법은 엄연히 다른 방법이다
 a는 스트링풀에서 가져다와 쓰는것이고 b는 새로운 스트링 객체를 힙의 메모리영영에 할당하여 저장하는방식이다. 즉, b는 메모리 주소를 참조하게된다. 

### String은 불변이다
한번 선ㅁ언되거나 생성된 문자열은 변경할 수 없다. 
```
String str1 = "java";
String str2 = " is best" ;

str1 = str1.concat(str2);
sout(str1); //java is best
```
concat()메서드를 사용하면 str1이 java is best로 바뀌는것이 아니라 "java is best"라는 새로운 문자열이 힙메모리에 저장되고 str1은 이 메모리 주소를 가리키게된다. 
즉 "java", " is best", "java is best"라는 각각의 메모리가 할당되게 된다.

이를 해결하기위해서 stringbuilder, buffer를 사용하면 된다. 

### StringBuilder vs StringBuffer
```
String str1 = "java";
String str2 = " is best";
StringBuilder sb = new StringBuilder(str1);
sout(System.identifyHashCode(sb)); //12334
sb.append(str2);
sout(System.identifyHashCode(sb)); //12334
str1 = sb.tostring(); 
sout(str1);
```
--------
## Wrapper Class
<img width="485" alt="image" src="https://user-images.githubusercontent.com/43670838/175961930-8fd4b7f7-0f22-427f-ada4-88c302e44620.png">

wrapper class는 객체이기때문에 toString, equals, hashcode 와 같은 메서드가 오버라이딩이 되어있다. 

