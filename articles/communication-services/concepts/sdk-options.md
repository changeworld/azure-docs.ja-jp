---
title: Azure Communication Services の SDK と REST API
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services の SDK と REST API について詳しく説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: d71720b446baa8e2e5e15c407a69d9ff884004b2
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307692"
---
# <a name="sdks-and-rest-apis"></a>SDK と REST API

Azure Communication Services の機能は、概念的に 6 つの領域に分けられています。 ほとんどの領域には、インターネット経由で直接使用できる、公開された REST API に対してプログラミングされた、完全にオープンソースである SDK があります。 Calling SDK は独自のネットワーク インターフェイスを使用しており、現在はクローズ ドソースです。 SDK のサンプルとその他の技術的な詳細は、[Azure Communication Services の GitHub リポジトリ](https://github.com/Azure/communication)で公開されています。

## <a name="rest-apis"></a>REST API
Communication Services API は、他の Azure REST API と共に [docs.microsoft.com](/rest/api/azure/) に記載されています。 このドキュメントでは、HTTP メッセージを構成する方法と、Postman を使用するためのガイダンスが提供されます。 このドキュメントは [GitHub](https://github.com/Azure/azure-rest-api-specs) の Swagger 形式でも提供されています。


## <a name="sdks"></a>SDK

| アセンブリ | 名前空間| プロトコル | 機能 |
|------------------------|-------------------------------------|---------------------------------|--------------------------------------------------------------------------------------------|
| Azure Resource Manager | Azure.ResourceManager.Communication | [REST](https://docs.microsoft.com/rest/api/communication/communicationservice)| Communication Services のリソースをプロビジョニングおよび管理します|
| 共通 | Azure.Communication.Common| REST | 他の SDK の基本型を指定します |
| ID | Azure.Communication.Identity| [REST](https://docs.microsoft.com/rest/api/communication/communicationidentity)| ユーザーを管理し、トークンにアクセスします|
| 電話番号 _(ベータ)_| Azure.Communication.PhoneNumbers| [REST](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)| 電話番号を取得して管理します |
| チャット | Azure.Communication.Chat| 独自の通知を含む [REST](https://docs.microsoft.com/rest/api/communication/) | アプリケーションにリアルタイムのテキスト ベースのチャットを追加します |
| SMS| Azure.Communication.SMS | [REST](https://docs.microsoft.com/rest/api/communication/sms)| SMS メッセージを送信および受信します|
| 呼び出し| Azure.Communication.Calling | 独自の転送 | 音声、ビデオ、画面共有、およびその他のリアルタイム データ通信機能を使用します |

Azure Resource Manager、ID、および SMS SDK はサービス統合に重点を置いており、多くの場合、これらの機能をエンド ユーザー アプリケーションに統合すると、セキュリティ上の問題が発生します。 Common および Chat SDK は、サービスおよびクライアント アプリケーションに適しています。 Calling SDK は、クライアント アプリケーション向けに設計されています。 サービス シナリオに重点を置いた SDK は開発中です。


### <a name="languages-and-publishing-locations"></a>言語と公開場所

以下では、個々の SDK パッケージの公開場所について説明します。

| 領域           | JavaScript | .NET | Python | Java SE | iOS | Android | その他                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [GitHub 経由の Go](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| 共通         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | 該当なし      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| ID | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| 電話番号 | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| チャット           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| 呼び出し        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| リファレンス ドキュメント     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.android.communication.calling)            | -                              |


## <a name="rest-api-throttles"></a>REST API スロットル
一部の REST API とそれに対応する SDK メソッドには、注意が必要なスロットル制限があります。 これらのスロットル制限を超えると、`429 - Too Many Requests` エラー応答がトリガーされます。 これらの制限は、[Azure サポートへのリクエスト](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)によって引き上げることができます。

| API                                                                                                                          | スロットル            |
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [すべての電話番号検索プラン API](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)         | 4 要求/日      |
| [電話番号プラン (購入)](https://docs.microsoft.com/rest/api/communication/phonenumberadministration/purchasesearch) | 1 月に 1 回の購入  |
| [SMS を送信する](https://docs.microsoft.com/rest/api/communication/sms/send)                                                       | 200 要求/分 |


## <a name="sdk-platform-support-details"></a>SDK プラットフォーム サポートの詳細

### <a name="ios-and-android"></a>iOS と Android 

- Communication Services iOS SDK は、iOS バージョン 13 以降と Xcode 11 以降を対象としています。
- Android Java SDK は、Android API レベル 21 以降と Android Studio 4.0 以降を対象としています

### <a name="net"></a>.NET 

Calling を除き、Communication Services パッケージは、下に示すプラットフォームがサポートされる .NET Standard 2.0 を対象としています。

.NET Framework 4.6.1 を介したサポート
- Windows 10、8.1、8、7
- Windows Server 2012 R2、2012、2008 R2 SP1

.NET Core 2.0 を介したサポート:
- Windows 10 (1607 以降)、7 SP1+、8.1
- Windows Server 2008 R2 SP1+
- Max OS X 10.12 以降
- Linux の複数のバージョン/ディストリビューション
- UWP 10.0.16299 (RS3) 2017 年 9 月
- Unity 2018.1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3.8

## <a name="api-stability-expectations"></a>API の安定性に関する想定

> [!IMPORTANT]
> このセクションでは、**安定** とマークされている REST API および SDK に関するガイダンスを提供します。 プレリリース、プレビュー、またはベータとマークされている API は、**予告なしに** 変更されたり非推奨になったりする場合があります。

将来的に、Communication Services SDK のバージョンが廃止される可能性があります。また、REST API とリリースされた SDK に対して破壊的変更が加えられる可能性があります。 Azure Communication Services は、サービス バージョンの廃止に関して、"*通常は*" 次の 2 つのサポート ポリシーに従います。

- Communication Services インターフェイスの変更に起因するコードの変更を求められる場合、お客様は少なくとも 3 年前に通知されます。 通常、ドキュメントに記載されているすべての REST API と SDK API では、インターフェイスが使用停止になる前に少なくとも 3 年間警告が発生します。
- お客様は、SDK アセンブリを最新のマイナー バージョンに更新する必要が生じる少なくとも 1 年前に通知されます。 これらの必須の更新は、同じメジャー バージョンに含まれているため、コードを変更する必要はありません。 これは、セキュリティとパフォーマンスに関する更新を頻繁に必要とするリアルタイム コンポーネントを持つ Calling および Chat ライブラリに対しては特に当てはまります。 Communication Services SDK を最新の状態に保つことを強くお勧めします。

### <a name="api-and-sdk-decommissioning-examples"></a>API と SDK の廃止の例

**v24 バージョンの SMS REST API をアプリケーションに統合しました。Azure Communication では v25 がリリースされています。**

これらの API の機能が停止し、v25 に強制的に更新される前に、3 年間警告が表示されます。 この更新ではコードの変更が必要になる場合があります。

**v2.02 バージョンの Calling SDK をアプリケーションに統合しました。Azure Communication では v2.05 がリリースされています。**

v2.05 バージョンの Calling SDK への更新を、v2.05 のリリースから 12 か月以内に求められる場合があります。 これはコードを変更せず、成果物を単に置き換えるだけで済みます。v2.05 は v2 メジャー バージョンに含まれ、破壊的変更はないためです。

## <a name="next-steps"></a>次の手順

詳細については、次の SDK の概要を参照してください。

- [Calling SDK の概要](../concepts/voice-video-calling/calling-sdk-features.md)
- [Chat SDK の概要](../concepts/chat/sdk-features.md)
- [SMS SDK の概要](../concepts/telephony-sms/sdk-features.md)

Azure Communication Services の使用を開始するには:

- [Azure Communication のリソースを作成する](../quickstarts/create-communication-resource.md)
- [ユーザー アクセス トークン](../quickstarts/access-tokens.md)を生成する
