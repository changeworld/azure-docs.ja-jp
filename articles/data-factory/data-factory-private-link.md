---
title: Azure Data Factory 用の Azure Private Link
description: Azure Data Factory 内での Azure Private Link の機能について説明します。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 2cd9f01404a4e33303356dd3f452cd7dbc47a747
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328566"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Data Factory 用の Azure Private Link

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure Private Link を使用すると、プライベート エンドポイント経由で Azure のさまざまなサービスとしてのプラットフォーム (PaaS) のデプロイに接続できます。 プライベート エンドポイントは、特定の仮想ネットワークおよびサブネット内のプライベート IP アドレスです。 Private Link 機能をサポートしている PaaS デプロイの一覧については、「[Private Link のドキュメント](https://docs.microsoft.com/azure/private-link/)」を参照してください。 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>カスタマー ネットワークと Azure Data Factory 間の通信をセキュリティで保護する 
クラウド内のネットワークを論理的に表すように Azure 仮想ネットワークをセットアップできます。 これにより、次のような利点があります。
* パブリック ネットワークでの攻撃から Azure リソースを保護することができます。
* ネットワークと Data Factory が相互に安全に通信できるようになります。 

また、インターネット プロトコル セキュリティ (IPsec) VPN (サイト間) 接続または Azure ExpressRoute (プライベート ピアリング) 接続をセットアップすることによって、オンプレミス ネットワークをご利用の仮想ネットワークに接続することもできます。 

オンプレミスのマシンまたは仮想ネットワーク内の仮想マシンに、セルフホステッド統合ランタイムをインストールすることもできます。 これにより、次のことが可能になります。
* クラウドのデータ ストアとプライベート ネットワーク内のデータ ストアの間でコピー アクティビティを実行する。
* オンプレミス ネットワークまたは Azure Virtual Network 内のコンピューティング リソースに対して変換アクティビティをディスパッチする。 

次の表に示すように、Azure Data Factory とお客様の仮想ネットワークの間にはいくつかの通信チャネルが必要です。

| Domain | Port | 説明 |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | コントロール プレーン。Data Factory の作成と監視に必要です。 |
| `*.{region}.datafactory.azure.net` | 443 | セルフホステッド統合ランタイムが Data Factory サービスに接続するために必要です。 |
| `*.servicebus.windows.net` | 443 | セルフホステッド統合ランタイムがインタラクティブな作成を行うために必要です。 |
| `download.microsoft.com` | 443 | セルフホステッド統合ランタイムが更新プログラムをダウンロードするために必要です。 |

Azure Data Factory で Private Link がサポートされていると、次のことを行うことができます。
* 仮想ネットワーク内にプライベート エンドポイントを作成する。
* 特定の Data Factory インスタンスへのプライベート接続を有効にする。 

Azure Data Factory サービスへの通信は、Private Link を経由し、セキュリティで保護されたプライベート接続が提供されます。 また、ご利用のリソースをより安全に保護する方法を提供するために、前述の仮想ネットワーク内のドメインとポートまたは企業ファイアウォールを構成する必要はありません。  

![Azure Data Factory アーキテクチャの Private Link の図。](./media/data-factory-private-link/private-link-architecture.png)

上記の各通信チャネルに対して Private Link サービスを有効にすると、次の機能が提供されます。
- **サポート対象**:
   - 送信方向の通信をすべてブロックした場合でも、ご利用の仮想ネットワーク内でデータ ファクトリの作成と監視を行うことができます。
   - セルフホステッド統合ランタイムと Azure Data Factory サービス間のコマンド通信をプライベート ネットワーク環境内で安全に実行できます。 セルフホステッド統合ランタイムと Azure Data Factory サービス間のトラフィックは Private Link を経由します。 
- **現在、サポートされていません**:
   - テスト接続、フォルダー リストやテーブル リストの参照、スキーマの取得、データのプレビューなど、セルフホステッド統合ランタイムを使用したインタラクティブな作成が Private Link を経由します。
   - 自動更新を有効にした場合、セルフホステッド統合ランタイムの新しいバージョンを Microsoft ダウンロード センターから自動的にダウンロードできます。

   > [!NOTE]
   > 現在サポートされていない機能については、引き続き、前述の仮想ネットワーク内のドメインとポートまたは企業ファイアウォールを構成する必要があります。 

> [!WARNING]
> リンクされたサービスを作成する場合は、必ず資格情報を Azure Key Vault 内に格納してください。 そうしないと、Azure Data Factory 内で Private Link サービスを有効にしても、資格情報が機能しません。

## <a name="set-up-private-link-for-azure-data-factory"></a>Azure Data Factory 用の Private Link をセットアップする
プライベート エンドポイントは、[Azure portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)、PowerShell、または Azure CLI を使用して作成できます。

また、次に示すように、Azure portal でご利用の Azure Data Factory に移動し、プライベート エンドポイントを作成することもできます。

![プライベート エンドポイントを作成するための [プライベート エンドポイント接続] ペインのスクリーンショット。](./media/data-factory-private-link/create-private-endpoint.png)


Azure Data Factory へのパブリック アクセスをブロックし、Private Link 経由のアクセスのみを許可する場合は、次に示すように Azure portal で Azure Data Factory へのネットワーク アクセスを無効にすることができます。

![プライベート エンドポイントを作成するための [ネットワーク アクセス] ペインのスクリーンショット。](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> パブリック ネットワーク アクセスの無効化は、Azure Integration Runtime および SQL Server Integration Services (SSIS) Integration Runtime ではなく、セルフホステッド統合ランタイムにのみ適用されます。

> [!NOTE]
> パブリック ネットワーク アクセスを無効にした後も、ユーザーはパブリック ネットワークを介して Azure Data Factory ポータルにアクセスできます。

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory UIを使用してData Factoryを作成する](quickstart-create-data-factory-portal.md)
- [Azure Data Factory の概要](introduction.md)
- [Azure Data Factory でのビジュアルの作成](author-visually.md)

