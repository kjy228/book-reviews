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