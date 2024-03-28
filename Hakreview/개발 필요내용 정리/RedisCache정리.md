# Spring Data Redis

[참고] : https://github.com/spring-projects/spring-data-examples

## Jedis VS Lettuce
둘다 Redis client 라이브러리이다.

<b>Jedis</b>
- 동기식 클라이언트 : 요청이 완료될때까지 현재 스레드가 대기
- 간단한 api : jedis는 사용하기 쉬운 api
- 멀티쓰레드 환경에서 사용 : Jedis 인스턴스는 스레드 안전하지 않습니다. 멀티 스레드 환경에서 사용하기 위해서는 Jedis 연결 풀을 사용해야 하며, 각 스레드마다 별도의 Jedis 인스턴스를 사용해야 합니다.
<b>Lettuce</b>
- 비동기식 및 동기식 클라이언트: Lettuce는 Netty를 기반으로 한 비동기 및 이벤트 주도 통신을 지원합니다. 또한, 동기식으로도 사용할 수 있는 API를 제공합니다.
- 스레드 안전성: Lettuce 클라이언트는 스레드 안전하며, 여러 스레드에서 동일한 인스턴스를 공유하여 사용할 수 있습니다. 이는 내부적으로 비동기 방식을 사용하기 때문에 가능합니다.
- 클러스터, 마스터-슬레이브, 센티넬 지원: Lettuce는 Redis의 다양한 배포 설정을 지원합니다. 자동 재시도, 마스터 슬레이브 스위치오버 등 고급 기능을 내장하고 있습니다.

우리의 application 에서는 어떤 라이브러리가 적합할까?
- 성능: 비동기 방식을 지원하는 Lettuce가 더 나은 성능을 제공할 수 있습니다. 특히 I/O 작업이 많은 환경에서 더 효율적입니다.
- 사용 용이성: Jedis는 API가 더 단순하고 직관적일 수 있어, 작은 규모의 프로젝트나 간단한 사용 사례에 적합할 수 있습니다.
- 멀티 스레딩: 멀티 스레드 환경에서는 Lettuce가 스레드 안전성 때문에 더 적합할 수 있습니다.

## serializers
Redis는 오직 byte로 저장관리 합니다. serializer는 두가지 타입이 있는데 `RedisSerializer`, `RdisElementReader, RedisElementWriter` 가 있다.
두가지의 가장큰 차이점은 RedisSerializer -> byte[] , ElementReader, ElementWriter -> Bytebuffer로 변환하는 것이다.
Multiple implementations are available (including two that have been already mentioned in this documentation):

JdkSerializationRedisSerializer, which is used by default for RedisCache and RedisTemplate.

the StringRedisSerializer.

However, one can use OxmSerializer for Object/XML mapping through Spring OXM support or Jackson2JsonRedisSerializer or GenericJackson2JsonRedisSerializer for storing data in JSON format.

Do note that the storage format is not limited only to values. It can be used for keys, values, or hashes without any restrictions.

