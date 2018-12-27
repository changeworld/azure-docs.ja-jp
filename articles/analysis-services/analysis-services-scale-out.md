---
title: Azure Analysis Services のスケールアウト | Microsoft Docs
description: Azure Analysis Services サーバーをスケールアウトによってレプリケートします
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/13/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8cfbc72e239a7a5b38cee6752803e79735e2adc9
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321276"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services のスケールアウト

スケールアウトによって、クエリ プール内の複数の*クエリ レプリカ*間でクライアント クエリを分散して、負荷の高いクエリのワークロードを実行している間の応答時間を短縮できます。 クエリ プールから処理を分離して、クライアント クエリが処理操作の悪影響を受けないようにすることもできます。 スケールアウトを構成するには Azure Portal から行うか、または Analysis Services の REST API を使用します。

## <a name="how-it-works"></a>動作のしくみ

一般的なサーバーのデプロイでは、1 つのサーバーで、処理サーバーとクエリ サーバーの両方の役割を果たします。 サーバー上のモデルに対するクライアント クエリの数が、加入しているサーバー プランのクエリ プロセッシング ユニット (QPU) を超過した場合、またはモデル処理が負荷の高いクエリのワークロードと同時に発生した場合、パフォーマンスが低下することがあります。 

スケールアウトでは、最大 7 つの追加のクエリ レプリカ リソース (サーバーを含めて合計 8 つ) を含むクエリ プールを作成できます。 重要な場面では、QPU の需要を満たせるようにクエリ レプリカの数をスケーリングできます。また、処理サーバーはいつでもクエリ プールから分離できます。 すべてのクエリ レプリカは、サーバーと同じリージョンに作成されます。

クエリ プール内にあるクエリ レプリカの数に関わらず、ワークロードの処理はクエリ レプリカ間で分散されません。 単一のサーバーが処理サーバーとして機能します。 クエリ レプリカは、クエリ プール内の各クエリ レプリカ間で同期されているモデルに対するクエリのみを処理します。 

スケールアウトすると、新しいクエリ レプリカがクエリ プールに増分的に追加されます。 新しいクエリ レプリカ リソースがクエリ プールに追加されるまで最大 5 分かかることがあります。 すべての新しいクエリ レプリカが稼働すると、新しいクライアント接続はすべてのクエリ プール リソースに負荷分散されます。 既存のクライアント接続は、現在の接続先のリソースから変更されることはありません。  スケールインすると、クエリ プールから削除されるクエリ プール リソースへの既存のクライアント接続が終了します。 これらは、スケールイン操作が完了した時点で、残りのクエリ プール リソースに再接続されます。これには最大 5 分かかる場合があります。

モデルを処理するときは、処理操作の完了後に、処理サーバーとクエリ レプリカ間で同期を実行する必要があります。 処理操作を自動化する場合は、処理操作が問題なく完了した際に同期操作が行われるよう構成することが重要です。 同期は、ポータルで手動で実行するか、PowerShell または REST API を使用して実行することができます。 

### <a name="separate-processing-from-query-pool"></a>クエリ プールから分離した処理

処理操作とクエリ操作の両方のパフォーマンスを最大限にするために、処理サーバーをクエリ プールから分離することができます。 分離すると、既存および新規のクライアント接続は、クエリ プール内のクエリ レプリカにのみ割り当てられます。 処理操作にそれほど時間がかからない場合は、、処理操作と同期操作を実行している間だけ処理サーバーをクエリ プールから分離し、その後、クエリ プールに戻すこともできます。 

> [!NOTE]
> スケールアウトは Standard 価格レベルのサーバーで使用できます。 各クエリ レプリカは、お使いのサーバーと同じ料金で課金されます。

> [!NOTE]
> スケールアウトでは、サーバーで使用可能なメモリの量は増えません。 メモリを増やすには、プランをアップグレードする必要があります。

## <a name="region-limits"></a>リージョンの制限

構成できるクエリ レプリカ数は、サーバーが存在しているリージョンによって制限されます。 詳細については、「[リージョンごとの可用性](analysis-services-overview.md#availability-by-region)」を参照してください。

## <a name="monitor-qpu-usage"></a>QPU の使用状況を監視する

 お使いのサーバーでスケールアウトが必要かどうかを判断するには、Azure Portal でメトリックを使用して監視します。 QPU が頻繁に上限に達するようであれば、モデルに対するクエリの数が、お使いのプランに設定されている QPU の制限を超過していることになります。 クエリ スレッド プール キュー内のキュー数が、利用可能な QPU を超過した場合も、クエリ プールのジョブ キュー長のメトリックは増加します。 詳細については、[サーバー メトリックの監視](analysis-services-monitor.md)に関する記事をご覧ください。

## <a name="configure-scale-out"></a>スケールアウトを構成する

### <a name="in-azure-portal"></a>Azure Portal

1. ポータルで、**[スケールアウト]** をクリックします。スライダーを使用してクエリ レプリカ サーバーの数を選択します。 選択したレプリカの数は既存のサーバーの数に追加されます。

2. **[処理中のサーバーと照会中のプールを分けてください]** で、[はい] を選択してクエリ サーバーから処理中のサーバーを除外します。 既定の接続文字列 (:rw なし) を利用するクライアント接続は、クエリ プールのレプリカにリダイレクトされます。 

   ![スケールアウト スライダー](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. **[保存]** をクリックして新しいクエリ レプリカ サーバーをプロビジョニングします。 

プライマリ サーバー上の表形式モデルはレプリカ サーバーと同期されます。 同期が完了すると、クエリ プールがレプリカ サーバー間で受信クエリの分散を開始します。 

## <a name="synchronization"></a>同期 

新しいクエリ レプリカをプロビジョニングするときに、Azure Analysis Services はすべてのレプリカ間でモデルを自動的にレプリケートします。 ポータルまたは REST API を使用して、手動で同期を行うこともできます。 モデルを処理する際は、クエリ レプリカ全体で更新が同期されるよう、同期を実行してください。

### <a name="in-azure-portal"></a>Azure Portal

**[概要]** > 対象のモデル >**[Synchronize model]\(モデルの同期\)** の順に選択します。

![スケールアウト スライダー](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API
**sync** 操作を使用します。

#### <a name="synchronize-a-model"></a>モデルを同期する   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>同期状態を取得する  
`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
PowerShell を使用する前に、[最新の AzureRM モジュールをインストールまたは更新します](https://github.com/Azure/azure-powershell/releases)。 

クエリ レプリカの数を設定するには、[Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)を使用します。 省略可能な `-ReadonlyReplicaCount` パラメーターを指定します。

同期を実行するには、[Sync-AzureAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance) を使用します。

## <a name="connections"></a>Connections

サーバーの [概要] ページに、サーバー名が 2 つ表示されます。 サーバーのスケールアウトをまだ構成していない場合は、両方のサーバー名は同じものとして機能します。 サーバーのスケールアウトを構成したら、接続の種類に応じて適切なサーバー名を指定する必要があります。 

Power BI Desktop、Excel、カスタム アプリなどのエンドユーザー クライアントの接続については、**[サーバー名]** を使用します。 

SSMS、SSDT、および PowerShell、Azure 関数アプリ、AMO の接続文字列については、**[管理サーバー名]** を使用します。 管理サーバー名は特殊な `:rw` (読み取り/書き込み) 修飾子を含みます。 すべての処理操作は管理サーバーで発生します。

![サーバー名](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>トラブルシューティング

**問題:** ユーザーに "**Cannot find server '\<Name of the server>' instance in connection mode 'ReadOnly'**" (接続モード 'ReadOnly' でサーバー '<サーバーの名前>' インスタンスが見つかりません) というエラーが表示されます。

**解決方法:** **[処理中のサーバーと照会中のプールを分けてください]** オプションが選択されているとき、既定の接続文字列 (:rw なし) を利用するクライアント接続は、クエリ プールのレプリカにリダイレクトされます。 同期が完了していないため、クエリ プールのレプリカがオンラインになっていない場合、リダイレクトのクライアント接続は失敗することがあります。 接続の失敗を防ぐには、スケールアウト/同期操作が完了するまで、処理中のサーバーと照会中のプールを分けないことを選択してください。 メモリと QPU のメトリックを使用し、同期の状態を監視できます。

## <a name="related-information"></a>関連情報

[サーバー メトリックの監視](analysis-services-monitor.md)   
[Azure Analysis Services を管理する](analysis-services-manage.md) 

