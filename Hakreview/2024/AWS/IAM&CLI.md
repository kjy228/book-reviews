# IAM
identity and access management, global service
사용자는 다수의 그룹에 속할 수 있다. 
<b>permission</b>
- 사용자들과 그룹들은 json 문서를 통해 권한을 부여받을 수 다.
- AWS는 최소권한의 원칙을 사용한다.

<b>IAM 정책 구조</b>
- version : policy language version
- Id : 정책의 identifier
- Statement 
    - Sid : statement의 id
    - effect :  접근을 허용할지 안할지에 (Allow, Deny)
    - Principal : 이 정책을 어느 계정에 적용할지 (아래 스크립트에서는 root)
    - Action : 이 정책을 허용하거나 거부할 action list
    - Resource : 액션을 적용할 자원 목록



```json
{
    "Version" : "2012-10-17",
    "Id" : "S3-Account-Permisisons",
    "Statement" : [
        {
            "Sid": "1",
            "Effect" : "Allow",
            "Principal":{
                "AWS" ["arn:aws:iam::12341234:root"]
            },
            "Action" : [
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource":["arn:aws:s3::mybucket/*"]
        }
    ]
}
```

## IAM MFA (Multi Factor Authentication) (출제)
- virtual MFA device :  
    - google authenticator : 핸드폰에서만 가능
    - Authy : 여러 장치에서 사용가능

- universal 2nd Factor(U2F) security key : 물리장치로 YubiKey가 있다. 여러 루트계정과 IAM 사용자를 하나의 보안키에서 지원한다
- Hardware key Fob MFA Device : gemalto의 물리 장치
- Hardware Key Fob MFA Device for AWS GovGloud(US) : 미국 정부사이트의 인증시 사용 


## AWS CLI

```
aws configure
- access key입력
- secret key 입력

aws iam list-users
```
output
```
{
    "Users": [
        {
            "Path": "/",
            "UserName": "devhak2",
            "UserId": "AIDATTQD6ZNCI3ANUQZU6",
            "Arn": "arn:aws:iam::248042670916:user/devhak2",
            "CreateDate": "2024-05-20T05:22:06+00:00"
        },
        {
            "Path": "/",
            "UserName": "enzo",
            "UserId": "AIDATTQD6ZNCIXBGAZNCM",
            "Arn": "arn:aws:iam::248042670916:user/enzo",
            "CreateDate": "2024-04-11T05:41:35+00:00",
            "PasswordLastUsed": "2024-04-11T06:00:30+00:00"
        },
        {
            "Path": "/",
            "UserName": "itadmin",
            "UserId": "AIDATTQD6ZNCGHM25ZK5I",
            "Arn": "arn:aws:iam::248042670916:user/itadmin",
            "CreateDate": "2023-04-16T13:35:25+00:00",
            "PasswordLastUsed": "2023-04-17T14:30:06+00:00"
        }
    ]
}

```


