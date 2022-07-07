# 스트림에서는 부작용 없는 함수를 사용하라
```
Map<String long> freq;
try(Stream<String> words = new Scanner(file).tokens()){
    freq = words.collect(groupingBy(String::toLowerCase, counting()));
}
```
try() 안에 뭐지????

forEach 연산은 스트림 계산 결과를 보고할 때만 사용하자 ( 계산할때 사용 x!!)

스트림 수집기 : toList(), toSet(), toCollection(collectionFactory)

toMap 전환시 key 충돌이 나면 illegalstateException을 던지는데 이를해결하기 위한 방법  mergeFunction 사용 하기 
참고 : https://www.baeldung.com/java-collectors-tomap
