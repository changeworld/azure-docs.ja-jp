<properties 
	pageTitle="Azure API Management でサービスのバックアップと復元を使用して障害復旧を実装する方法" 
	description="Azure API Management でバックアップと復元を使用して障害復旧を行う方法について説明します。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Azure API Management でサービスのバックアップと復元を使用して障害復旧を実装する方法

Azure API Management を介して API の発行と管理を行うように選択することで、そうしなければ自分で設計、実装、および管理する必要のある、さまざまなフォールト トレランス機能やインフラストラクチャ機能を利用できるようになります。Azure プラットフォームにより、わずかな料金で潜在的な障害の大部分が軽減されます。

API Management サービスがホストされているリージョンに影響する可用性の問題から復旧するためには、いつでも異なるリージョンにサービスを再構成できるように準備しておく必要があります。可用性の目標と復旧時間の目標に応じて、バックアップ サービスを 1 つ以上のリージョンに確保し、それらの構成と内容がアクティブ サービスと同期するように保守することができます。サービス バックアップと復元の機能は、災害復旧戦略を実装するために必要な構成要素となります。

サービス バックアップと復元の機能は、Service Management REST API を介して使用可能です。この API にアクセスする方法について詳しくは、「[Azure リソース マネージャーの要求の認証][Azure リソース マネージャーの要求の認証]」を参照してください。

## このトピックの内容

-   [API Management サービスのバックアップ][API Management サービスのバックアップ]
-   [API Management サービスの復元][API Management サービスの復元]

## <a name="step1"> </a>API Management サービスのバックアップ

API Management サービスをバックアップするには、次の HTTP 要求を発行します。

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

各値の説明:

-   `subscriptionId` - バックアップ対象の API Management サービスを含むサブスクリプションの ID
-   `resourceGroupName` - 'Api-Default-{service-region}' の形式の文字列。ここで、`service-region` はバックアップ対象の API Management サービスがホストされている Azure リージョンです。例: `North-Central-US`
-   `serviceName` - バックアップを作成する API Management サービスの、作成時に指定された名前
-   `api-version` - 次に置き換えます `2014-02-14`

要求の本文に、ターゲットの Azure ストレージ アカウント、アクセス キー、BLOB コンテナー名、およびバックアップ名を指定します。

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

`Content-Type` 要求ヘッダーの値を `application\json` に設定します。

バックアップは完了まで何分もかかる長時間の処理です。要求が成功してバックアップ処理が開始されると、`Location` ヘッダーのある `202 Accepted` 応答ステータス コードを受け取ります。`Location` ヘッダー内の URL に「GET」要求を出して、処理のステータスを確認します。バックアップの進行中は、「202 Accepted」ステータス コードの受け取りが続きます。応答コードの `200 OK` は、バックアップ処理が正常に終了したことを示します。

**メモ**:

-   要求の本文に指定された**コンテナー**は、**存在する必要があります**。
-   バックアップの進行中には、層のアップグレードやダウングレード、ドメイン名の変更、その他の操作などの、**サービス管理操作は試行しないでください**。
-   バックアップの復元は、作成されたときから **7 日間だけ保証されます**。
-   分析レポートの生成に使用される**使用状況データ**は、バックアップに**含まれません**。[Azure API Management REST API][Azure API Management REST API] を使用して、分析レポートを保管用に定期的に取り出します。
-   サービス バックアップを実行する頻度は、復旧ポイントの目標に影響を与えます。その頻度を最小限に抑えるため、定期的なバックアップを実装すると共に、API Management サービスに対して重要な変更を行った後のオンデマンドのバックアップを実行することをお勧めします。
-   バックアップ処理の進行中にサービス構成 (API、ポリシー、開発者ポータルの外観など) に対して行われる**変更**は、**バックアップに含まれないので失われる可能性があります**。

## <a name="step2"> </a>API Management サービスの復元

以前に作成されたバックアップから API Management サービスを復元するには、次の HTTP 要求を行います。

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

各値の説明:

-   `subscriptionId` - バックアップの復元先の API Management サービスを含むサブスクリプションの ID
-   `resourceGroupName` - 'Api-Default-{service-region}' の形式の文字列。ここで、`service-region` はバックアップの復元先の API Management サービスがホストされている Azure リージョンです。例: `North-Central-US`
-   `serviceName` - 復元先の API Management サービスの、作成時に指定された名前
-   `api-version` - 次に置き換えます `2014-02-14`

要求の本文に、バックアップ ファイルの場所、つまり Azure ストレージ アカウント、アクセス キー、BLOB コンテナー名、およびバックアップ名を指定します。

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

`Content-Type` 要求ヘッダーの値を `application\json` に設定します。

復元は、完了までに 30 分以上かかることのある長時間の操作です。要求が成功して復元処理が開始されると、`Location` ヘッダーのある `202 Accepted` 応答ステータス コードを受け取ります。`Location` ヘッダー内の URL に「GET」要求を出して、処理のステータスを確認します。復元の進行中は、「202 Accepted」ステータス コードの受け取りが続きます。応答コードの `200 OK` は、復元処理が正常に終了したことを示します。

**メモ**:

-   復元先のサービスの**層**は、復元されるバックアップ サービスの層と**一致しなければなりません**。
-   復元処理の進行中にサービス構成 (API、ポリシー、開発者ポータルの外観など) に対して行われる**変更**は、**上書きされることがあります**。

  [Azure リソース マネージャーの要求の認証]: http://msdn.microsoft.com/library/dn790557.aspx
  [API Management サービスのバックアップ]: #step1
  [API Management サービスの復元]: #step2
  [Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

<!--HONumber=46--> 
