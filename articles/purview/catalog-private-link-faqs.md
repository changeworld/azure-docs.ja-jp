---
title: Azure Purview プライベート エンドポイントに関してよく寄せられる質問 (FAQ)
description: この記事では、Azure Purview プライベート エンドポイントについてよく寄せられる質問に回答します
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: 13d9f65a812dcf9cfc45b84758f351e301827173
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104928"
---
# <a name="frequently-asked-questions-faq-about-azure-purview-private-endpoints"></a>Azure Purview プライベート エンドポイントに関してよく寄せられる質問 (FAQ)

この記事では、[Azure Private Link](../private-link/private-link-overview.md) を使用した Azure Purview のネットワーク構成について、お客様やフィールド チームからよく寄せられる質問に回答します。 これは、Azure Purview のファイアウォールの設定、プライベート エンドポイント、DNS 構成、および関連する構成に関する質問を明確にすることを目的としています。

Private Link を使用して Azure Purview を設定するには、[Purview アカウントにプライベート エンドポイントを使用する](./catalog-private-link.md)方法に関する記事を参照してください。

## <a name="common-questions"></a>一般的な質問

### <a name="what-is-purpose-of-deploying-azure-purview-account-private-endpoint"></a>Azure Purview の "アカウント" プライベート エンドポイントをデプロイする目的は何ですか?

Azure Purview "_アカウント_" プライベート エンドポイントは、V-net 内から発信されたクライアント呼び出しのみがアカウントへのアクセスを許可されるシナリオを可能にすることで、セキュリティを強化するために使用されます。 このプライベート エンドポイントは、ポータルのプライベート エンドポイントの前提条件でもあります。
    
### <a name="what-is-purpose-of-deploying--azure-purview-portal-private-endpoint"></a>Azure Purview の "ポータル" プライベート エンドポイントをデプロイする目的は何ですか?
Azure Purview "_ポータル_" プライベート エンドポイントは、Azure Purview Studio へのプライベート接続を提供することを目的としています。
    
### <a name="what-is-purpose-of-deploying-azure-purview-ingestion-private-endpoints"></a>Azure Purview の "インジェスト" プライベート エンドポイントをデプロイする目的は何ですか?

Azure Purview では、インジェスト プライベート エンドポイントを使用して、Azure またはオンプレミス環境のデータ ソースをスキャンすることができます。 インジェスト プライベート エンドポイントが作成されると、3 つの追加のプライベート エンドポイント リソースがデプロイされ、Azure Purview 管理対象リソースにリンクされます。
- Azure Purview マネージド ストレージ アカウントにリンクされた _BLOB_ 
- Azure Purview マネージド ストレージ アカウントにリンクされた _キュー_  
- Azure Purview マネージド イベント ハブ名前空間にリンクされた _名前空間_
     
### <a name="can-i-scan-data-through-public-endpoint-if-private-endpoint-is-enabled-on-my-azure-purview-account"></a>Azure Purview アカウントでプライベート エンドポイントが有効になっている場合、パブリック エンドポイント経由でデータをスキャンすることはできますか?

はい。Azure Purview がプライベート エンドポイントを使用するように構成されている場合、プライベート エンドポイントを介して接続されていないデータ ソースは、パブリック エンドポイントを使用してスキャンできます。
    
### <a name="can-i-scan-data-through-service-endpoint-if-private-endpoint-is-enabled"></a>プライベート エンドポイントが有効になっている場合、サービス エンドポイントを介してデータをスキャンすることはできますか?

はい。Azure Purview がプライベート エンドポイントを使用するように構成されている場合、プライベート エンドポイントを介して接続されていないデータ ソースは、サービス エンドポイントを使用してスキャンできます。
Azure のデータ ソース リソースのサービス エンドポイント構成内のリソースにアクセスするために、[信頼された Microsoft サービスを許可する] を有効にしてください。 たとえば、ファイアウォールと仮想ネットワークの設定が _[選択されたネットワーク]_ になっている Azure Blob Storage をスキャンする場合は、例外として _[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]_ がチェックされていることを確認する必要があります。 
    
###  <a name="can-i-access-azure-purview-studio-from-public-network-if-public-network-access-is-set-to-deny-in-azure-purview-account-networking"></a>Azure Purview のアカウント ネットワーキングで公衆ネットワーク アクセスが [拒否] に設定されている場合、公衆ネットワークから Azure Purview Studio にアクセスできますか?

いいえ。 _[公衆ネットワーク アクセス]_ が _[拒否]_ に設定されているパブリック エンドポイントから Azure Purview に接続すると、次のようなエラー メッセージが表示されます。

_この Purview アカウントにアクセスする権限がありません_
_この Purview アカウントは、プライベート エンドポイントの背後にあります。このアカウントには、Purview アカウントのプライベート エンドポイント用に構成されている同じ仮想ネットワーク (VNet) 内のクライアントからアクセスしてください。_

この場合、Azure Purview Studio を開くには、Azure Purview ポータルのプライベート エンドポイントと同じ仮想ネットワークにデプロイされているマシンを使用するか、ハイブリッド接続が許可されている CorpNet に接続されている VM を使用する必要があります。

### <a name="is-it-possible-to-restrict-access-to-azure-purview-managed-storage-account-and-event-hub-namespace-for-private-endpoint-ingestion-only-but-keep-portal-access-enabled-for-end-users-across-the-web"></a>Azure Purview マネージド ストレージ アカウントと Event Hub 名前空間へのアクセスを制限しつつ (プライベート エンドポイント インジェストのみ)、Web 経由のエンド ユーザーによるポータルへのアクセスを有効にしておくことは可能ですか?

いいえ。 _[公衆ネットワーク アクセス]_ を _[拒否]_ に設定すると、Azure Purview マネージド ストレージ アカウントと Event Hub 名前空間へのアクセスは、自動的にプライベート エンドポイント インジェストのみに設定されます。 _[公衆ネットワーク アクセス]_ を _[許可]_ に設定すると、Azure Purview マネージド ストレージ アカウントと Event Hub 名前空間へのアクセスは、自動的に _[すべてのネットワーク]_ に設定されます。 マネージド ストレージ アカウントや Event Hub 名前空間のプライベート エンドポイント インジェストを手動で変更することはできません。
    
### <a name="if-public-network-access-is-set-to-allow-does-it-mean-the-managed-storage-account-and-event-hub-can-be-publicly-accessible"></a>[公衆ネットワーク アクセス] を [許可] に設定した場合、これはマネージド ストレージ アカウントと Event Hub にパブリックにアクセスできるという意味ですか?

いいえ。 保護されたリソースとして、Azure Purview マネージド ストレージ アカウントと Event Hub 名前空間へのアクセスは、Azure Purview にのみ制限されます。 これらのリソースは、すべてのプリンシパルへの割り当てが拒否された状態でデプロイされ、アプリケーション、ユーザー、グループがアクセスできないようになっています。

Azure の拒否の割り当ての詳細については、「[Azure 拒否割り当てについて](../role-based-access-control/deny-assignments.md)」を参照してください。
    
### <a name="what-are-the-supported-authentication-type-when-using-private-endpoint"></a>プライベート エンドポイントを使用する場合、サポートされている認証の種類は何ですか?

Azure Key Vault またはサービス プリンシパルです。
  
### <a name="what-are-private-dns-zones-required-for-azure-purview-for-private-endpoint"></a>プライベート エンドポイントの Azure Purview に必要なプライベート DNS ゾーンは何ですか?

**Azure Purview リソースの場合:** 
- `privatelink.purview.azure.com`

**Azure Purview 管理対象リソースの場合:**
- `privatelink.blob.core.windows.net`
- `privatelink.queue.core.windows.net`
- `privatelink.servicebus.windows.net`
    
### <a name="do-i-have-to-use-a-dedicated-virtual-network-and-dedicated-subnet-when-deploying-azure-purview-private-endpoints"></a>Azure Purview プライベート エンドポイントをデプロイするときに、専用の Virtual Network および専用サブネットを使用する必要がありますか?

いいえ。ただし、プライベート エンドポイントをデプロイする前に、宛先サブネットで _PrivateEndpointNetworkPolicies_ を無効にする必要があります。
データ ソースをクロス プレミスでスキャンする予定の場合は、VNet ピアリングを介してデータ ソース VNet にネットワーク接続され、オンプレミス ネットワークにアクセスする Virtual Network に Azure Purview をデプロイします。
    
「[プライベート エンドポイントのネットワーク ポリシーを無効にする](../private-link/disable-private-endpoint-network-policy.md)」を参照してください。

### <a name="can-i-deploy-azure-purview-private-endpoints-and-use-existing-private-dns-zones-in-my-subscription-to-register-the-a-records"></a>Azure Purview プライベート エンドポイントをデプロイし、サブスクリプション内の既存のプライベート DNS ゾーンを使用して A レコードを登録することはできますか?

はい。 プライベート エンドポイントの DNS ゾーンは、Azure Purview とすべてのデータ ソース レコードに必要なすべての内部 DNS ゾーンのハブまたはデータ管理サブスクリプションで一元化できます。 これは、Azure Purview がプライベート エンドポイントの内部 IP アドレスを使用してデータ ソースを解決するために推奨される方法です。

また、既存のプライベート DNS ゾーンに VNet の[仮想ネットワーク リンク](../dns/private-dns-virtual-network-links.md)を設定する必要があります。
    
### <a name="can-i-use-azure-integration-runtime-to-scan-data-sources-through-private-endpoint"></a>Azure 統合ランタイムを使用して、プライベート エンドポイント経由でデータ ソースをスキャンできますか? 

いいえ。 プライベート接続を使用してデータをスキャンするには、セルフホステッド統合ランタイムをデプロイして登録する必要があります。 Azure Key Vault またはサービス プリンシパルは、データ ソースへの認証方法として使用する必要があります。
    
### <a name="what-are-the-outbound-ports-and-firewall-requirements-for-virtual-machines-with-self-hosted-integration-runtime-for-azure-purview-when-using-private-endpoint"></a>プライベート エンドポイントを使用する場合、Azure Purview のセルフホステッド統合ランタイムを使用した仮想マシンの送信ポートとファイアウォールの要件はどのようなものですか?

セルフホステッド統合ランタイムがデプロイされている VM では、ポート 443 を通じて Azure エンドポイントおよび Azure Purview プライベート IP アドレスへのア送信アクセスが可能である必要があります。 
    
### <a name="do-i-need-to-enable-outbound-internet-access-from-the-virtual-machine-running-self-hosted-integration-runtime-if-private-endpoint-is-enabled"></a>プライベート エンドポイントが有効になっている場合、セルフホステッド統合ランタイムを実行している仮想マシンからの送信インターネット アクセスを有効にする必要はありますか?

いいえ。 ただし、セルフホステッド統合ランタイムを実行している仮想マシンは、ポート 443 を使用して内部 IP アドレスを介して Azure Purview に接続できることが期待されます。 名前解決には一般的なツールを使用し、トラブルシューティングには nslookup.exe や Test-NetConnection などの接続テストを使用します。
    
### <a name="why-do-i-receive-the-following-error-message-when-i-try-to-launch-azure-purview-studio-from-my-machine"></a>マシンから Azure Purview スタジオを起動しようとすると、次のエラー メッセージが表示されるのはなぜですか。

_この Purview アカウントは、プライベート エンドポイントの背後にあります。このアカウントには、Purview アカウントのプライベート エンドポイント用に構成されている同じ仮想ネットワーク (VNet) 内のクライアントからアクセスしてください。_

Azure Purview アカウントが Azure Private Link を使用してデプロイされており、Azure Purview アカウントでパブリック アクセスが無効になっている可能性があるため、Azure Purview への内部ネットワーク接続がある仮想マシンから Azure Purview スタジオを参照する必要があります。

ハイブリッド ネットワークの内側にある VM から接続している場合、または VNet に接続されているジャンプ マシンを使用している場合は、名前解決と接続テストのための一般的なトラブルシューティング ツール (nslookup.exe、Test-NetConnection など) を使用します。

1. Azure Purview アカウントのプライベート IP アドレスを使用して、次のアドレスを解決できるかどうかを検証します。

   - `Web.Purview.Azure.com`
   - `<YourPurviewAccountName>.Purview.Azure.com`

2. 次の PowerShell コマンドを使用して、Azure Purview アカウントへのネットワーク接続を確認します。

   ```powershell
   Test-NetConnection -ComputerName <YourPurviewAccountName>.Purview.Azure.com -Port 443
   ```

3. 独自の DNS 解決インフラストラクチャを使用している場合は、クロスプレミス DNS 構成を確認します。 

   プライベート エンドポイントの DNS 設定の詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」を参照してください。

## <a name="next-steps"></a>次の手順

Private Link を使用して Azure Purview を設定するには、[Purview アカウントにプライベート エンドポイントを使用する](./catalog-private-link.md)方法に関する記事を参照してください。
