---
title: ネットワーク接続に関する問題のトラブルシューティング | Microsoft Docs
description: プライベート エンドポイントで Azure Migrate を使用する際の一般的なエラーに対するトラブルシューティングのヒントを提供します。
author: SGSneha
ms.author: v-ssudhir
ms.manager: deseelam
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: 8b71f6d0a3dade62ff701c3586c232a6cc7210d4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724826"
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

-  「_nslookup ```<storage-account-name>_.blob.core.windows.net.```」を入力します。```<storage-account-name>``` は、Azure Migrate で使用するストレージ アカウントの名前に置き換えてください。  

    このようなメッセージが返されます。  

   ![DNS 解決の例](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

-  ストレージ アカウントに対応する 10.1.0.5 というプライベート IP アドレスが返されます。 このアドレスは、プライベート エンドポイントの仮想ネットワーク サブネットに属します。   

他の Azure Migrate アーティファクトについても、同様の方法を使用して DNS 解決を検証できます。   

DNS 解決が正しくない場合は、これらの手順を実行します。  

テスト目的での **推奨事項**: ソース環境の DNS レコードを手動で更新する場合は、オンプレミス アプライアンス上にある DNS の hosts ファイルを、プライベート リンク リソースの FQDN とそれに対応するプライベート IP アドレスで編集してください。
- カスタム DNS を使用している場合は、カスタム DNS の設定を確認し、DNS 構成が正しいことを確認します。 ガイダンスについては、[プライベート エンドポイントの概要に関する記事の「DNS の構成」](../private-link/private-endpoint-overview.md#dns-configuration)を参照してください。
- Azure に用意されている DNS サーバーを使用する場合は、以降のセクションを参照して、引き続きトラブルシューティングを行います。  

> [!Tip]
> ソース環境の DNS レコードを手動で更新する場合は、プライベート リンク リソースの FQDN とそれに対応するプライベート IP アドレスで、オンプレミス アプライアンス上にある DNS の hosts ファイルを編集してください。 この方法は、テスト目的でのみ推奨されます。 <br/>  


## <a name="validate-the-private-dns-zone"></a>プライベート DNS ゾーンを検証する   
前のセクションで説明したように DNS 解決が機能していない場合は、プライベート DNS ゾーンに問題がある可能性があります。  

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>必要なプライベート DNS ゾーン リソースが存在することを確認する  
既定では Azure Migrate は、*privatelink* サブドメインに対応するプライベート DNS ゾーンも作成します。 プライベート DNS ゾーンは、プライベート エンドポイントのリソース グループと同じ Azure リソース グループに作成されます。 Azure リソース グループには、次の形式のプライベート DNS ゾーン リソースが含まれている必要があります。
-  privatelink.vaultcore.azure.net (キー コンテナー)
-  privatelink.blob.core.windows.net (ストレージ アカウント)
-  privatelink.siterecovery.windowsazure.com (Recovery Services コンテナー) (Hyper-V およびエージェントベースのレプリケーションの場合)
-  privatelink.prod.migration.windowsazure.com (移行プロジェクト、評価プロジェクト、探索サイト)   

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

-  ファイアウォールの設定。仮想ネットワークに接続されている Azure Firewall、またはアプライアンス マシンにデプロイされるカスタム ファイアウォール ソリューションが該当します。  
-  ネットワーク ピアリング。使用される DNS サーバーと、トラフィックのルーティング方法に影響を与える可能性があります。  
-  カスタム ゲートウェイ (NAT) ソリューション。DNS クエリからのトラフィックなど、トラフィックのルーティング方法に影響を与える可能性があります。

詳細については、[プライベート エンドポイント接続の問題に関するトラブルシューティング ガイド](../private-link/troubleshoot-private-endpoint-connectivity.md)を参照してください。  

## <a name="common-issues-while-using-azure-migrate-with-private-endpoints"></a>プライベート エンドポイントで Azure Migrate を使用する場合の一般的な問題
このセクションでは、よく発生する問題のいくつかを示し、問題をご自身で修復するためのトラブルシューティング手順を提案します。

### <a name="appliance-registration-fails-with-the-error-forbiddentoaccesskeyvault"></a>アプライアンスの登録がエラー ForbiddenToAccessKeyVault で失敗する
<_KeyVaultName_> に対する Azure Key Vault の作成または更新操作が、次のエラーのため失敗しました: <_ErrorMessage_>

#### <a name="possible-causes"></a>考えられる原因:
この問題は、アプライアンスの登録に使用されている Azure アカウントに必要なアクセス許可がない場合、または Azure Migrate アプライアンスが Key Vault にアクセスできない場合に発生する可能性があります。

#### <a name="remediation"></a>修復:

**Key Vault へのアクセスに関する問題のトラブルシューティング手順:**
1. アプライアンスの登録に使用する Azure ユーザー アカウントに、少なくともサブスクリプションの共同作成者アクセス許可があることを確認します。
1. アプライアンスを登録しようとしているユーザーが Key Vault にアクセスできること、および [Key Vault] > [アクセス ポリシー] セクションでアクセス ポリシーが割り当てられていることを確認します。 [詳細情報](../key-vault/general/assign-access-policy-portal.md)
-  必要な Azure ロールとアクセス許可の[詳細を参照](./migrate-appliance.md#appliance---vmware)してください。

**Key Vault に対する接続の問題のトラブルシューティング手順:** プライベート エンドポイント接続用にアプライアンスを有効にした場合は、以下の手順に従ってネットワーク接続の問題のトラブルシューティングを行います。
-  アプライアンスが同じ仮想ネットワーク内でホストされているか、プライベート リンク経由で (Key Vault プライベート エンドポイントが作成されている) ターゲットの Azure 仮想ネットワークに接続されていることを確認します。 Key Vault プライベート エンドポイントは、プロジェクトの作成エクスペリエンス中に選択した仮想ネットワークに作成されます。 仮想ネットワークの詳細は、 **[Azure Migrate] > [プロパティ]** ページで確認できます。
   ![Azure Migrate のプロパティ](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-properties-page.png)  

-  アプライアンスがプライベート リンク経由で Key Vault にネットワーク接続できることを確認します。 プライベート リンク接続を検証するには、アプライアンスをホストしているオンプレミス サーバーから Key Vault リソース エンドポイントの DNS 解決を実行して、プライベート IP アドレスに解決されることを確認します。
-  **[Azure Migrate: Discovery and Assessment] > [プロパティ]** にアクセスして、キーの生成手順で作成した Key Vault などのリソースのプライベート エンドポイントの詳細を確認します。  

      ![Azure Migrate サーバー評価のプロパティ](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-assessment-properties.png)  
-  **[DNS 設定のダウンロード]** を選択して、DNS マッピングをダウンロードします。

      ![DNS 設定のダウンロード](./media/how-to-use-azure-migrate-with-private-endpoints/download-dns-settings.png)  

-  コマンド ラインを開いて、以下の nslookup コマンドを実行し、DNS 設定ファイルに記載されている Key Vault URL へのネットワーク接続を確認します。   

    ```console
    nslookup <your-key-vault-name>.vault.azure.net
    ```

    nslookup コマンドを実行して、パブリック エンドポイントを介してキー コンテナーの IP アドレスを解決すると、次のような結果が表示されます。

    ```console
    c:\ >nslookup <your-key-vault-name>.vault.azure.net

    Non-authoritative answer:
    Name:    
    Address:  (public IP address)
    Aliases:  <your-key-vault-name>.vault.azure.net
    ```

    nslookup コマンドを実行して、プライベート エンドポイントを介してキー コンテナーの IP アドレスを解決すると、次のような結果が表示されます。

    ```console
    c:\ >nslookup your_vault_name.vault.azure.net

    Non-authoritative answer:
    Name:    
    Address:  10.12.4.20 (private IP address)
    Aliases:  <your-key-vault-name>.vault.azure.net
              <your-key-vault-name>.privatelink.vaultcore.azure.net
    ```

    前述のように、nslookup コマンドはプライベート IP アドレスに解決される必要があります。 プライベート IP アドレスは、DNS 設定ファイルに記載されているものと一致するはずです。

DNS 解決が正しくない場合は、これらの手順を実行します。

1. オンプレミス アプライアンス上にある DNS の hosts ファイルを編集し、DNS マッピングとそれに対応するプライベート IP アドレスでソース環境の DNS レコードを手動で更新します。 この方法は、テスト目的で推奨されます。

      ![DNS hosts ファイル](./media/how-to-use-azure-migrate-with-private-endpoints/dns-hosts-file-1.png)

1. カスタム DNS サーバーを使用している場合は、カスタム DNS の設定を確認し、DNS 構成が正しいことを確認します。 ガイダンスについては、[プライベート エンドポイントの概要に関する記事の「DNS の構成」](../private-link/private-endpoint-overview.md#dns-configuration)を参照してください。

1. **プロキシ サーバーに関する考慮事項**: アプライアンスでプロキシ サーバーが送信接続に使用されている場合は、ネットワーク設定と構成を検証して、プライベート リンク URL に到達可能であり、予想どおりにルーティングできることを確認する必要が生じる場合があります。

    - プロキシ サーバーがインターネット接続用の場合は、プライベート リンク FQDN のプロキシ サーバーをバイパスするためにトラフィック フォワーダーまたは規則を追加する必要が生じる場合があります。 プロキシ バイパス規則を追加する方法の[詳細を参照してください](./how-to-use-azure-migrate-with-private-endpoints.md#set-up-prerequisites)。
    - または、プロキシ サーバーがすべての送信トラフィック用の場合は、プロキシ サーバーがプライベート リンク FQDN をそれぞれのプライベート IP アドレスに解決できることを確認します。 簡単な回避策として、上に示すように、DNS マッピングと、関連付けられたプライベート IP アドレスを使用して、プロキシ サーバー上の DNS レコードを手動で更新できます。 この方法は、テスト目的で推奨されます。

1. それでも問題が解決しない場合は、[こちらのセクションを参照して](#validate-the-private-dns-zone)、さらにトラブルシューティングを行ってください。

接続を確認したら、登録プロセスを再試行します。

### <a name="start-discovery-fails-with-the-error-agentnotconnected"></a>検出の開始がエラー AgentNotConnected で失敗する
オンプレミスのエージェントが次の Azure Migrate サービス エンドポイントと通信できないため、アプライアンスは検出を開始できませんでした: Azure 内の <_URLname_>。

   ![エージェントが接続されていないエラー](./media/how-to-use-azure-migrate-with-private-endpoints/agent-not-connected-error.png)  

#### <a name="possible-causes"></a>考えられる原因:
この問題は、アプライアンスがエラー メッセージに示されているサービス エンドポイントに接続できない場合に発生する可能性があります。

#### <a name="remediation"></a>修復:
アプライアンスが直接またはプロキシを介して接続されていることを確認し、エラー メッセージに示されているサービス エンドポイントを解決できることを確認します。  

プライベート エンドポイント接続用にアプライアンスを有効にした場合は、アプライアンスがプライベート リンク経由で Azure 仮想ネットワークに接続されていて、エラー メッセージに示されているサービス エンドポイントを解決できることを確認します。

**Azure Migrate サービス エンドポイントに対するプライベート リンク接続の問題のトラブルシューティングの手順:**

プライベート エンドポイント接続用にアプライアンスを有効にした場合は、以下の手順に従ってネットワーク接続の問題のトラブルシューティングを行います。

-  アプライアンスが同じ仮想ネットワーク内でホストされているか、プライベート リンク経由で (プライベート エンドポイントが作成されている) ターゲットの Azure 仮想ネットワークに接続されていることを確認します。 Azure Migrate サービスのプライベート エンドポイントは、プロジェクトの作成エクスペリエンス中に選択した仮想ネットワークに作成されます。 仮想ネットワークの詳細は、 **[Azure Migrate] > [プロパティ]** ページで確認できます。

      ![Azure Migrate のプロパティ](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-properties-page.png)   

-  アプライアンスがプライベート リンク接続を介して、エラー メッセージに示されているサービス エンドポイントの URL やその他の URL にネットワーク接続できることを確認します。 プライベート リンク接続を検証するには、アプライアンスをホストしているオンプレミス サーバーから URL の DNS 解決を実行して、プライベート IP アドレスに解決されることを確認します。
-  **[Azure Migrate: Discovery and Assessment] > [プロパティ]** にアクセスして、キーの生成手順で作成したサービス エンドポイントのプライベート エンドポイントの詳細を確認します。 

      ![Azure Migrate サーバー評価のプロパティ](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-assessment-properties.png)  

- **[DNS 設定のダウンロード]** を選択して、DNS マッピングをダウンロードします。

   ![DNS 設定のダウンロード](./media/how-to-use-azure-migrate-with-private-endpoints/download-dns-settings.png)   

|**プライベート エンドポイント URL を含む DNS マッピング**  | **詳細** |
|--- | ---|
|*.disc.privatelink.test.migration.windowsazure.com | Azure Migrate Discovery のサービス エンドポイント
|*.asm.privatelink.test.migration.windowsazure.com  | Azure Migrate Assessment のサービス エンドポイント  
|*.hub.privatelink.test.migration.windowsazure.com  | 他の Microsoft または外部の[独立系ソフトウェア ベンダー (ISV)](./migrate-services-overview.md#isv-integration) のオファリングからデータを受信するための Azure Migrate ハブ エンドポイント
|*.vault.azure.net | Key Vault エンドポイント
|*.blob.core.windows.net | 依存関係およびパフォーマンス データのストレージ アカウント エンドポイント  

上記の URL に加えて、アプライアンスはインターネット経由 (直接またはプロキシ経由) で、以下の URL にアクセスできる必要があります。

| **その他のパブリック クラウド URL<br> (パブリック エンドポイント URL)** | **詳細** |
|--- | ---|
|*.portal.azure.com | Azure portal に移動します
|*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com <br/> *.microsoftonline.com <br/> *.microsoftonline-p.com <br/> | Azure Active Directory によるアクセス制御と ID 管理に使用されます
|management.azure.com | Azure Resource Manager のデプロイのトリガー用です
|*.services.visualstudio.com (省略可能) | 内部監視に使用するアプライアンス ログをアップロードします。
|aka.ms/* (省略可能) | アプライアンス サービスの最新の更新プログラムをダウンロードしてインストールするために使用される、aka リンクへのアクセスを許可します
|download.microsoft.com/download | Microsoft ダウンロード センターからのダウンロードを許可します    

-  コマンド ラインを開いて、以下の nslookup コマンドを実行し、DNS 設定ファイルに記載されている URL へのプライベートリンク接続を確認します。 DNS 設定ファイル内のすべての URL に対してこの手順を繰り返します。

    _**図**: 検出サービス エンドポイントへのプライベート リンク接続の検証_

    ```console
    nslookup 04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com
    ```
    要求がプライベート エンドポイント経由で検出サービス エンドポイントに到達できた場合、以下のような結果が表示されます。

    ```console
    nslookup 04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com

    Non-authoritative answer:
    Name:    
    Address:  10.12.4.23 (private IP address)
    Aliases:  04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com
              prod.cus.discoverysrv.windowsazure.com
    ```

    前述のように、nslookup コマンドはプライベート IP アドレスに解決される必要があります。 プライベート IP アドレスは、DNS 設定ファイルに記載されているものと一致するはずです。

DNS 解決が正しくない場合は、これらの手順を実行します。

1. オンプレミス アプライアンス上にある DNS の hosts ファイルを編集し、DNS マッピングとそれに対応するプライベート IP アドレスでソース環境の DNS レコードを手動で更新します。 この方法は、テスト目的で推奨されます。

      ![DNS hosts ファイル](./media/how-to-use-azure-migrate-with-private-endpoints/dns-hosts-file-1.png)

1. カスタム DNS サーバーを使用している場合は、カスタム DNS の設定を確認し、DNS 構成が正しいことを確認します。 ガイダンスについては、[プライベート エンドポイントの概要に関する記事の「DNS の構成」](../private-link/private-endpoint-overview.md#dns-configuration)を参照してください。

1. **プロキシ サーバーに関する考慮事項**: アプライアンスでプロキシ サーバーが送信接続に使用されている場合は、ネットワーク設定と構成を検証して、プライベート リンク URL に到達可能であり、予想どおりにルーティングできることを確認する必要が生じる場合があります。

    - プロキシ サーバーがインターネット接続用の場合は、プライベート リンク FQDN のプロキシ サーバーをバイパスするためにトラフィック フォワーダーまたは規則を追加する必要が生じる場合があります。 プロキシ バイパス規則を追加する方法の[詳細を参照してください](./how-to-use-azure-migrate-with-private-endpoints.md#set-up-prerequisites)。
    - または、プロキシ サーバーがすべての送信トラフィック用の場合は、プロキシ サーバーがプライベート リンク FQDN をそれぞれのプライベート IP アドレスに解決できることを確認します。 簡単な回避策として、上に示すように、DNS マッピングと、関連付けられたプライベート IP アドレスを使用して、プロキシ サーバー上の DNS レコードを手動で更新できます。 この方法は、テスト目的で推奨されます。

1. それでも問題が解決しない場合は、[こちらのセクションを参照して](#validate-the-private-dns-zone)、さらにトラブルシューティングを行ってください。

接続を確認したら、検出プロセスを再試行します。

### <a name="importexport-request-fails-with-the-error-403-this-request-is-not-authorized-to-perform-this-operation"></a>インポートまたはエクスポートの要求がエラー "403: This request is not authorized to perform this operation" (403: この要求にはこの操作を実行する権限がありません) で失敗する 

プライベート エンドポイント接続を使用するプロジェクトの場合、レポートのエクスポート、インポート、またはダウンロード要求がエラー *"403: This request is not authorized to perform this operation" (403: この要求にはこの操作を実行する権限がありません)* で失敗します。

#### <a name="possible-causes"></a>考えられる原因: 
このエラーは、エクスポート、インポート、またはダウンロード要求が承認されたネットワークから開始されなかった場合に発生する可能性があります。 これは、インポート、エクスポート、またはダウンロード要求が、プライベート ネットワーク経由で Azure Migrate サービス (Azure 仮想ネットワーク) に接続されていないクライアントから開始された場合に発生します。 

#### <a name="remediation"></a>Remediation
**オプション 1** *(推奨)* ：
  
このエラーを解決するには、プライベート リンク経由で Azure に接続されている仮想ネットワークに存在するクライアントから、インポート、エクスポート、またはダウンロード操作を再試行します。 オンプレミス ネットワークまたはアプライアンス VM で Azure portal を開き、操作を再試行することができます。 

**オプション 2**:

インポート、エクスポート、またはダウンロード要求により、レポートをアップロードまたはダウンロードするためにストレージ アカウントへの接続が行われます。 また、インポート、エクスポート、またはダウンロード操作に使用されるストレージ アカウントのネットワーク設定を変更して、他のネットワーク (パブリック ネットワーク) 経由でのストレージ アカウントへのアクセスを許可することもできます。  

パブリック エンドポイント接続用のストレージ アカウントを設定するには、

1. **ストレージ アカウントを見つける**: ストレージ アカウント名は、[Azure Migrate: Discovery and Assessment properties]\(Azure Migrate: 検出と評価のプロパティ\) ページで確認できます。 ストレージ アカウント名には、*usa* というサフィックスが付きます。 

      :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png" alt-text="DNS 設定のダウンロードのスナップショット。"::: 

2. ストレージ アカウントに移動し、ストレージ アカウントのネットワーク プロパティを編集して、すべての、または他のネットワークからのアクセスを許可します。 

    :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/networking-firewall-virtual-networks.png" alt-text="ストレージ アカウントのネットワーク プロパティのスナップショット。":::

3. また、選択したネットワークにアクセスを制限し、Azure portal にアクセスしようとしている場所からクライアントのパブリック IP アドレスを追加することもできます。  

      :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/networking-firewall.png" alt-text="クライアントのパブリック IP アドレスの追加のスナップショット。":::