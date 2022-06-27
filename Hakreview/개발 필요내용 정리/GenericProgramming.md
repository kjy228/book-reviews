## Generic
변수의 선언이나 메서드의 매개변수를 하나의 참조 자료형이 아닌 여러 자료형을 변환 될 수 있도록 프로그래밍 하는 방식
실제 사용되는 참조 자료형으로의 변환은 컴파일러가 검증하므로 안정적인 프로그래밍 방식
[참조] : https://st-lab.tistory.com/153
### T
여러 참조 자료형으로 대체 될 수 있는 부분을 하나의 문자로 표현
T : type의 약자, 자료형 매개변수
```
public class GenericPrinter<T>{ //제네릭 클래스 
    private T material;

    public void setMaterial(T material){
        this.material = material;
    }

    public T getMaterial(){
        return material;
    }
}
```

### Generic 약자 
<img width="500" alt="image" src="https://user-images.githubusercontent.com/43670838/175971604-16f89922-0f33-4dca-993c-b9432972a812.png">

