# 関連図

```mermaid
flowchart LR
  A<-->B
  B-->C
  C-->D
```

```plantuml
@startuml
collections 保険会社
agent 保険代理店 as doit
actor 募集人
collections マーケット
actor 顧客
agent itz

保険会社<-do-顧客: 保険料支払
募集人<-do-マーケット: 案件買取
募集人-ri->顧客: 営業
doit-ri->募集人: 手数料
doit<-ri-募集人: 調整額・控除額（案件代等）
doit->マーケット: 案件代を一括支払
doit<-up-保険会社: 手数料
マーケット<-up-顧客: 保険相談
doit<-do-itz: システム提供
@enduml
```

# シーケンス図

```plantuml
@startuml
actor ユーザ
participant ファンクラブサイト as fan
participant 会員サービス as kai

ユーザ->fan: ログイン
fan<->kai: ガチャ権利回数取得API
kai<-fan: ログイン後Top画面
note right: ガチャ権利回数を表示（0回でも表示する） 
ユーザ->fan: ガチャを回すボタンClick
ユーザ<-fan: ガチャスタート画面表示
ユーザ->fan: 画面タップ
fan<->kai: ユーザのカード情報取得API
fan<->kai: ユーザのカード情報取得API
fan<->kai: ガチャ権利回数取得API
note right: 2端末操作等で、この時点でガチャ権利が無ければ、\nTOP画面に戻りエラーメッセージを表示？
fan->fan: ガチャ抽選処理
note right: カードの取得枚数制限超えていれば再度抽選する
fan->kai: ユーザのカード情報登録API
kai->kai: ガチャ権利回数、カードの取得枚数のチェック\nガチャ権利回数の減算\nカードの取得情報の更新
fan<-kai: ユーザのカード情報登録APIの成功可否
note right: カードの取得枚数制限超えているエラーなら再度抽選する\nガチャ権利回数を超えているエラーならTOP画面に戻りエラーメッセージを表示？
ユーザ<-fan: ガチャ演出画面、ガチャ結果画面
note right: ガチャ権利回数があれば【もう一度ガチャをひく】ボタン表示\n【もう一度ガチャをひく】ボタンをClickしたら、ガチャスタート画面表示をして上記と同様の処理をする
@enduml
```

# 構成図

AWSPUMLを使用してAWS構成図
https://github.com/awslabs/aws-icons-for-plantuml

```plantuml
@startuml
!define AWSPUML https://raw.githubusercontent.com/milo-minderbinder/AWS-PlantUML/release/18-2-22/dist

!includeurl AWSPUML/common.puml
!includeurl AWSPUML/ApplicationServices/AmazonAPIGateway/AmazonAPIGateway.puml
!includeurl AWSPUML/Compute/AWSLambda/AWSLambda.puml
!includeurl AWSPUML/Compute/AWSLambda/LambdaFunction/LambdaFunction.puml
!includeurl AWSPUML/Database/AmazonDynamoDB/AmazonDynamoDB.puml
!includeurl AWSPUML/Database/AmazonDynamoDB/table/table.puml
!includeurl AWSPUML/General/AWScloud/AWScloud.puml
!includeurl AWSPUML/General/client/client.puml
!includeurl AWSPUML/General/user/user.puml
!includeurl AWSPUML/SDKs/JavaScript/JavaScript.puml
!includeurl AWSPUML/Storage/AmazonS3/AmazonS3.puml
!includeurl AWSPUML/Storage/AmazonS3/bucket/bucket.puml

skinparam componentArrowColor Black
skinparam componentBackgroundColor White
skinparam nodeBackgroundColor White
skinparam agentBackgroundColor White
skinparam artifactBackgroundColor White

USER(user)
CLIENT(browser)
JAVASCRIPT(js,SDK)

AWSCLOUD(aws) {

  AMAZONS3(s3) {
    BUCKET(site,www.insecurity.co)
    BUCKET(logs,logs.insecurity.co)
  }

  AMAZONAPIGATEWAY(api)

  AWSLAMBDA(lambda) {
    LAMBDAFUNCTION(addComments,addComments)
  }

  AMAZONDYNAMODB(dynamo) {
    TABLE(comments,Comments)
  }
}

user - browser

browser -d-> site :**1a**) get\nstatic\ncontent
site ~> logs :1a
site .u.> browser :**1b**
browser - js
js -r-> comments :**2a**) get\ncomments
comments ..> js :**2b**

js -r-> api :**3**) add\ncomment

api -d-> addComments :**4**

addComments -> comments :**5**

comments ..> js :**6**) new\ncomments
@enduml
```