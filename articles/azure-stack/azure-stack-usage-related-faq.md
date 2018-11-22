---
title: Usage API のよくあるご質問 (FAQ) | Microsoft Docs
description: Azure Stack の測定、Azure Usage API との比較、使用時間と報告時間、エラー コードの一覧。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 95b63b525365a5a31856c2f14ac965aaa1113cff
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515626"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Azure Stack Usage API のよくあるご質問

この記事では、Azure Stack Usage API についてよくあるご質問 (FAQ) とその回答を紹介します。

## <a name="what-meter-ids-can-i-see"></a>どのような測定 ID を表示できますか。
次のリソース プロバイダーの使用状況が報告されます。

### <a name="network"></a>ネットワーク
  
**測定 ID**: F271A8A388C44D93956A063E1D2FA80B  
**測定名**: 静的 IP アドレスの使用状況  
**単位**: IP アドレス  
**備考**: 使用中の IP アドレス数。 使用状況 API を日単位の細分性で呼び出した場合、メーターは IP アドレスに時間数を乗算して返します。  
  
**測定 ID**: 9E2739BA86744796B465F64674B822BA  
**測定名**: 動的 IP アドレスの使用状況  
**単位**: IP アドレス  
**備考**: 使用中の IP アドレス数。 使用状況 API を日単位の細分性で呼び出した場合、メーターは IP アドレスに時間数を乗算して返します。  
  
### <a name="storage"></a>Storage
  
**測定 ID**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**測定名**: TableCapacity  
**単位**: GB\*時間数  
**備考**: テーブルによって使用された合計容量。  
  
**測定 ID**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**測定名**: PageBlobCapacity  
**単位**: GB\*時間数  
**備考**: ページ BLOB によって使用された合計容量。  
  
**測定 ID**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**測定名**: QueueCapacity  
**単位**: GB\*時間数  
**備考**: キューによって使用された合計容量。  
  
**測定 ID**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**測定名**: BlockBlobCapacity  
**単位**: GB\*時間数  
**備考**: ブロック BLOB によって使用された合計容量。  
  
**測定 ID**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**測定名**: TableTransactions  
**単位**: 10,000 秒間の要求数  
**備考**: Table service 要求 (10,000 件)。  
  
**測定 ID**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**測定名**: TableDataTransIn  
**単位**: GB 単位の受信データ  
**備考**: GB 単位の Table service データ受信。  
  
**測定 ID**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**測定名**: TableDataTransOut  
**単位**: GB 単位のエグレス  
**備考**: GB 単位の Table service データ送信  
  
**測定 ID**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**測定名**: BlobTransactions  
**単位**: 10,000 秒間の要求数  
**備考**: Blob service 要求 (10,000 件)。  
  
**測定 ID**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**測定名**: BlobDataTransIn  
**単位**: GB 単位の受信データ  
**備考**: GB 単位の Blob service データ受信。  
  
**測定 ID**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**測定名**: BlobDataTransOut  
**単位**: GB 単位のエグレス  
**備考**: GB 単位の Blob service データ送信。  
  
**測定 ID**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**測定名**: QueueTransactions  
**単位**: 10,000 秒間の要求数  
**備考**: Queue サービス要求 (10,000 件)。  
  
**測定 ID**: E518E809-E369-4A45-9274-2017B29FFF25  
**測定名**: QueueDataTransIn  
**単位**: GB 単位の受信データ  
**備考**: GB 単位の Queue サービス データ受信。  
  
**測定 ID**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**測定名**: QueueDataTransOut  
**単位**: GB 単位のエグレス  
**備考**: GB 単位の Queue サービス データ送信  

### <a name="compute"></a>コンピューティング 
  
**測定 ID**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**測定名**: ベース VM サイズ時間  
**単位**: 仮想コア時間  
**備考**: VM が実行された時間を乗算した仮想コア数。  
  
**測定 ID**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**測定名**: Windows VM サイズ時間  
**単位**: 仮想コア時間  
**備考**: VM が実行された時間を乗算した仮想コア数。  
  
**測定 ID**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**測定名**: VM サイズ時間  
**単位**: VM 時間  
**備考**: ベース VM と Windows VM の両方をキャプチャ。 コアの調整なし。  
  
### <a name="managed-disks"></a>Managed Disks

**測定 ID**: 380874f9-300c-48e0-95a0-d2d9a21ade8f   
**測定名**: S4   
**単位**: ディスク数 \* 月   
**備考**: Standard マネージド ディスク - 32 GB 

**測定 ID**: 1b77d90f-427b-4435-b4f1-d78adec53222   
**測定名**: S6   
**単位**: ディスク数 \* 月   
**備考**: Standard マネージド ディスク - 64 GB 

**測定 ID**: d5f7731b-f639-404a-89d0-e46186e22c8d   
**測定名**: S10   
**単位**: ディスク数 \* 月   
**備考**: Standard マネージド ディスク - 128 GB 

**測定 ID**: ff85ef31-da5b-4eac-95dd-a69d6f97b18a   
**測定名**: S15   
**単位**: ディスク数 \* 月   
**備考**: Standard マネージド ディスク - 256 GB 

**測定 ID**: 88ea9228-457a-4091-adc9-ad5194f30b6e   
**測定名**: S20   
**単位**: ディスク数 \* 月      
**備考**: Standard マネージド ディスク - 512 GB 

**測定 ID**: 5b1db88a-8596-4002-8052-347947c26940   
**測定名**: S30   
**単位**: ディスク数 \* 月   
**備考**: Standard マネージド ディスク - 1024 GB 

**測定 ID**: 7660b45b-b29d-49cb-b816-59f30fbab011   
**測定名**: P4   
**単位**: ディスク数 \* 月   
**備考**: Premium マネージド ディスク - 32 GB 

**測定 ID**: 817007fd-a077-477f-bc01-b876f27205fd   
**測定名**: P6   
**単位**: ディスク数 \* 月   
**備考**: Premium マネージド ディスク - 64 GB 

**測定 ID**: e554b6bc-96cd-4938-a5b5-0da990278519   
**測定名**: P10   
**単位**: ディスク数 \* 月   
**備考**: Premium マネージド ディスク - 128 GB  

**測定 ID**: cdc0f53a-62a9-4472-a06c-e99a23b02907   
**測定名**: P15  
**単位**: ディスク数 \* 月   
**備考**: Premium マネージド ディスク - 256 GB 

**測定 ID**: b9cb2d1a-84c2-4275-aa8b-70d2145d59aa   
**測定名**: P20   
**単位**: ディスク数 \* 月   
**備考**: Premium マネージド ディスク - 512 GB 

**測定 ID**: 06bde724-9f94-43c0-84c3-d0fc54538369   
**測定名**: P30   
**単位**: ディスク数 \* 月   
**備考**: Premium マネージド ディスク - 1024 GB 

**測定 ID**: 7ba084ec-ef9c-4d64-a179-7732c6cb5e28   
**測定名**: ActualStandardDiskSize   
**単位**: GB \* 月      
**備考**: Standard マネージド ディスクのディスク上の実際のサイズ  

**測定 ID**: daef389a-06e5-4684-a7f7-8813d9f792d5  
**測定名**: ActualPremiumDiskSize   
**単位**: GB \* 月      
**備考**: Premium マネージド ディスクのディスク上の実際のサイズ 

**測定 ID**: 75d4b707-1027-4403-9986-6ec7c05579c8  
**測定名**: ActualStandardSnapshotSize   
**単位**: GB \* 月   
**備考**: マネージド Standard スナップショットのディスク上の実際のサイズ。  

**測定 ID**: 5ca1cbb9-6f14-4e76-8be8-1ca91547965e   
**測定名**: ActualPremiumSnapshotSize   
**単位**: GB \* 月   
**備考**: マネージド Premium のディスク上の実際のサイズ。   

**測定 ID**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**測定名**: S4   
**単位**: ディスク数\* (時間)   
**備考**: Standard マネージド ディスク – 32 GB (非推奨) 

**測定 ID**: dc9fc6a9-0782-432a-b8dc-978130457494   
**測定名**: S6   
**単位**: ディスク数\* (時間)   
**備考**: Standard マネージド ディスク – 64 GB (非推奨) 

**測定 ID**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**測定名**: S10   
**単位**: ディスク数\* (時間)   
**備考**: Standard マネージド ディスク – 128 GB (非推奨) 

**測定 ID**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**測定名**: S15   
**単位**: ディスク数\* (時間)   
**備考**: Standard マネージド ディスク – 256 GB (非推奨) 

**測定 ID**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**測定名**: S20   
**単位**: ディスク数\* (時間)      
**備考**: Standard マネージド ディスク – 512 GB (非推奨) 

**測定 ID**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**測定名**: S30   
**単位**: ディスク数\* (時間)   
**備考**: Standard マネージド ディスク – 1024 GB (非推奨) 

**測定 ID**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**測定名**: P4   
**単位**: ディスク数\* (時間)   
**備考**: Premium マネージド ディスク – 32 GB (非推奨) 

**測定 ID**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**測定名**: P6   
**単位**: ディスク数\* (時間)   
**備考**: Premium マネージド ディスク – 64 GB (非推奨) 

**測定 ID**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**測定名**: P10   
**単位**: ディスク数\* (時間)   
**備考**: Premium マネージド ディスク – 128 GB (非推奨)  

**測定 ID**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**測定名**: P15  
**単位**: ディスク数\* (時間)   
**備考**: Premium マネージド ディスク – 256 GB (非推奨) 

**測定 ID**: c7e7839c-293b-4761-ae4c-848eda91130b   
**測定名**: P20   
**単位**: ディスク数\* (時間)   
**備考**: Premium マネージド ディスク – 512 GB (非推奨) 

**測定 ID**: 9f502103-adf4-4488-b494-456c95d23a9f   
**測定名**: P30   
**単位**: ディスク数\* (時間)   
**備考**: Premium マネージド ディスク – 1024 GB (非推奨) 

**測定 ID**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**測定名**: ActualStandardDiskSize   
**単位**: バイト\* (時間)      
**備考**: Standard マネージド ディスクのディスク上の実際のサイズ (非推奨)  

**測定 ID**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**測定名**: ActualPremiumDiskSize   
**単位**: バイト\* (時間)      
**備考**: Premium マネージド ディスクのディスク上の実際のサイズ (非推奨) 

**測定 ID**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**測定名**: ActualStandardSnapshotSize   
**単位**: バイト\* (時間)   
**備考**: マネージド Standard スナップショットのディスク上の実際のサイズ (非推奨) 

**測定 ID**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**測定名**: ActualPremiumSnapshotSize   
**単位**: バイト\* (時間)   
**備考**: マネージド Premium のディスク上の実際のサイズ (非推奨) 

### <a name="sql-rp"></a>Sql RP
  
**測定 ID**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**測定名**: DatabaseSizeHourSqlMeter  
**単位**: MB\*時間数  
**備考**: 作成時のデータベースの合計容量。 使用状況 API を日単位の細分性で呼び出した場合、メーターは MB に時間数を乗算して返します。  
  
### <a name="mysql-rp"></a>MySql RP   
  
**測定 ID**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**測定名**: DatabaseSizeHourMySqlMeter  
**単位**: MB\*時間数  
**備考**: 作成時のデータベースの合計容量。 使用状況 API を日単位の細分性で呼び出した場合、メーターは MB に時間数を乗算して返します。    
### <a name="key-vault"></a>Key Vault   
  
**測定 ID**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**測定名**: Key Vault トランザクション  
**単位**: 10,000 秒間の要求数  
**備考**: Key Vault データ プレーンによって受信された REST API 要求数。  
  
**測定 ID**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**測定名**: 高度なキー トランザクション  
**単位**: 10K のトランザクション  
**備考**: RSA 3K/4K、ECC キー トランザクション。 (プレビュー)。  
  
### <a name="app-service"></a>App Service   
  
**測定 ID**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**測定名**: App Service  
**単位**: 仮想コア時間  
**備考**: App Service を実行するのに使用した仮想コア数。 注意: マイクロソフトはこの測定を使用して、Azure Stack の App Service の料金を請求します。 クラウド サービス プロバイダーは、他の App Service の測定 (下記) を使用して、テナントの使用状況を計算できます。  
  
**測定 ID**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**測定名**: Functions 要求  
**単位**: 10 個の要求  
**備考**: 要求された実行の合計数 (実行 10 回あたり)。 実行回数は、イベントに応じて、またはバインドによってトリガーされて、関数が実行されるたびにカウントされます。  
  
**測定 ID**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**測定名**: 関数 - コンピューティング  
**単位**: GB/秒  
**備考**: ギガバイト/秒 (GB/秒) 単位で測定されたリソース使用量。 **実際のリソース使用量**は、平均メモリ サイズ (GB) に関数の実行にかかった時間 (ミリ秒) を乗じて計算されます。 関数によって使用されたメモリは、128 MB 単位で切り上げて測定されます。最大メモリ サイズは 1,536 MB です。実行時間は 1 ミリ秒単位で切り上げて計算されます。 1 つの関数の実行の最小実行時間は 100 ミリ秒、最小メモリは 128 MB です。  
  
**測定 ID**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**測定名**: Shared App Service 時間  
**単位**: 1 時間  
**備考**: Shared App Service プランの 1 時間あたりの使用量。 プランはアプリ単位で課金されます。  
  
**測定 ID**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**測定名**: Free App Service 時間  
**単位**: 1 時間  
**備考**: Free App Service プランの 1 時間あたりの使用量。 プランはアプリ単位で課金されます。  
  
**測定 ID**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**測定名**: 小規模 Standard App Service 時間  
**単位**: 1 時間  
**備考**: インスタンスのサイズと数に基づいて計算されます。  
  
**測定 ID**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**測定名**: 中規模 Standard App Service 時間  
**単位**: 1 時間  
**備考**: インスタンスのサイズと数に基づいて計算されます。  
  
**測定 ID**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**測定名**: 大規模 Standard App Service 時間  
**単位**: 1 時間  
**備考**: インスタンスのサイズと数に基づいて計算されます。  
  
### <a name="custom-worker-tiers"></a>カスタム worker 層   
  
**測定 ID**: *カスタム worker 層*  
**測定名**: カスタム worker 層  
**単位**: 時間  
**備考**: 決定論決的測定 ID は、SKU とカスタム worker 層の名前に基づいて作成されます。 この測定 ID は各カスタム worker 層で位置値です。  
  
**測定 ID**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**測定名**: SNI SSL  
**単位**: Per SNI SSL バインディングあたり  
**備考**: App Service がサポートする SSL 接続には、Server Name Indication (SNI) SSL 接続と、IP アドレス SSL 接続の 2 種類があります。 SNI ベースの SSL は、最新のブラウザーで使用できますが、IP ベースの SSL はすべてのブラウザーで使用できます。  
  
**測定 ID**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**測定名**: IP SSL  
**単位**: IP ベースの SSL バインディングあたり  
**備考**: App Service がサポートする SSL 接続には、Server Name Indication (SNI) SSL 接続と、IP アドレス SSL 接続の 2 種類があります。 SNI ベースの SSL は、最新のブラウザーで使用できますが、IP ベースの SSL はすべてのブラウザーで使用できます。  
  
**測定 ID**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**測定名**: Web プロセス  
**単位**:  
**備考**: 1 時間あたりとしてアクティブ サイトごとに計算されます。  
  
**測定 ID**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**測定名**: 外部送信帯域幅  
**単位**: GB  
**備考**: 受信要求応答の合計バイト数 + 送信要求応答の合計バイト数 + 受信 FTP 要求応答の合計バイト数 + 受信 Web デプロイ要求応答の合計バイト数。  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>Azure Stack Usage API は [Azure Usage API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (現在パブリック プレビュー中) と比較してどうですか。
* Tenant Usage API は、Azure API と一貫性がありますが、唯一の例外として、現在 Azure Stack では *showDetails* フラグがサポートされていません。
* Provider Usage API は、Azure Stack にのみ適用されます。
* 現在、Azure で使用可能な [RateCard API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) は Azure Stack で使用できません。

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>使用時間と報告時間の違いは何ですか。
使用状況データ レポートには、2 つの主要な時間値があります。

* **報告時間**。 使用状況システムに使用状況イベントが入力された時間
* **使用時間**。 Azure Stack リソースが使用された時間

特定の使用状況イベントに対して、使用時間と報告時間の値に不一致が見られることがあります。 遅延はすべての環境で数時間に達することもあります。

現在、"*報告時間によってのみ*" クエリを実行できます。

## <a name="what-do-these-usage-api-error-codes-mean"></a>これらの Usage API エラー コードの意味は何ですか。
| **HTTP 状態コード** | **エラー コード** | **説明** |
| --- | --- | --- |
| 400/無効な要求 |*NoApiVersion* |*api-version* クエリ パラメーターがありません。 |
| 400/無効な要求 |*InvalidProperty* |プロパティが見つからないか、無効な値が指定されています。 応答本文のエラー コード内のメッセージに、見つからないプロパティが示されています。 |
| 400/無効な要求 |*RequestEndTimeIsInFuture* |*ReportedEndTime* の値は将来の値です。 この引数では将来の値を使用できません。 |
| 400/無効な要求 |*SubscriberIdIsNotDirectTenant* |プロバイダー API 呼び出しで、呼び出し元の有効なテナントではないサブスクリプション ID が使用されました。 |
| 400/無効な要求 |*SubscriptionIdMissingInRequest* |呼び出し元のサブスクリプション ID がありません。 |
| 400/無効な要求 |*InvalidAggregationGranularity* |無効な集計単位が要求されました。 有効な値は、日単位と時間単位です。 |
| 503 |*ServiceUnavailable* |サービスがビジー状態か、呼び出しが調整されているため、再試行可能エラーが発生しました。 |

## <a name="next-steps"></a>次の手順
[Azure Stack でのお客様への請求と配賦](azure-stack-billing-and-chargeback.md)

[プロバイダー リソース使用量 API](azure-stack-provider-resource-api.md)

[テナント リソース使用量 API](azure-stack-tenant-resource-usage-api.md)
