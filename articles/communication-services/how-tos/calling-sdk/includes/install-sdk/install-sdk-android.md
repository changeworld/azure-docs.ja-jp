---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 41f8fc2adefd29bb7cfda31e3dbaa83de599b154
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837813"
---
## <a name="install-the-sdk"></a>SDK のインストール

プロジェクト レベルの build.gradle を見つけて、`buildscript` および `allprojects` の下のリポジトリの一覧に `mavenCentral()` を追加します
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
次に、お使いのモジュール レベルの build.gradle で、次の行を dependencies セクションに追加します

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
    ...
}
```

### <a name="initialize-the-required-objects"></a>必要なオブジェクトを初期化する

`CallAgent` インスタンスを作成するには、`CallClient` インスタンスで `createCallAgent` メソッドを呼び出す必要があります。 これにより、`CallAgent` インスタンス オブジェクトが非同期に返されます。
`createCallAgent` メソッドが引数として受け取る `CommunicationUserCredential` には、[アクセス トークン](../../../../quickstarts/access-tokens.md)がカプセル化されています。
`DeviceManager` にアクセスするには、最初に callAgent インスタンスを作成してから、`CallClient.getDeviceManager` メソッドを使用して DeviceManager を取得する必要があります。

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```
呼び出し元の表示名を設定するには、この代替メソッドを使用します。

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
```