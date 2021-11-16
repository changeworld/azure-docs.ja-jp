---
title: Purview アカウントのプライベート エンドポイント構成のトラブルシューティング
description: この記事では、プライベート エンドポイント構成に関連する Purview アカウントに関する問題をトラブルシューティングする方法について説明します
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: 1557b229c4db50dca2115fc9c11123c76c5adc50
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851821"
---
# <a name="troubleshooting-private-endpoint-configuration-for-purview-accounts"></a>Purview アカウントのプライベート エンドポイント構成のトラブルシューティング

> [!IMPORTANT]
> **2021 年 9 月 27 日 15:30 UTC より前に** Purview アカウントの "_ポータル_" プライベート エンドポイントを作成した場合は、「[ポータル プライベート エンドポイント用に DNS を再構成する](./catalog-private-link.md#reconfigure-dns-for-portal-private-endpoints)」で詳しく説明されているように、必要なアクションを実行する必要があります。 **これらの操作は、2021 年 11 月 12 日より前に完了する必要があります。そうしない場合、既存のポータルのプライベート エンドポイントの機能が停止します**。

このガイドでは、Azure Purview 用のプライベート エンドポイントの使用に関連する既知の制限について要約し、最も一般的な関連する問題のトラブルシューティングを行うための手順と解決策の一覧を示します。 

## <a name="known-limitations"></a>既知の制限事項

- AWS ソースに対応するインジェスト プライベート エンドポイントは、現時点ではサポートされていません。
- セルフホステッド統合ランタイムを使用した Azure の複数ソースのスキャンはサポートされていません。
- Azure 統合ランタイムを使用して、プライベート エンドポイントの背後にあるデータ ソースをスキャンすることはサポートされていません。
- Azure portal を使用すると、前の手順で説明した Azure Purview ポータルのエクスペリエンスを介して、インジェスト プライベート エンドポイントを作成できます。 Private Link センターから作成することはできません。
- Azure プライベート DNS ゾーンがプライベート エンドポイントとは異なるサブスクリプションに配置されている場合に、既存の Azure DNS ゾーン内にインジェスト プライベート エンドポイント用の DNS A レコードを作成することは、Azure Purview ポータルのエクスペリエンスを介してサポートされません。 A レコードは、他のサブスクリプションの宛先 DNS ゾーンに手動で追加できます。 
- セルフホステッド統合ランタイムのマシンは、Azure Purview インジェスト プライベートエンド ポイントがデプロイされているのと同じ VNet にデプロイされている必要があります。
- 現在、パブリック アクセスをブロックするように構成されているプライベート エンドポイントがある Power BI テナントのスキャンはサポートされていません。
- Private Link サービスに関連する制限については、「[Azure Private Link の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)」を参照してください。

## <a name="recommended-troubleshooting-steps"></a>推奨されるトラブルシューティングの手順  

1. Purview アカウントのプライベート エンドポイントをデプロイしたら、Azure 環境をレビューして、プライベート エンドポイント リソースが正常にデプロイされていることを確認します。 シナリオに応じて、次の 1 つ以上の Azure プライベート エンドポイントを Azure サブスクリプションにデプロイする必要があります。

    |プライベート エンドポイント  |プライベート エンドポイントの割り当て先 | 例|
    |---------|---------|---------|
    |Account  |Azure Purview アカウント         |mypurview-private-account  |
    |ポータル     |Azure Purview アカウント         |mypurview-private-portal  |
    |データの取り込み     |マネージド ストレージ アカウント (BLOB)         |mypurview-ingestion-blob  |
    |データの取り込み     |マネージド ストレージ アカウント (キュー)         |mypurview-ingestion-queue  |
    |データの取り込み     |マネージド Event Hubs 名前空間         |mypurview-ingestion-namespace  |

2. ポータルのプライベート エンドポイントがデプロイされている場合は、アカウントのプライベート エンドポイントもデプロイしてください。

3. ポータルのプライベート エンドポイントがデプロイされていて、Azure Purview アカウントで公衆ネットワーク アクセスが拒否に設定されている場合は、内部ネットワークから [Azure Purview Studio](https://web.purview.azure.com/resource/) を起動していることを確認してください。
  <br>
    - 正しい名前解決を確認するには、**NSlookup.exe** コマンド ライン ツールを使用して `web.purview.azure.com` に問い合わせます。 結果として、ポータルのプライベート エンドポイントに属しているプライベート IP アドレスが返される必要があります。 
    - ネットワーク接続を確認するには、任意のネットワーク テスト ツールを使って、`web.purview.azure.com` エンドポイントのポート **443** への送信接続をテストします。 接続が成功する必要があります。    

3. Azure プライベート DNS ゾーンが使用されている場合は、必要な Azure DNS ゾーンがデプロイされていて、各プライベート エンドポイントの DNS (A) レコードがあることを確認します。

4. 管理マシンから Purview エンドポイントと Purview Web の URL へのネットワーク接続と名前解決をテストします。 アカウントとポータルのプライベート エンドポイントがデプロイされている場合は、プライベート IP アドレスを使用してエンドポイントが解決される必要があります。


    ```powershell
    Test-NetConnection -ComputerName web.purview.azure.com -Port 443
    ```

    プライベート IP アドレスを使用した正常な送信接続の例:

    ```
    ComputerName     : web.purview.azure.com
    RemoteAddress    : 10.9.1.7
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName purview-test01.purview.azure.com -Port 443
    ```

    プライベート IP アドレスを使用した正常な送信接続の例:

    ```
    ComputerName     : purview-test01.purview.azure.com
    RemoteAddress    : 10.9.1.8
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```
    
5. 2021 年 8 月 18 日以降に Azure Purview アカウントを作成した場合は必ず、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から最新バージョンのセルフホステッド統合ランタイムをダウンロードしてインストールしてください。
   
6. セルフホステッド統合ランタイムの VM から Purview エンドポイントへのネットワーク接続と名前解決をテストします。

7. セルフホステッド統合ランタイムから、Azure Purview 管理対象リソース (BLOB キューやイベント ハブなど) へのポート 443 とプライベート IP アドレスを使用したネットワーク接続と名前解決をテストします。 (マネージド ストレージ アカウントとイベント ハブ名前空間を、Azure Purview アカウントに割り当てられている対応する管理対象リソース名に置き換えます)。

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.blob.core.windows.net -Port 443
    ```
    プライベート IP アドレスを使用した、マネージド BLOB ストレージへの正常な送信接続の例:

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.6
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.queue.core.windows.net -Port 443
    ```
    プライベート IP アドレスを使用した、マネージド キュー ストレージへの正常な送信接続の例:

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.5
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `Atlas-1225cae9-d651-4039-86a0-b43231a17a4b`.servicebus.windows.net -Port 443
    ```
    プライベート IP アドレスを使用した、イベント ハブ名前空間への正常な送信接続の例:

    ```
    ComputerName     : Atlas-1225cae9-d651-4039-86a0-b43231a17a4b.servicebus.windows.net
    RemoteAddress    : 10.15.1.4
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

8. データソースが配置されているネットワークから、Purview エンドポイントと管理対象リソースのエンドポイントへのネットワーク接続と名前解決をテストします。

9.  データ ソースがオンプレミスのネットワークにある場合は、DNS フォワーダーの構成を確認します。 データ ソースが配置されているのと同じネットワーク内から、セルフホステッド統合ランタイム、Azure Purview エンドポイント、管理対象リソースへの名前解決をテストします。 DNS クエリから各エンドポイントの有効なプライベート IP アドレスが取得されることが想定されています。
    
    詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」の「[カスタム DNS サーバーのない仮想ネットワークのワークロード](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)」と「[DNS フォワーダーを使用しているオンプレミスのワークロード](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)」を参照してください。

10. 管理マシンとセルフホステッド統合ランタイムの VM がオンプレミス ネットワークにデプロイされていて、環境に DNS フォワーダーがセットアップされている場合は、環境の DNS とネットワークの設定を確認します。 

11. インジェスト プライベート エンドポイントが使用されている場合は、セルフホステッド統合ランタイムが Purview アカウント内に正常に登録されていて、セルフホステッド統合ランタイムの VM と [Purview Studio](https://web.purview.azure.com/resource/) の両方で実行中であることが示されていることを確認します。

## <a name="common-errors-and-messages"></a>一般的なエラーとメッセージ

### <a name="issue"></a>問題 
スキャンを実行すると、次のエラー メッセージを受け取ることがあります。

`Internal system error. Please contact support with correlationId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx System Error, contact support.`

### <a name="cause"></a>原因 
これは、セルフホステッド統合ランタイムが実行されている VM と Azure Purview の管理対象リソース ストレージ アカウントまたはイベント ハブ間の接続または名前解決に関連する問題を示している可能性があります。

### <a name="resolution"></a>解像度 
セルフホステッド統合ランタイムが実行されている VM 間で名前解決が行われるかどうかを検証します。


### <a name="issue"></a>問題 
新しいスキャンを実行すると、次のエラー メッセージを受け取ることがあります。

  `message: Unable to setup config overrides for this scan. Exception:'Type=Microsoft.WindowsAzure.Storage.StorageException,Message=The remote server returned an error: (404) Not Found.,Source=Microsoft.WindowsAzure.Storage,StackTrace= at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndExecuteAsync[T](IAsyncResult result)`

### <a name="cause"></a>原因 
これは、以前のバージョンのセルフホステッド統合ランタイムが実行されていることを示している場合があります。 セルフホステッド統合ランタイム バージョン 5.9.7885.3 以上を使用する必要があります。

### <a name="resolution"></a>解像度 
セルフホステッド統合ランタイムを 5.9.7885.3 にアップグレードします。


### <a name="issue"></a>問題 
プライベート エンドポイントのデプロイがある Azure Purview アカウントが、デプロイ中に Azure Policy 検証エラーで失敗しました。

### <a name="cause"></a>原因
このエラーは、Azure サブスクリプションに既存の Azure Policy の割り当てが存在し、必要な Azure リソースのデプロイを妨げている可能性があることを示しています。 

### <a name="resolution"></a>解像度 
既存の Azure Policy の割り当てを確認し、Azure サブスクリプションで次の Azure リソースのデプロイが許可されていることを確認してください。 
   
> [!NOTE]
> シナリオによっては、次の Azure リソースの種類を 1 つ以上デプロイする必要がある場合があります。 
>  -   Azure Purview (Microsoft.Purview/Accounts)
>  -   プライベート エンドポイント (Microsoft.Network/privateEndpoints)
>  -   プライベート DNS ゾーン (Microsoft.Network/privateDnsZones)
>  -   イベント ハブの名前空間 (Microsoft.EventHub/namespaces)
>  -   Storage アカウント (Microsoft.Storage/storageAccounts)


### <a name="issue"></a>問題
この Purview アカウントにアクセスする権限がありません。 この Purview アカウントは、プライベート エンドポイントの背後にあります。 このアカウントには、Purview アカウントのプライベート エンドポイント用に構成されている同じ仮想ネットワーク (VNet) 内のクライアントからアクセスしてください。

### <a name="cause"></a>原因 
ユーザーがパブリック エンドポイントから Azure Purview に接続しようとしているか、**公衆ネットワーク アクセス** が **拒否** に設定されている Azure Purview パブリック エンドポイントを使用しています。

### <a name="resolution"></a>解像度
この場合、Azure Purview Studio を開くには、Azure Purview ポータルのプライベート エンドポイントと同じ仮想ネットワークにデプロイされているマシンを使用するか、ハイブリッド接続が許可されている CorpNet に接続されている VM を使用します。

### <a name="issue"></a>問題
セルフホステッド統合ランタイムを使用して SQL サーバーをスキャンすると、次のエラー メッセージが表示される場合があります。

  `Message=This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms`

### <a name="cause"></a>原因 
セルフホステッド統合ランタイム マシンで FIPS モードが有効になっています。
Federal Information Processing Standards (FIPS) では、使用が許可される暗号化アルゴリズムの特定のセットを定義します。 マシンで FIPS モードが有効になっている場合、一部のシナリオでは、起動したプロセスが依存する一部の暗号化クラスがブロックされます。

### <a name="resolution"></a>解像度
セルフホステッド統合サーバーで FIPS モードを無効にします。

## <a name="next-steps"></a>次のステップ

お客様の問題がこの記事に掲載されていない場合、または解決できない場合は、次のいずれかのチャネルにアクセスしてサポートを受けてください。

- [Microsoft Q&A](/answers/topics/azure-purview.html) を通じて、専門家からの回答を得る。
- [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる。 この Twitter 上の Microsoft Azure 公式リソースは、Azure コミュニティを適切な回答、サポート、エキスパートに結び付けることによって、カスタマー エクスペリエンスを向上させます。
- それでもヘルプが必要な場合は、[Azure サポート サイト](https://azure.microsoft.com/support/options/)にアクセスし、 **[サポート リクエストの送信]** を選択してください。
