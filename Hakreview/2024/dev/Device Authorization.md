# Device Authoriatino flow


참고 문서 : RFC 8628: OAuth 2.0 Device Authorization Grant 

 
device authorization은 디바이스로 사용자 정보를 입력하지 못할때, 이를 인증하기 위한 절차이다.

따라서 email, password를 디바이스를 통해서 입력하는 절차가 없음

 

<b>User Agent</b> : 사용자가 login한 web or app

<b>Device</b> : Glass와 같은 user 정보(email, password)를 입력하지 못하는 device

<b>Authorization Server</b> : OAuth2.0 서버

![image](https://github.com/user-attachments/assets/6d109fc9-3e0c-4070-93ce-998b4d119d26) 

 

## device_authorization 응답
```json
{
    "user_code": "XZGX-WHKK",
    "device_code": "XjcPAPR5054FdhL7jHP19eS2SIdX5_yHhpj1jH8Hbz3sGZe0S_90RvwNqn2giDazYhYMD_iLWHm67uSCv7kxQnW_kzPKDV5YWE_vWdo0QdeGh8iPMsCLDeVMYuo4T139",
    "verification_uri_complete": "http://localhost:8080/device-verification?user_code=XZGX-WHKK",
    "verification_uri": "http://localhost:8080/device-verification",
    "expires_in": 300
}
```
위의 verification_uri로 접속하여 userCode 입력(그동안 device는 `/token` endpoint polling)

 
Flow 참고할만한 예제 : 블라인드 어플 web login

