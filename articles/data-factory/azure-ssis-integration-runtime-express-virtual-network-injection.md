---
title: Azure-SSIS 統合ランタイムの高速挿入用に仮想ネットワークを構成する
description: Azure-SSIS 統合ランタイムの高速挿入用に仮想ネットワークを構成する方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1d904e33f830f36e41acd0afd32dc7dd7dfaf2c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092232"
---
# <a name="express-virtual-network-injection-method-preview"></a>高速仮想ネットワークインジェクション方法 (プレビュー)

Azure Data Factory (ADF) で SQL Server Integration Services (SSIS) を使用する場合、Azure-SSIS 統合ランタイム (IR) を仮想ネットワークに参加する 2 つの方法 (標準と高速) があります。 高速方法を使用する場合は、次の要件を満たした仮想ネットワークを構成する必要があります： 

- *Microsoft.Batch* が、Azure-SSIS IRが参加するための仮想ネットワークを持つAzureサブスクリプションに登録されたリソースプロバイダーであることを確認します。 詳細な手順については、「[リソース プロバイダーとしてAzure Batch 登録する](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch)」セクションを参照 してください。

- 仮想ネットワークにリソース ロックが設定されていないことを確認します。

- Azure-SSIS IR が参加するための仮想ネットワーク内の適切なサブネットを選択します。 詳細については、以下の「[サブネットの選択](#subnet)」セクションを参照してください。

- 仮想ネットワークまたはサブネットに参加するためにAzure-SSIS IRロールベースのアクセス制御 (RBAC) アクセス許可がユーザーに付与されている必要があります。  詳細については、以下の「[仮想ネットワークのアクセス許可の選択](#perms)」セクションを参照してください。

特定のシナリオに応じて、必要に応じて次を構成できます：

- Azure-SSIS IR の送信トラフィックに静的パブリック IP アドレスを使用する場合は、以下の「[静的パブリック IP アドレスの構成](#ip)」 セクションを参照してください。

- 仮想ネットワークで独自のドメインネームシステム (DNS) サーバーを使用する場合は、以下の「[カスタム DNS サーバーの構成](#dns)」セクションを参照してください。

- ネットワーク セキュリティ グループ (NSG) を使用してサブネット上の送信トラフィックを制限する場合は、以下の 「[NSGの構成](#nsg)」セクションを参照してください。

- 送信トラフィックを監査/検査するためにユーザー定義ルート (UDRs) を使用する場合は、以下の「[UDRs の構成](#udr)」セクションを参照してください。

次の図は、Azure-SSIS IR に必要な接続を示しています。

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-express.png" alt-text="高速仮想ネットワーク挿入でのAzure-SSISIRに必要な接続を示す図。":::

## <a name="select-a-subnet"></a><a name="subnet"></a>サブネットを選択する

高速仮想ネットワーク挿入を有効にするには、Azure-SSISIRが参加する適切なサブネットを選択する必要があります：

- GatewaySubnetは仮想ネットワークゲートウェイ専用であるため、選択しないでください。

- 選択したサブネットに、Azure-SSIS IRノード番号の少なくとも2倍の使用可能なIPアドレスがあることを確認してください。 これらは、Azure-SSISIRのパッチ/アップグレードをロールアウトする際の中断を回避するために必要です。 また、Azureも、各サブネットで使用できないIPアドレスをいくつか予約しています。 最初と最後の IP アドレスはプロトコルコンプライアンスのために予約され、さらに 3 つのアドレスは Azure サービス用に予約されています。 詳細については、「[サブネット IP アドレスの制限](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)」セクションを参照 してください。

- 他の Azure サービス (Azure SQL Managed Instance、App Service など) によって排他的に占有されているサブネットは使用しないでください。

- 選択したサブネットは、*Microsoft.Batch/batchAccounts* サービスに委任する必要があります。 詳細については、[サブネット委任の概要](../virtual-network/subnet-delegation-overview.md)に関する記事を参照してください。 詳細な手順については、[サブネットをAzureBatch に委任する](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet)セクションを参照してください。

## <a name="select-virtual-network-permissions"></a><a name="perms"></a>仮想ネットワークのアクセス許可を選択する

高速仮想ネットワーク挿入を有効にするには、仮想ネットワークAzure-SSIS IRに参加するために必要な RBAC アクセス許可をユーザーに付与する必要があります。 2 つのオプションがあります。

- 組み込みの *ネットワーク共同作成者* ロールを使用します。 このロールには、必要なスコープよりずっと大きなスコープを持つ _Microsoft.Network/\*_ アクセス許可が備わっています。

- 必要な *Microsoft.Network/virtualNetworks/subnets/join/action* アクセス許可のみを含むカスタム ロールを作成します。

詳細な手順については、「[仮想ネットワークのアクセス許可を 付与する](azure-ssis-integration-runtime-virtual-network-configuration.md#grantperms)」セクションを参照してください。

## <a name="configure-a-static-public-ip-address"></a><a name="ip"></a>静的パブリック IP アドレスを構成する

Azure-SSIS IR の送信トラフィックに静的パブリック IP アドレスを使用してファイアウォールで許可する場合は、[仮想ネットワーク ネットワーク アドレス変換 (NAT)](../virtual-network/nat-gateway/nat-overview.md) を構成して設定する必要があります。

## <a name="configure-a-custom-dns-server"></a><a name="dns"></a>カスタム DNS サーバーを構成する

仮想ネットワーク内の独自の DNS サーバーを使用してプライベート ホスト名を解決する場合は、グローバルな Azure ホスト名（たとえば、`<your storage account>.blob.core.windows`という名前のAzure Blob Storage）も解決できることを確認してください。

未解決のDNS 要求を Azure 再帰リゾルバーの IP アドレス (*168.63.129.16*) に転送するように独自の DNS サーバーを構成することをお勧めします。

詳細については、「[DNS サーバーの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」セクションを参照 してください。

現時点では、独自Azure-SSIS IRに対してDNS サーバーを使用するには、次の手順に従って標準のカスタムセットアップで構成する必要があります：

1. カスタム セットアップ スクリプト ([main.cmd](https://expressvnet.blob.core.windows.net/customsetup/main.cmd)) + 関連ファイル ([setupdnsserver.ps1](https://expressvnet.blob.core.windows.net/customsetup/setupdnsserver.ps1)) をダウンロードします。

1. main.cmdの "your-dns-server-ip "をご自身のDNSサーバーのIPアドレスに置き換えてください。

1. main.cmd + setupdnsserver.ps1を独自のAzureStorage BLOBコンテナーにアップロードして、標準のカスタムセットアップを行い、Azure-SSIS IRをプロビジョニングするときにそのSAS URIを入力します。[Azure-SSIS IR のカスタマイズ](how-to-configure-azure-ssis-ir-custom-setup.md)の記事を参照してください。

> [!NOTE]
> プライベート ホスト名には完全修飾ドメイン名 (FQDN) を使用してください (たとえば、 `<your_private_server>`ではなく`<your_private_server>.contoso.com`を使用します )。 または、Azure-SSIS IR で標準のカスタム セットアップを使用して、独自の DNS サフィックス (例として `contoso.com`) を修飾されていない単一ラベルドメイン名に自動的に追加し、DNS クエリで使用する前に FQDN に変換することもできます。「[標準 カスタム セットアップ のサンプル](how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples)」セクションを参照してください。 

## <a name="configure-an-nsg"></a><a name="nsg"></a>NSG を構成する

Azure-SSIS IRによって参加されたサブネットでNSGを使用する場合は、次のアウトバウンドトラフィックを許可します： 

| トランスポート プロトコル | source | ソース ポート | 到着地 | 宛先ポート | 説明 | 
|--------------------|--------|--------------|-------------|-------------------|----------| 
| TCP | *VirtualNetwork* | * | *DataFactoryManagement* | *443* | Azure-SSIS IRがADFサービスにアクセスするために必要です。<br/><br/>そのアウトバウンドトラフィックは、現時点ではADFパブリックエンドポイントのみを使用します。 | 
| TCP | *VirtualNetwork* | * | *Sql/VirtualNetwork* | *1433、11000 ～ 11999* | （オプション）Azure SQLデータベースサーバー/マネージドインスタンスを使用してSSISカタログ（SSISDB）をホストする場合にのみ必要です。<br/><br/>Azure SQLデータベースサーバー/マネージドインスタンスがパブリックエンドポイント/仮想ネットワークサービスエンドポイントで構成されている場合は、宛先として *Sql* サービスタグを使用します。<br/><br/>Azure SQLデータベースサーバー/マネージドインスタンスがプライベートエンドポイントで構成されている場合は、宛先として *VirtualNetwork* サービスタグを使用します。<br/><br/>サーバー接続ポリシーが *リダイレクト* ではなく *プロキシ* に設定されている場合は、ポート *1433* のみが必要です。 | 
| TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *443* | （オプション）Azure Storage blob コンテナーを使用して標準のカスタムセットアップスクリプト/ファイルを格納する場合にのみ必要です。<br/><br/>Azure Storageがパブリックエンドポイント/仮想ネットワークサービスエンドポイントで構成されている場合は、宛先として *Storage* サービスタグを使用します。<br/><br/>Azure Storageがプライベートエンドポイントで構成されている場合は、宛先として *VirtualNetwork* サービスタグを使用します。 | 
| TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *445* | (オプション)Azure Filesにアクセスする必要がある場合にのみ必要です。<br/><br/>Azure Storageがパブリックエンドポイント/仮想ネットワークサービスエンドポイントで構成されている場合は、宛先として *Storage* サービスタグを使用します。<br/><br/>Azure Storageがプライベートエンドポイントで構成されている場合は、宛先として *VirtualNetwork* サービスタグを使用します。 | 

## <a name="configure-udrs"></a><a name="udr"></a>UDRs の構成

Azure-SSIS IR からの送信トラフィックを監査/検査する場合は、[ユーザー定義ルート(UDRs)](../virtual-network/virtual-networks-udr-overview.md)を使用して、境界ゲートウェイ プロトコル (BGP) ルート *0.0.0.0/0* を仮想ネットワークにアドバタイズする [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 強制トンネリングを介して、ファイアウォールとして構成されたネットワーク仮想アプライアンス (NVA) または[Azure Firewall](../firewall/overview.md)サービスにリダイレクトできます。

上記の[NSGの構成](#nsg)セクションのガイダンスに従って、Azure-SSIS IRからのアウトバウンドトラフィックを許可するために、ファイアウォールアプライアンス/サービスに同様のルールを実装する必要があります：

- Azure Firewallを使用する場合：
  - *DataFactoryManagement* サービスタグを宛先として、アウトバウンドTCPトラフィック用にポート *443* を開く必要があります。

  - Azure SQLデータベースサーバー/マネージドインスタンスを使用してSSISDBをホストする場合は、*Sql / VirtualNetwork* サービスタグを宛先として、アウトバウンドTCPトラフィック用にポート *1433、11000-11999* を開く必要があります。

  - Azure Storage BLOBコンテナーを使用して、標準のカスタムセットアップスクリプト/ファイルを格納する場合は、*Storage / VirtualNetwork* サービスタグを宛先として、アウトバウンドTCPトラフィック用にポート *443* を開く必要があります。

  - Azure Files にアクセスする必要がある場合は、*Storage/VirtualNetwork* サービス タグを宛先として使用して送信 TCP トラフィック用にポート *445* を開く必要があります。

- 他のファイアウォール アプライアンス/サービスを使用する場合:
  - 宛先として *0.0.0.0/0* または次の Azure 環境固有の FQDN を使用する送信 TCP トラフィック用にポート *443* を開く必要があります：

    | Azure 環境 | FQDN |
    |-------------------|------|
    | <b>Azure Public</b> | _\*.frontend.clouddatahub.net_ |
    | <b>Azure Government</b> | _\*.frontend.datamovement.azure.us_ |
    | <b>Azure China 21Vianet</b> | _\*.frontend.datamovement.azure.cn_ |

  - Azure SQL Database server/Managed Instance を使用して SSISDB をホストする場合は、送信先として *0.0.0.0/0* または Azure SQL Database server/Managed Instance FQDN を使用する送信 TCP トラフィック用にポート *1433、11000-11999* を開く必要があります。

  - Azure Storage BLOB コンテナーを使用して標準のカスタム セットアップ スクリプト/ファイルを格納する場合は、送信先として *0.0.0.0/0* または Azure Blob Storage FQDN を使用して送信 TCP トラフィック用にポート *443* を開く必要があります。

  - Azure Files にアクセスする必要がある場合は、送信先として *0.0.0.0/0* または Azure Files FQDN を使用して送信 TCP トラフィック用にポート *445* を開く必要があります。

## <a name="next-steps"></a>次の手順

- [ADF UI を使用して仮想ネットワークに Azure SSIS IR を参加させる](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell を使用して仮想ネットワークに Azure-SSIS IR を参加する](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR の詳細については、次の記事を参照してください。 

- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [チュートリアル:Azure への SSIS パッケージのデプロイに関するチュートリアル](tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。 このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 ここでは、SSISDB をホストするために Azure SQL Database サーバーを使用しています。 
- 「[Azure-SSIS IR を作成する](create-azure-ssis-integration-runtime.md)」。 この記事は、このチュートリアルを拡張しています。 仮想ネットワーク サービス エンドポイント、IP ファイアウォール規則、プライベート エンドポイントで構成された Azure SQL Database サーバー、または仮想ネットワークに参加する Azure SQL Managed Instance を使用して SSISDB をホストする手順について説明しています。 Azure-SSIS IR を仮想ネットワークに参加させる方法について説明します。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR についての情報を取得して理解するための方法を示します。
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。
