---
title: Azure Communication Services の SDK と REST API
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services の SDK と REST API について詳しく説明します。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: baba53d797d3d530b7f71b7f87e01dd673e6a6cc
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966405"
---
# <a name="sdks-and-rest-apis"></a>SDK と REST API

Azure Communication Services の機能は、概念的に 8 つの領域に分けられています。 ほとんどの領域には、インターネット経由で直接使用できる、公開された REST API に対してプログラミングされた、完全にオープンソースである SDK があります。 Calling SDK は独自のネットワーク インターフェイスを使用しており、クローズ ドソースです。

下の表では、これらの領域と、REST API と SDK ライブラリの可用性についてまとめています。 また、API と SDK がエンドユーザー クライアント向けか、信頼できるサービス環境向けかについても記載しています。 SMS などの API と SDK には、信頼度の低い環境にあるエンドユーザーのデバイスから直接アクセスしないようにしてください。

Web ベースの通話およびチャット アプリケーションの開発は、[Azure Communication Services UI ライブラリ](https://azure.github.io/communication-ui-library)によって迅速化できます。 この UI ライブラリには、アプリケーションにドロップできる、運用環境に対応した UI コンポーネントが用意されています。

## <a name="rest-apis"></a>REST API
Communication Services API は、他の Azure REST API と共に [docs.microsoft.com](/rest/api/azure/) に記載されています。 このドキュメントでは、HTTP メッセージを構成する方法と、Postman を使用するためのガイダンスが提供されます。 REST インターフェイス ドキュメントは [GitHub](https://github.com/Azure/azure-rest-api-specs) で Swagger 形式でも提供されています。


## <a name="sdks"></a>SDK
| アセンブリ | プロトコル| Environment | 機能|
|--------|----------|---------|----------------------------------|
| Azure Resource Manager | [REST](/rest/api/communication/communicationservice)| サービス| Communication Services のリソースをプロビジョニングおよび管理します|
| 共通 | 該当なし | クライアントとサービス | 他の SDK の基本型を指定します |
| ID | [REST](/rest/api/communication/communicationidentity) | サービス| ユーザーを管理し、トークンにアクセスします|
| 電話番号| [REST](/rest/api/communication/phonenumbers)| サービス| 電話番号を取得して管理します |
| SMS| [REST](/rest/api/communication/sms) | サービス| SMS メッセージを送信および受信します|
| チャット | 独自の通知を含む [REST](/rest/api/communication/) | クライアントとサービス | リアルタイムのテキスト チャットをアプリケーションに追加します |
| 呼び出し| 独自の転送 | クライアント | 音声、ビデオ、画面共有、および他のリアルタイム通信 |
| 呼び出し元のサーバー | REST| サービス| 呼び出しの作成と管理、音声の再生、および記録の構成 |
| ネットワーク トラバーサル| REST| サービス| TURN サーバーへのアクセスによる低レベルのデータ転送 |
| UI ライブラリ | 該当なし | クライアント | チャットおよび通話アプリ用の運用環境対応 UI コンポーネント |

### <a name="languages-and-publishing-locations"></a>言語と公開場所

以下では、個々の SDK パッケージの公開場所について説明します。

| 領域 | JavaScript | .NET | Python | Java SE | iOS | Android | その他|
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | [npm](https://www.npmjs.com/package/@azure/arm-communication) | [NuGet](https://www.NuGet.org/packages/Azure.ResourceManager.Communication)| [PyPi](https://pypi.org/project/azure-mgmt-communication/)| [Maven](https://search.maven.org/search?q=azure-resourcemanager-communication)| -| -| [GitHub 経由の Go](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| 共通 | [npm](https://www.npmjs.com/package/@azure/communication-common) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Common/)| 該当なし| [Maven](https://search.maven.org/search?q=a:azure-communication-common) | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)| [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common) | -|
| ID | [npm](https://www.npmjs.com/package/@azure/communication-identity) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Identity)| [PyPi](https://pypi.org/project/azure-communication-identity/)| [Maven](https://search.maven.org/search?q=a:azure-communication-identity) | -| -| -|
| 電話番号 | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.PhoneNumbers)| [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)| [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers) | -| -| -|
| チャット | [npm](https://www.npmjs.com/package/@azure/communication-chat)| [NuGet](https://www.NuGet.org/packages/Azure.Communication.Chat) | [PyPi](https://pypi.org/project/azure-communication-chat/) | [Maven](https://search.maven.org/search?q=a:azure-communication-chat) | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)| [Maven](https://search.maven.org/search?q=a:azure-communication-chat) | -|
| SMS| [npm](https://www.npmjs.com/package/@azure/communication-sms) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Sms)| [PyPi](https://pypi.org/project/azure-communication-sms/) | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms) | -| -| -|
| 呼び出し| [npm](https://www.npmjs.com/package/@azure/communication-calling) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Calling) | -| - | [GitHub](https://github.com/Azure/Communication/releases) | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)| -|
|呼び出し自動化||[NuGet](https://www.NuGet.org/packages/Azure.Communication.CallingServer/)||[Maven](https://search.maven.org/artifact/com.azure/azure-communication-callingserver)
|ネットワーク トラバーサル| [npm](https://www.npmjs.com/package/@azure/communication-network-traversal)|[NuGet](https://www.NuGet.org/packages/Azure.Communication.NetworkTraversal/)
| UI ライブラリ| [npm](https://www.npmjs.com/package/@azure/communication-react) | - | - | - | - | - | [GitHub](https://github.com/Azure/communication-ui-library)、[Storybook](https://azure.github.io/communication-ui-library/?path=/story/overview--page) |
| リファレンス ドキュメント | [docs](https://azure.github.io/azure-sdk-for-js/communication.html) | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)| -| [docs](http://azure.github.io/azure-sdk-for-java/communication.html) | [docs](/objectivec/communication-services/calling/)| [docs](/java/api/com.azure.android.communication.calling)| -|

フレンドリ アセンブリ名と名前空間の間のマッピングを以下に示します。

| アセンブリ | 名前空間 |
|------------------------|--------------------------------------|
| Azure Resource Manager | Azure.ResourceManager.Communication|
| 共通 | Azure.Communication.Common |
| ID | Azure.Communication.Identity |
| 電話番号| Azure.Communication.PhoneNumbers |
| SMS| Azure.Communication.SMS|
| チャット | Azure.Communication.Chat |
| 呼び出し| Azure.Communication.Calling|
| 呼び出し元のサーバー | Azure.Communication.CallingServer|
| ネットワーク トラバーサル| Azure.Communication.NetworkTraversal |
| UI ライブラリ | Azure.Communication.Calling|


## <a name="rest-api-throttles"></a>REST API スロットル
一部の REST API とそれに対応する SDK メソッドには、注意が必要なスロットル制限があります。 これらのスロットル制限を超えると、`429 - Too Many Requests` エラー応答がトリガーされます。 これらの制限は、[Azure サポートへのリクエスト](../../azure-portal/supportability/how-to-create-azure-support-request.md)によって引き上げることができます。

| API| スロットル|
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [すべての電話番号検索プラン API](/rest/api/communication/phonenumbers) | 4 要求/日|
| [電話番号プラン (購入)](/rest/api/communication/phonenumbers/purchasephonenumbers) | 1 月に 1 回の購入|
| [SMS を送信する](/rest/api/communication/sms/send) | 200 要求/分 |


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

通話パッケージでは、以下で .NET Native または C++/WinRT を使用してビルドされた UWP アプリがサポートされています。
- Windows 10 10.0.17763
- Windows Server 2019 10.0.17763

## <a name="api-stability-expectations"></a>API の安定性に関する想定

> [!IMPORTANT]
> このセクションでは、**安定** とマークされている REST API および SDK に関するガイダンスを提供します。 プレリリース、プレビュー、またはベータとマークされている API は、**予告なしに** 変更されたり非推奨になったりする場合があります。

将来的に、Communication Services SDK のバージョンが廃止される可能性があります。また、REST API とリリースされた SDK に対して破壊的変更が加えられる可能性があります。 Azure Communication Services は、サービス バージョンの廃止に関して、"*通常は*" 次の 2 つのサポート ポリシーに従います。

- Communication Services インターフェイスの変更に起因するコードの変更を求められる場合、お客様は少なくとも 3 年前に通知されます。 通常、ドキュメントに記載されているすべての REST API と SDK API では、インターフェイスが使用停止になる前に少なくとも 3 年間警告が発生します。
- お客様は、SDK アセンブリを最新のマイナー バージョンに更新する必要が生じる少なくとも 1 年前に通知されます。 これらの必須の更新は、同じメジャー バージョンに含まれているため、コードを変更する必要はありません。 最新の SDK を使用することは、セキュリティとパフォーマンスに関する更新を頻繁に必要とするリアルタイム コンポーネントである Calling および Chat ライブラリにとって特に重要です。 すべての Communication Services SDK を最新の状態に保つことを強くお勧めします。

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

- [Azure Communication Services リソースを作成する](../quickstarts/create-communication-resource.md)
- [ユーザー アクセス トークン](../quickstarts/access-tokens.md)を生成する
