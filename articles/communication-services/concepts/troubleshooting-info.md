---
title: Azure Communication Services でのトラブルシューティング
description: Communication Services ソリューションのトラブルシューティングに必要な情報を収集する方法について説明します。
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ff3e7fee87661fb89ba930b7368bd54e71ad57bf
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2020
ms.locfileid: "93357625"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Azure Communication Services でのトラブルシューティング

このドキュメントでは、Communication Services ソリューションで発生する可能性がある問題のトラブルシューティングについて説明します。 SMS のトラブルシューティングを行う場合は、[Event Grid で配信レポートを有効にする](../quickstarts/telephony-sms/handle-sms-events.md)ことで、SMS の配信の詳細を取得できます。

## <a name="getting-help"></a>ヘルプの表示

開発者の皆さんは、ご質問の送信、機能のご提案、および Communication Services [GitHub リポジトリ](https://github.com/Azure/communication)の問題のレポートに関してぜひご協力ください。 他に次のフォーラムもあります。

* [Microsoft Q&A](https://docs.microsoft.com/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

Azure サブスクリプションの[サポート プラン](https://azure.microsoft.com/support/plans/)によっては、[Azure portal](https://azure.microsoft.com/support/create-ticket/) を通じてサポート チケットを直接送信することもできます。

特定の種類の問題のトラブルシューティングを支援するために、次の情報をおたずねする場合があります。

* **MS-CV ID**: この ID は、通話とメッセージのトラブルシューティングに使用されます。 
* **通話 ID**: この ID は、Communication Services の通話を識別するために使用されます。
* **SMS メッセージ ID**: この ID は、SMS メッセージを識別するために使用されます。
* **呼び出しログ**: これらのログには、呼び出しとネットワークの問題のトラブルシューティングに使用できる詳細情報が含まれています。


## <a name="access-your-ms-cv-id"></a>MS-CV ID にアクセスする

MS-CV ID にアクセスするには、クライアント ライブラリを初期化する際に、`clientOptions` オブジェクト インスタンスで診断を構成します。 診断は、チャット、管理、VoIP 通話など、任意の Azure クライアント ライブラリに対して構成できます。

### <a name="client-options-example"></a>クライアント オプションの例

次のコード スニペットは、診断の構成を示しています。 診断を有効にした状態でクライアント ライブラリを使用すると、構成済みのイベント リスナーに診断の詳細が出力されます。

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>通話 ID にアクセスする

Azure portal を通じて通話の問題に関連したサポート リクエストを提出する場合は、参照している通話の ID を提供するよう求められることがあります。 これには、通話クライアント ライブラリを介してアクセスできます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>SMS メッセージ ID にアクセスする

SMS の問題については、応答オブジェクトからメッセージ ID を収集できます。

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="enable-and-access-call-logs"></a>呼び出しログを有効にしてアクセスする




# <a name="javascript"></a>[JavaScript](#tab/javascript)

次のコードを使用すると、JavaScript クライアント ライブラリを使用してコンソールにログを出力するように、`AzureLogger` を構成することができます。

```javascript
import { AzureLogger } from '@azure/logger'; 

AzureLogger.verbose = (...args) => { console.info(...args); } 
AzureLogger.info = (...args) => { console.info(...args); } 
AzureLogger.warning = (...args) => { console.info(...args); } 
AzureLogger.error = (...args) => { console.info(...args); } 

callClient = new CallClient({logger: AzureLogger}); 
```

# <a name="ios"></a>[iOS](#tab/ios)

iOS 用に開発する場合、ログは `.blog` ファイルに格納されます。 ログは暗号化されているため、直接表示できないことに注意してください。

これらにアクセスするには、Xcode を開きます。 [Windows]\(ウィンドウ\) > [Devices and Simulators]\(デバイスとシミュレーター\) > [Devices]\(デバイス\) に移動します。 デバイスを選択します。 [Installed Apps]\(インストールされているアプリ\) でアプリケーションを選択し、[Download container]\(コンテナーのダウンロード\) をクリックします。 

これにより、`xcappdata` ファイルを取得できます。 このファイルを右クリックし、[Show package contents]\(パッケージの内容の表示\) を選択します。 その後、`.blog` ファイルを表示し、それを Azure サポート リクエストに添付できます。

# <a name="android"></a>[Android](#tab/android)

Android 用に開発する場合、ログは `.blog` ファイルに格納されます。 ログは暗号化されているため、直接表示できないことに注意してください。

Android Studio で、シミュレーターとデバイスの両方から [View]\(表示\) > [Tool Windows]\(ツール ウィンドウ\) > [Device File Explorer]\(デバイス ファイル エクスプローラー\) を選択して、デバイス ファイル エクスプローラーに移動します。 `.blog` ファイルはアプリケーションのディレクトリ内にあり、`/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog` のように表示されるはずです。 このファイルをサポート リクエストに添付できます。 
   

---


## <a name="related-information"></a>関連情報
- [ログと診断](logging-and-diagnostics.md)
- [メトリック](metrics.md)
