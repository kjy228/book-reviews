# RabbitMQ란? 
두개의 Application이 메세지 큐를 사용하여 통신할 수 있도록한다. 한쪽 서버가 연결이 되어있지않아도 통신할 수 있으며 producer, consumer의 역할을 각각 하게된다. 

## Exchange N Queue in RabbitMQ
exchange은 router의 역할을 한다. 
X application에서 exchange(router)를 통해서 consumer 역할을하는 Y application과 통신 할 수 있다. 이때 x 와 y는 broker인 exchange를 통해서 통신하므로 서로에 대한 정보를 알 수 없다.