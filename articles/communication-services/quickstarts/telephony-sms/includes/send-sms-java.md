---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: c11c2098d30ed6f00d94124fd77c2ebdb6cd2c7a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91303322"
---
Communication Services Java SMS クライアント ライブラリを使用して SMS メッセージを送信することによって、Azure Communication Services の使用を開始します。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) バージョン 8 以降。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- SMS が有効になっている電話番号。 [電話番号を取得します](../get-phone-number.md)。

### <a name="prerequisite-check"></a>前提条件のチェック

- ターミナルまたはコマンド ウィンドウで `mvn -v` を実行して、maven がインストールされていることを確認します。
- Communication Services リソースに関連付けられている電話番号を表示するには、[Azure portal](https://portal.azure.com/) にサインインし、Communication Services リソースを見つけて、左側のナビゲーション ペインから **[電話番号]** タブを開きます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-java-application"></a>新しい Java アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、Java アプリケーションを作成するディレクトリに移動します。 次のコマンドを実行して、maven-archetype-quickstart テンプレートから Java プロジェクトを生成します。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

"generate" 目標により、artifactId と同じ名前のディレクトリが作成されます。 このディレクトリの下の **src/main/java** ディレクトリにはプロジェクトのソース コードが、**src/test/java** ディレクトリにはテスト ソースがそれぞれ含まれており、**pom.xml** ファイルはプロジェクトのプロジェクト オブジェクト モデル (POM) です。

### <a name="install-the-package"></a>パッケージをインストールする

テキスト エディターで **pom.xml** ファイルを開きます。 依存関係のグループに、次の dependency 要素を追加します。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

`azure-core-http-netty` 依存関係を **pom.xml** ファイルに追加します。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

テキスト エディターで **/src/main/java/com/communication/quickstart/App.java** を開き、import ディレクティブを追加して、`System.out.println("Hello world!");` ステートメントを削除します。

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.CommunicationClientCredential;
import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>オブジェクト モデル

Java 用 Azure Communication Services SMS クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                                             | 説明                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | このクラスによって、SmsClient が作成されます。 これには、エンドポイント、資格情報、および http クライアントを指定します。 |
| SmsClient                    | このクラスは、すべての SMS 機能に必要となります。 これを使用して、SMS メッセージを送信します。                |
| SendSmsResponse               | このクラスには、SMS サービスからの応答が含まれます。                                          |
| CommunicationClientCredential | このクラスによって署名要求が処理されます。                                                            |
| PhoneNumber                   | このクラスは電話番号情報を保持します

## <a name="authenticate-the-client"></a>クライアントを認証する

接続文字列を使用して `SmsClient` をインスタンス化します。 次のコードでは、`COMMUNICATION_SERVICES_ENDPOINT_STRING` および `COMMUNICATION_SERVICES_CREDENTIAL_STRING` という名前の環境変数からリソースのエンドポイントと資格情報文字列を取得します。資格情報は Azure portal の `Key` です。 [リソースの接続文字列を管理する](../../create-communication-resource.md#store-your-connection-string)方法を参照してください。

`main` メソッドに次のコードを追加します。

```java
// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationClientCredential credential = new CommunicationClientCredential(accessKey);

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .httpClient(httpClient)
    .buildClient();
```

`com.azure.core.http.HttpClient` インターフェイスを実装する任意のカスタム HTTP クライアントを使用して、クライアントを初期化できます。 上記のコードは、`azure-core` によって提供される [Azure Core Netty HTTP クライアント](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true)の使用方法を示しています。

## <a name="send-an-sms-message"></a>SMS メッセージの送信

sendMessage メソッドを呼び出して、SMS メッセージを送信します。 `main` メソッドの末尾に次のコードを追加します。

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

`<leased-phone-number>` は Communication Services リソースに関連付けられている、SMS が有効になっている電話番号で置き換え、`<to-phone-number>` はメッセージの送信先の電話番号で置き換える必要があります。 すべての電話番号パラメーターは、[E.164 規格](../../../concepts/telephony-sms/plan-solution.md#optional-reading-international-public-telecommunication-numbering-plan-e164)に準拠している必要があります。

`enableDeliveryReport` パラメーターは、配信レポートを構成するために使用できる省略可能なパラメーターです。 これは、SMS メッセージが配信されたときにイベントを生成する場合に便利です。 SMS メッセージの配信レポートを構成するには、[SMS イベントの処理](../handle-sms-events.md)に関するクイックスタートを参照してください。

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

## <a name="run-the-code"></a>コードの実行

**pom.xml** ファイルが格納されているディレクトリに移動し、`mvn` コマンドを使用してプロジェクトをコンパイルします。

```console

mvn compile

```

次に、パッケージをビルドします。

```console

mvn package

```

次の `mvn` コマンドを実行して、アプリを実行します。

```console

mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false

```
