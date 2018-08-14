---
title: BizTalk Services における発行者名と発行者キー | Microsoft Docs
description: BizTalk サービスの Service Bus または Access Control (ACS) の発行者名と発行者キーを取得する方法について説明します。 MABS、WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 78796b5dc62cb573f149c24d90205d26fb139cf7
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628649"
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk サービス: 発行者名および発行者キーに関するページ

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk Services では、サービス バス発行者名および発行者キーと Access Control 発行者名および発行者キーが使用されます。 具体的には次の処理が行われます。

| タスク | 発行者名および発行者キー |
| --- | --- |
| Visual Studio からアプリケーションをデプロイする |Access Control 発行者名および発行者キー |
| Azure BizTalk サービス ポータルを構成する |Access Control 発行者名および発行者キー |
| Visual Studio で BizTalk Adapter サービスを使用して LOB リレーを作成する |Service Bus 発行者名および発行者キー |

このトピックでは、発行者名と発行者キーを取得する手順を示します。 

## <a name="access-control-issuer-name-and-issuer-key"></a>Access Control 発行者名および発行者キー
Access Control 発行者名および発行者キーは次のコンポーネントで使用されます。

* Visual Studio で作成された Azure BizTalk サービス アプリケーション: Visual Studio で BizTalk サービス アプリケーションを Azure にデプロイするには、Access Control 発行者名と発行者キーを入力する必要があります。 
* Azure BizTalk Services ポータル: BizTalk サービスを作成して BizTalk Services ポータルを開くと、Access Control 発行者名と発行者キーが、同じ Access Control 値で展開に自動的に登録されます。

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Access Control 発行者名および発行者キーの取得

ACS で認証して、発行者名と発行者キーの値を取得するための全体的な手順は次のとおりです。

1. [Azure PowerShell コマンドレット](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)をインストールします。
2. Azure アカウント `Add-AzureAccount` を追加します
3. サブスクリプション名 `get-azuresubscription` が返されます
4. サブスクリプション `select-azuresubscription <name of your subscription>` を選択します 
5. 新しい名前空間 `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging` を作成します

    例:    `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. 新しい ACS 名前空間が作成されたら (数分かかる場合があります)、発行者名と発行者キーの一覧が接続文字列に表示されます。 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

まとめると次のようになります。  
発行者名 = SharedSecretIssuer  
発行者キー = SharedSecretKey

詳細については、[New-AzureSBNamespace](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace) コマンドレットに関するページをご覧ください。 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Service Bus 発行者名および発行者キー
サービス バス発行者名および発行者キーは BizTalk Adapter サービスで使用されます。 Visual Studio の BizTalk サービス プロジェクトでは、BizTalk Adapter サービスを使用して内部設置型の基幹業務 (LOB) システムに接続します。 接続するには、LOB リレーを作成し、LOB システムの詳細を入力します。 このとき、サービス バス発行者名と発行者キーも入力します。

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Service Bus 発行者名および発行者キーを取得するには
1. [Azure ポータル](http://portal.azure.com)にサインインします。
2. **Service Bus** を検索し、名前空間を選択します。 
3. **[共有アクセス ポリシー]** プロパティを開き、ポリシーを選択し、名前とキーの値の **[接続文字列]** を表示します。  

## <a name="next"></a>次へ
Azure BizTalk Services に関するその他のトピック:

* [Azure BizTalk Services SDK のインストール](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [チュートリアル: Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Visual Studio でのプロジェクトの作成](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>関連項目
* [方法: ACS 管理サービスを使用してサービス ID を構成する](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: プロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: サービスの状態のチャート](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk サービス: バックアップと復元に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk サービス: 調整](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

