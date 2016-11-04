---
title: Log Analytics の Azure Key Vault ソリューション | Microsoft Docs
description: Log Analytics の Azure Key Vault ソリューションを使用して、Azure Key Vault のログを調査することができます。
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: richrund

---
# <a name="azure-key-vault-(preview)-solution-in-log-analytics"></a>Log Analytics の Azure Key Vault (プレビュー) ソリューション
> [!NOTE]
> これは[プレビュー ソリューション](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features)です。
> 
> 

Log Analytics の Azure Key Vault ソリューションを使用して、Azure Key Vault の AuditEvent ログを調査することができます。

Azure Key Vault の監査イベントに対するログを記録することができます。 これらのログは Azure Blob Storage に書き込まれ、そこで Log Analytics によってインデックスが作成されて検索や分析が可能となります。

## <a name="install-and-configure-the-solution"></a>ソリューションのインストールと構成
Azure Key Vault ソリューションのインストールと構成は、次の手順で行います。

1. 監視する [Key Vault リソースの診断ログ](../key-vault/key-vault-logging.md)を有効にします。
2. Blob Storage からログを読み取るための設定を Log Analytics に対して行います。[Blob Storage 内の JSON ファイル](log-analytics-azure-storage-json.md)に関するページで説明されている手順に従ってください。
3. 「[ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」に説明されている手順に従って Azure Key Vault ソリューションを有効にします。  

## <a name="review-azure-key-vault-data-collection-details"></a>Azure Key Vault データ収集の詳細の確認
Azure Key Vault の診断ログは、Azure Key Vault ソリューションによって Azure Blob Storage から収集されます。
データ収集のためのエージェントは不要です。

次の表は、Azure Key Vault のデータ収集手段とデータ収集方法に関する各種情報をまとめたものです。

| Platform | 直接エージェント | Systems Center Operations Manager (SCOM) エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![いいえ](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![いいえ](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![あり](./media/log-analytics-azure-keyvault/oms-bullet-green.png) |![なし](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![なし](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |10 分 |

## <a name="use-azure-key-vault"></a>Azure Key Vault の使用
このソリューションのインストール後は、監視対象となる Key Vault について、一定期間における要求の状態を、Log Analytics の **[概要]** ページの **[Azure Key Vault]** タイルに集約して表示できます。

![[Azure Key Vault] タイルの画像](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

**[概要]** タイルをクリックした後、収集されたログの概要を表示し、次のカテゴリについて詳細表示することができます。

* すべての Key Vault 操作の時系列ボリューム
* 失敗した操作の時系列ボリューム
* 操作ごとの平均待機時間
* 操作のサービス品質 (所要時間が 1,000 ミリ秒を超える操作の数と所要時間が 1,000 ミリ秒を超える操作の一覧)

![Azure Key Vault ダッシュボードの画像](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Azure Key Vault ダッシュボードの画像](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>いずれかの操作の詳細を表示するには
1. **[概要]** ページの **[Azure Key Vault]** タイルをクリックします。
2. **[Azure Key Vault]** ダッシュボードにあるいずれかのブレードで概要情報を確認し、ログの検索ページで、詳細情報の表示対象をクリックします。
   
    どのログの検索ページでも、時間、詳細結果、ログ検索履歴を表示することができます。 結果を絞り込むファセットを使用してフィルター処理することもできます。

## <a name="log-analytics-records"></a>Log Analytics のレコード
Azure Key Vault ソリューションによって分析されるのは、Azure 診断の [AuditEvent ログ](../key-vault/key-vault-logging.md)から収集された **KeyVaults** タイプのレコードです。  これらのレコードは、次の表に示したプロパティを持ちます。  

| プロパティ | 説明 |
|:--- |:--- |
| 型 |*KeyVaults* |
| SourceSystem |*AzureStorage* |
| CallerIpAddress |要求を行ったクライアントの IP アドレス |
| カテゴリ |Key Vault のログの場合、AuditEvent は使用可能な単一の値です。 |
| CorrelationId |オプションの GUID であり、クライアント側のログとサービス側の (Key Vault) ログを対応付ける場合に渡します。 |
| DurationMs |REST API 要求を処理するのにかかった時間 (ミリ秒単位) です。 これにネットワーク待機時間は含まれません。したがって、クライアント側で測定する時間はこの時間と一致しない場合があります。 |
| HttpStatusCode_d |要求によって返された HTTP 状態コード |
| Id_s |要求の一意の ID |
| Identity_o |REST API 要求を行う場合に提示されたトークンからの ID です。 これは、通常、Azure PowerShell コマンドレットの実行結果として生じる要求の場合と同様に、"user"、"service principal"、または組み合わせ "user+appId" となります。 |
| OperationName |操作の名前 (「[Azure Key Vault のログ記録](../key-vault/key-vault-logging.md)」を参照) |
| OperationVersion |クライアントによって要求された REST API バージョン |
| RemoteIPLatitude |要求を行ったクライアントの緯度 |
| RemoteIPLongitude |要求を行ったクライアントの経度 |
| RemoteIPCountry |要求を行ったクライアントの国 |
| RequestUri_s |要求の URI |
| リソース |Key Vault の名前 |
| ResourceGroup |Key Vault のリソース グループ |
| ResourceId |Azure リソース マネージャー リソース ID。 Key Vault のログの場合は、常に Key Vault リソース ID となります。 |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResultSignature |HTTP の状態 |
| ResultType |REST API 要求の結果 |
| SubscriptionId |Key Vault を含んでいるサブスクリプションの Azure サブスクリプション ID |

## <a name="next-steps"></a>次のステップ
* [Log Analytics のログ検索機能](log-analytics-log-searches.md)を使用して、詳細な Azure Key Vault データを確認してください。

<!--HONumber=Oct16_HO2-->


