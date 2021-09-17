---
title: Azure Purview プライベート エンドポイントに関してよく寄せられる質問 (FAQ)
description: この記事では、Azure Purview プライベート エンドポイントについてよく寄せられる質問に回答します。
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: 3d7a37565b46137a95f0dd30a3e29b750bed3afe
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515566"
---
# <a name="faq-about-azure-purview-private-endpoints"></a>Azure Purview プライベート エンドポイントに関する FAQ

この記事では、[Azure Private Link](../private-link/private-link-overview.md) を使用した Azure Purview のネットワーク構成について、お客様やフィールド チームからよく寄せられる質問に回答します。 これは、Azure Purview のファイアウォールの設定、プライベート エンドポイント、DNS 構成、関連する構成に関する質問を明確にすることを目的としています。

Private Link を使用して Azure Purview を設定するには、[Azure Purview アカウントにプライベート エンドポイントを使用する](./catalog-private-link.md)方法に関する記事を参照してください。

## <a name="common-questions"></a>一般的な質問

次の一般的な質問に対する回答を確認してください。

### <a name="whats-the-purpose-of-deploying-the-azure-purview-account-private-endpoint"></a>Azure Purview のアカウント プライベート エンドポイントをデプロイする目的は何ですか?

Azure Purview のアカウント プライベート エンドポイントは、仮想ネットワーク内から発信されたクライアント呼び出しのみがアカウントへのアクセスを許可されるシナリオを可能にすることで、セキュリティを強化するために使用されます。 このプライベート エンドポイントは、ポータルのプライベート エンドポイントの前提条件でもあります。

### <a name="whats-the-purpose-of-deploying-the-azure-purview-portal-private-endpoint"></a>Azure Purview のポータル プライベート エンドポイントをデプロイする目的は何ですか?

Azure Purview のポータル プライベート エンドポイントは、Azure Purview Studio へのプライベート接続を提供します。

### <a name="whats-the-purpose-of-deploying-the-azure-purview-ingestion-private-endpoints"></a>Azure Purview のインジェスト プライベート エンドポイントをデプロイする目的は何ですか?

Azure Purview では、インジェスト プライベート エンドポイントを使用して、Azure またはオンプレミス環境のデータ ソースをスキャンできます。 インジェスト プライベート エンドポイントが作成されると、3 つの追加のプライベート エンドポイント リソースがデプロイされ、Azure Purview 管理対象リソースにリンクされます。

- **BLOB** は Azure Purview マネージド ストレージ アカウントにリンクされます。
- **キュー** は Azure Purview マネージド ストレージ アカウントにリンクされます。
- **名前空間** は Azure Purview マネージド イベント ハブ名前空間にリンクされます。

### <a name="can-i-scan-data-through-a-public-endpoint-if-a-private-endpoint-is-enabled-on-my-azure-purview-account"></a>Azure Purview アカウントでプライベート エンドポイントが有効になっている場合、パブリック エンドポイント経由でデータをスキャンすることはできますか?

はい。 Azure Purview がプライベート エンドポイントを使用するように構成されている場合、プライベート エンドポイントを介して接続されていないデータ ソースは、パブリック エンドポイントを使用してスキャンできます。

### <a name="can-i-scan-data-through-a-service-endpoint-if-a-private-endpoint-is-enabled"></a>プライベート エンドポイントが有効になっている場合、サービス エンドポイントを介してデータをスキャンすることはできますか?

はい。 Azure Purview がプライベート エンドポイントを使用するように構成されている場合、プライベート エンドポイントを介して接続されていないデータ ソースは、サービス エンドポイントを使用してスキャンできます。

Azure のデータ ソース リソースのサービス エンドポイント構成内のリソースにアクセスするために、 **[信頼された Microsoft サービスを許可する]** を有効にしてください。 たとえば、ファイアウォールと仮想ネットワークの設定が **[選択されたネットワーク]** になっている Azure Blob Storage をスキャンする場合、例外として **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** チェックボックスがオンになっていることを確認します。

### <a name="can-i-access-azure-purview-studio-from-a-public-network-if-public-network-access-is-set-to-deny-in-azure-purview-account-networking"></a>Azure Purview のアカウント ネットワーキングで公衆ネットワーク アクセスが [拒否] に設定されている場合、公衆ネットワークから Azure Purview Studio にアクセスできますか?

いいえ。 **[公衆ネットワーク アクセス]** が **[拒否]** に設定されているパブリック エンドポイントから Azure Purview に接続すると、次のようなエラー メッセージが表示されます。

「この Purview アカウントにアクセスする権限がありません。 この Purview アカウントは、プライベート エンドポイントの背後にあります。 このアカウントには、Purview アカウントのプライベート エンドポイント用に構成されている同じ仮想ネットワーク (VNet) 内のクライアントからアクセスしてください」。

この場合、Azure Purview Studio を開くには、Azure Purview ポータルのプライベート エンドポイントと同じ仮想ネットワークにデプロイされているマシンを使用するか、ハイブリッド接続が許可されている CorpNet に接続されている VM を使用します。

### <a name="is-it-possible-to-restrict-access-to-the-azure-purview-managed-storage-account-and-event-hub-namespace-for-private-endpoint-ingestion-only-but-keep-portal-access-enabled-for-users-across-the-web"></a>Azure Purview マネージド ストレージ アカウントと Event Hub 名前空間へのアクセスを制限しつつ (プライベート エンドポイント インジェストのみ)、Web 経由のユーザーによるポータルへのアクセスを有効にしておくことは可能ですか?

いいえ。 **[公衆ネットワーク アクセス]** を **[拒否]** に設定すると、Azure Purview マネージド ストレージ アカウントと Event Hub 名前空間へのアクセスは、自動的にプライベート エンドポイント インジェストのみに設定されます。 **[公衆ネットワーク アクセス]** を **[許可]** に設定すると、Azure Purview マネージド ストレージ アカウントと Event Hub 名前空間へのアクセスは、自動的に **[すべてのネットワーク]** に設定されます。 マネージド ストレージ アカウントや Event Hub 名前空間のプライベート エンドポイント インジェストを手動で変更することはできません。

### <a name="if-public-network-access-is-set-to-allow-does-it-mean-the-managed-storage-account-and-event-hub-namespace-can-be-publicly-accessible"></a>[公衆ネットワーク アクセス] を [許可] に設定した場合、これはマネージド ストレージ アカウントと Event Hub 名前空間にパブリックにアクセスできるという意味ですか?

いいえ。 保護されたリソースとして、Azure Purview マネージド ストレージ アカウントと Event Hub 名前空間へのアクセスは、Azure Purview のみに制限されます。 これらのリソースは、すべてのプリンシパルへの割り当てが拒否された状態でデプロイされ、アプリケーション、ユーザー、グループがアクセスできないようになっています。

Azure の拒否の割り当ての詳細については、「[Azure 拒否割り当てについて](../role-based-access-control/deny-assignments.md)」を参照してください。

### <a name="what-are-the-supported-authentication-types-when-you-use-a-private-endpoint"></a>プライベート エンドポイントを使用する場合、サポートされている認証の種類は何ですか?

Azure Key Vault またはサービス プリンシパルです。

### <a name="what-private-dns-zones-are-required-for-azure-purview-for-a-private-endpoint"></a>プライベート エンドポイントの Azure Purview に必要なプライベート DNS ゾーンは何ですか?

Azure Purview "_アカウント_" および "_ポータル_" プライベート エンドポイントの場合:

- `privatelink.purview.azure.com`

Azure Purview "_インジェスト_" プライベート エンドポイントの場合:

- `privatelink.blob.core.windows.net`
- `privatelink.queue.core.windows.net`
- `privatelink.servicebus.windows.net`

### <a name="do-i-have-to-use-a-dedicated-virtual-network-and-dedicated-subnet-when-i-deploy-azure-purview-private-endpoints"></a>Azure Purview プライベート エンドポイントをデプロイするときに、専用の仮想ネットワークおよび専用サブネットを使用する必要はありますか?

いいえ。 ただし、プライベート エンドポイントをデプロイする前に、`PrivateEndpointNetworkPolicies` を移行先サブネットで無効にする必要があります。 データ ソースをクロス プレミスでスキャンする予定の場合、VNet ピアリングを介してデータ ソースの仮想ネットワークにネットワーク接続され、オンプレミス ネットワークにアクセスする仮想ネットワークに Azure Purview をデプロイします。

「[プライベート エンドポイントのネットワーク ポリシーを無効にする](../private-link/disable-private-endpoint-network-policy.md)」を参照してください。

### <a name="can-i-deploy-azure-purview-private-endpoints-and-use-existing-private-dns-zones-in-my-subscription-to-register-the-a-records"></a>Azure Purview プライベート エンドポイントをデプロイし、サブスクリプション内の既存のプライベート DNS ゾーンを使用して A レコードを登録することはできますか?

はい。 プライベート エンドポイントの DNS ゾーンは、Azure Purview とすべてのデータ ソース レコードに必要なすべての内部 DNS ゾーンのハブまたはデータ管理サブスクリプションで一元化できます。 Azure Purview がプライベート エンドポイントの内部 IP アドレスを使用してデータ ソースを解決できるこの方法をお勧めします。

また、既存のプライベート DNS ゾーンに仮想ネットワークの[仮想ネットワーク リンク](../dns/private-dns-virtual-network-links.md)を設定する必要があります。

### <a name="can-i-use-azure-integration-runtime-to-scan-data-sources-through-a-private-endpoint"></a>Azure 統合ランタイムを使用して、プライベート エンドポイント経由でデータ ソースをスキャンできますか?

いいえ。 プライベート接続を使用してデータをスキャンするには、セルフホステッド統合ランタイムをデプロイして登録する必要があります。 Azure Key Vault またはサービス プリンシパルは、データ ソースへの認証方法として使用する必要があります。

### <a name="what-are-the-outbound-ports-and-firewall-requirements-for-virtual-machines-with-self-hosted-integration-runtime-for-azure-purview-when-you-use-a-private-endpoint"></a>プライベート エンドポイントを使用する場合、Azure Purview のセルフホステッド統合ランタイムを使用した仮想マシンの送信ポートとファイアウォールの要件はどのようなものですか?

セルフホステッド統合ランタイムがデプロイされている VM では、ポート 443 を通じて Azure エンドポイントおよび Azure Purview プライベート IP アドレスへの送信アクセスが可能である必要があります。

### <a name="do-i-need-to-enable-outbound-internet-access-from-the-virtual-machine-running-self-hosted-integration-runtime-if-a-private-endpoint-is-enabled"></a>プライベート エンドポイントが有効になっている場合、セルフホステッド統合ランタイムを実行している仮想マシンからの送信インターネット アクセスを有効にする必要はありますか?

いいえ。 ただし、セルフホステッド統合ランタイムを実行している仮想マシンは、ポート 443 を使用して内部 IP アドレスを介して Azure Purview のインスタンスに接続できることが期待されます。 名前解決や接続テストには nslookup.exe や Test-NetConnection などの一般的なトラブルシューティング ツールを使用します。

### <a name="why-do-i-receive-the-following-error-message-when-i-try-to-launch-azure-purview-studio-from-my-machine"></a>マシンから Azure Purview スタジオを起動しようとすると、次のエラー メッセージが表示されるのはなぜですか。

「この Purview アカウントは、プライベート エンドポイントの背後にあります。 このアカウントには、Purview アカウントのプライベート エンドポイント用に構成されている同じ仮想ネットワーク (VNet) 内のクライアントからアクセスしてください」。

Azure Purview アカウントがプライベート リンクを使用してデプロイされており、Azure Purview アカウントでパブリック アクセスが無効になっている可能性があります。 そのため、Azure Purview への内部ネットワーク接続がある仮想マシンから Azure Purview Studio を参照する必要があります。

ハイブリッド ネットワークの背後にある VM から接続している場合、または仮想マシンに接続されているジャンプ マシンを使用している場合、名前解決と接続テストのための一般的なトラブルシューティング ツール (nslookup.exe、Test-NetConnection など) を使用します。

1. Azure Purview アカウントのプライベート IP アドレスを使用して、次のアドレスを解決できるかどうかを検証します。

   - `Web.Purview.Azure.com`
   - `<YourPurviewAccountName>.Purview.Azure.com`

1. 次の PowerShell コマンドを使用して、Azure Purview アカウントへのネットワーク接続を確認します。

   ```powershell
   Test-NetConnection -ComputerName <YourPurviewAccountName>.Purview.Azure.com -Port 443
   ```

1. 独自の DNS 解決インフラストラクチャを使用している場合は、クロスプレミス DNS 構成を確認します。

プライベート エンドポイントの DNS 設定の詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」を参照してください。

## <a name="next-steps"></a>次の手順

Private Link を使用して Azure Purview を設定するには、[Azure Purview アカウントにプライベート エンドポイントを使用する](./catalog-private-link.md)方法に関する記事を参照してください。
