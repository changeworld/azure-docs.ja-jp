---
title: ネットワーク接続に関する問題のトラブルシューティング | Microsoft Docs
description: プライベート エンドポイントで Azure Migrate を使用する際の一般的なエラーに対するトラブルシューティングのヒントを提供します。
author: SGSneha
ms.author: v-ssudhir
ms.manager: deseelam
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: 9e987b4db88379e0dfe40b8839b073b893811fb4
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110547790"
---
# <a name="troubleshoot-network-connectivity"></a>ネットワーク接続のトラブルシューティング
このアーティクルは、プライベート エンドポイントで Azure Migrate を使用する際のネットワーク接続のトラブルシューティングを行う際に役立ちます。

## <a name="validate-private-endpoints-configuration"></a>プライベート エンドポイントの構成を確認する

プライベート エンドポイントが承認済みの状態であることを確認します。  

1. **Azure Migrate** にアクセスします。 **[Discovery and Assessment (発見と評価)]** と **[Server Migration properties (サーバー移行のプロパティ)]** ページ。

2. プロパティ ページには、Azure Migrate によって自動的に作成されたプライベート リンク FQDN とプライベート エンドポイントが一覧表示されます。  

3. 診断したいプライベート エンドポイントを選択します。  
   a. 接続状態が [承認済み] になっていることを確認します。           
   b. 接続が保留中の状態である場合、接続を承認する必要があります。                         
   c. プライベート エンドポイント リソースに移動して、仮想ネットワークが、移行プロジェクトのプライベート エンドポイントの仮想ネットワークと一致しているかどうかを確認することもできます。                                                        

     ![プライベート エンドポイント接続を確認する](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)


## <a name="validate-the-data-flow-through-the-private-endpoints"></a>プライベート エンドポイントを介してデータ フローを検証する
データ フロー メトリックを確認して、プライベート エンドポイント経由のトラフィック フローを確認します。 [Azure Migrate: サーバー評価とサーバー移行のプロパティ] ページで、プライベート エンドポイントを選択します。 これにより、Azure プライベート リンク センターの 「プライベート エンドポイントの概要」セクションにリダイレクトされます。 左側のメニューで **[メトリック]** を選択して、トラフィック フローを表示するための _データ バイト数_ と _データ バイト アウト_ 情報を表示します。

## <a name="verify-dns-resolution"></a>DNS 解決を確認する

オンプレミス アプライアンス (またはレプリケーション プロバイダー) が Azure Migrate リソースにアクセスする際は、その完全修飾プライベート リンク ドメイン名 (FQDN) が使用されます。 ソース環境からプライベート エンドポイントのプライベート IP アドレスを解決するために、追加の DNS 設定が必要になる場合があります。 ネットワーク接続の問題をトラブルシューティングする際に役立つ可能性のある DNS 構成のシナリオについては、[こちらのアーティクルを参照](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)してください。  

プライベート リンク接続を検証するには、Migrate アプライアンスのホストとなるオンプレミス サーバーから Azure Migrate リソース エンドポイント (プライベート リンク リソースの FQDN) の DNS 解決を実行し、その FQDN がプライベート IP アドレスに解決されることを確認します。
プライベート エンドポイントの詳細とプライベート リンク リソースの FQDN の情報は、Discovery and Assessment と Server Migration のプロパティ ページで入手できます。 **[DNS 設定のダウンロード]** を選択すると、その一覧が表示されます。   

 ![Azure Migrate: Discovery and Assessment のプロパティ](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 [![Azure Migrate: Server Migration のプロパティ](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-expanded.png#lightbox)

ストレージ アカウントのプライベート リンク FQDN を DNS 解決する具体的な例  

- 「_nslookup<storage-account-name>_ .blob.core.windows.net」と入力します。  <storage-account-name> は、Azure Migrate で使用するストレージ アカウントの名前に置き換えてください。  

    このようなメッセージが返されます。  

   ![DNS 解決の例](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- ストレージ アカウントに対応する 10.1.0.5 というプライベート IP アドレスが返されます。 このアドレスは、プライベート エンドポイントの仮想ネットワーク サブネットに属します。   

他の Azure Migrate アーティファクトについても、同様の方法を使用して DNS 解決を検証できます。   

DNS 解決が正しくない場合は、これらの手順を実行します。  

- カスタム DNS を使用している場合は、カスタム DNS の設定を確認し、DNS 構成が正しいことを確認します。 ガイダンスについては、[プライベート エンドポイントの概要に関する記事の「DNS の構成」](../private-link/private-endpoint-overview.md#dns-configuration)を参照してください。
- Azure に用意されている DNS サーバーを使用する場合は、以降のセクションを参照して、引き続きトラブルシューティングを行います。  

> [!Tip]
> ソース環境の DNS レコードを手動で更新する場合は、プライベート リンク リソースの FQDN とそれに対応するプライベート IP アドレスで、オンプレミス アプライアンス上にある DNS の hosts ファイルを編集してください。 この方法は、テスト目的でのみ推奨されます。 <br/>  


## <a name="validate-the-private-dns-zone"></a>プライベート DNS ゾーンを検証する   
前のセクションで説明したように DNS 解決が機能していない場合は、プライベート DNS ゾーンに問題がある可能性があります。  

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>必要なプライベート DNS ゾーン リソースが存在することを確認する  
既定では Azure Migrate は、*privatelink* サブドメインに対応するプライベート DNS ゾーンも作成します。 プライベート DNS ゾーンは、プライベート エンドポイントのリソース グループと同じ Azure リソース グループに作成されます。 Azure リソース グループには、次の形式のプライベート DNS ゾーン リソースが含まれている必要があります。
- privatelink.vaultcore.azure.net (キー コンテナー)
- privatelink.blob.core.windows.net (ストレージ アカウント)
- privatelink.siterecovery.windowsazure.com (Recovery Services コンテナー) (Hyper-V およびエージェントベースのレプリケーションの場合)
- privatelink.prod.migration.windowsazure.com (移行プロジェクト、評価プロジェクト、探索サイト)   

Azure Migrate は、プライベート DNS ゾーンを自動的に作成します (ユーザーが選択したキャッシュ ストレージ アカウントとレプリケーション ストレージ アカウントを除くを除く)。 リンクされたプライベート DNS ゾーンは、プライベート エンドポイント ページに移動し、DNS 構成を選択することで特定できます。 プライベート DNS ゾーンは、[private DNS integration (プライベート DNS 統合)] セクションに表示されます。

[![DNS 構成のスクリーンショット](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-expanded.png#lightbox)

DNS ゾーンが存在しない場合は (下図)、[新しいプライベート DNS ゾーン リソースを作成](../dns/private-dns-getstarted-portal.md)してください。  

[![プライベート DNS ゾーンを作成する](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-expanded.png#lightbox)

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>プライベート DNS ゾーンが仮想ネットワークにリンクされていることを確認する  
リソース エンドポイントのプライベート IP アドレスを DNS クエリで解決するためには、プライベート エンドポイントを含んだ仮想ネットワークにプライベート DNS ゾーンがリンクされている必要があります。 プライベート DNS ゾーンが正しい仮想ネットワークにリンクされていない場合、その仮想ネットワークからの DNS 解決では、プライベート DNS ゾーンが無視されます。   

Azure portal でプライベート DNS ゾーン リソースに移動し、左側のメニューから [仮想ネットワーク リンク] を選択します。 リンクされている仮想ネットワークが表示されます。

[![仮想ネットワーク リンクを表示する](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-expanded.png#lightbox)

これにより、リンクの一覧が表示されます。どれにも、自分のサブスクリプション内の仮想ネットワークの名前が表示されています。 プライベート エンドポイント リソースが含まれる仮想ネットワークが、この一覧に表示される必要があります。 そのようになっていない場合は、[この記事に従って](../dns/private-dns-getstarted-portal.md#link-the-virtual-network)、プライベート DNS ゾーンを仮想ネットワークにリンクしてください。    

プライベート DNS ゾーンを仮想ネットワークにリンクすると、その仮想ネットワークから発信された DNS 要求で、プライベート DNS ゾーン内の DNS レコードが検索されるようになります。 プライベート エンドポイントが作成された仮想ネットワークへのアドレス解決が正しく実行されるためには、これが必要です。   

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>プライベート DNS ゾーンに正しい A レコードが含まれることを確認する

トラブルシューティングの対象となるプライベート DNS ゾーンに移動します。 [概要] ページには、そのプライベート DNS ゾーンのすべての DNS レコードが表示されます。 リソースの DNS A レコードが存在することを確認してください。 A レコードの値 (IP アドレス) は、リソースのプライベート IP アドレスであることが必要です。 A レコードが見つかっても、IP アドレスが正しくない場合は、間違った IP アドレスを削除して新しい IP アドレスを追加する必要があります。 A レコード全体を削除して新しいレコードを追加し、オンプレミスのソース アプライアンスに対して DNS フラッシュを実行することをお勧めします。   

プライベート DNS ゾーンにおけるストレージ アカウントの DNS A レコードの具体的な例を次に示します。

![DNS レコード](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

プライベート DNS ゾーンにおける Recovery Services コンテナーのマイクロサービスの DNS A レコードの具体的な例を次に示します。

[![Recovery Services コンテナーの DNS レコード](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-expanded.png#lightbox)  

>[!Note]
> A レコードを削除または変更しても、TTL (Time To Live) の値の有効期限がまだ切れていない可能性があるため、マシンが古い IP アドレスに解決される場合があります。  

### <a name="items-that-may-affect-private-link-connectivity"></a>プライベート リンクの接続に影響を与える可能性のある項目  

以下、高度なシナリオまたは複雑なシナリオで想定される事柄をいくつか挙げてみます。

- ファイアウォールの設定。仮想ネットワークに接続されている Azure Firewall、またはアプライアンス マシンにデプロイされるカスタム ファイアウォール ソリューションが該当します。  
- ネットワーク ピアリング。使用される DNS サーバーと、トラフィックのルーティング方法に影響を与える可能性があります。  
- カスタム ゲートウェイ (NAT) ソリューション。DNS クエリからのトラフィックなど、トラフィックのルーティング方法に影響を与える可能性があります。

詳細については、[プライベート エンドポイント接続の問題に関するトラブルシューティング ガイド](../private-link/troubleshoot-private-endpoint-connectivity.md)を参照してください。  
