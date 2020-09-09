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
ms.openlocfilehash: 1d560bd4a6b826e2bf0b3e23dde563cc39863537
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322847"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Data Factory 用の Azure Private Link

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Private Link を使用すると、プライベート エンドポイントを経由して Azure 内のさまざまな PaaS サービスに接続できます。 Private Link 機能をサポートしている PaaS サービスの一覧については、「[Private Link のドキュメント](https://docs.microsoft.com/azure/private-link/)」ページを参照してください。 プライベート エンドポイントは、特定の仮想ネットワークおよびサブネット内のプライベート IP アドレスです。

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>カスタマー ネットワークと Azure Data Factory サービス間の通信をセキュリティで保護する
ご利用の Azure リソースをパブリック ネットワーク内で攻撃から保護したり、相互に安全に通信できるようにしたりするには、クラウド内のご利用のネットワークの論理表現として Azure Virtual Network を設定します。 また、IPSec VPN (サイト間) または ExpressRoute (プライベート ピアリング) を設定して、オンプレミス ネットワークをご利用の仮想ネットワークに接続することもできます。 セルフホステッド統合ランタイムをオンプレミス マシンまたは Virtual Network 内の仮想マシンにインストールすると、クラウド データ ストアとプライベート ネットワーク内のデータ ストアとの間でコピー アクティビティを実行し、オンプレミス ネットワークまたは Azure Virtual Network 内のコンピューティング リソースに対して変換アクティビティをディスパッチすることができます。 

Data Factory とお客様の仮想ネットワークの間には、いくつかの通信チャネルが必要です。


| **[ドメイン]** | **[ポート]** | **説明** |
| ---------- | -------- | --------------- |
| `pe-adf.azure.com` | 443 | コントロール プレーン。 Data Factory の作成と監視に必要です。 |
| `*.{region}.datafactory.azure.net` | 443 | セルフホステッド統合ランタイムが Data Factory サービスに接続するために必要です。 |
| `*.servicebus.windows.net` | 443 | セルフホステッド統合ランタイムがインタラクティブな作成のために必要です。 |
| `download.microsoft.com` | 443 | セルフホステッド統合ランタイムが更新プログラムをダウンロードするために必要です。 |


Azure Data Factory の Azure Private Link のサポートにより、ご利用の仮想ネットワーク内にプライベート エンドポイント (PE) を作成し、特定の Azure Data Factory へのプライベート接続を有効にすることができます。 Azure Data Factory サービスへの通信は、セキュリティで保護されたプライベート接続を提供する Azure Private Link を経由します。 また、ご利用のリソースをより安全に保護する方法を提供する仮想ネットワーク内のドメインとポートまたはご利用の企業ファイアウォールを超えて構成する必要はありません。  

![Azure Data Factory の Private Link アーキテクチャ](./media/data-factory-private-link/private-link-architecture.png)

上記の各通信チャネルに対して Private Link サービスを有効にする利点を次に示します。
- (サポートされています) 送信方向の通信をすべてブロックした場合でも、ご利用の仮想ネットワーク内で Azure Data Factory の作成と監視を行うことができます。
- (サポートされています) セルフホステッド統合ランタイムと Azure Data Factory サービス間のコマンド通信をプライベート ネットワーク環境内で安全に実行できます。 セルフホステッド統合ランタイムと Azure Data Factory サービス間のトラフィックは Private Link を経由します。 
- (現在サポートされていません) テスト接続、フォルダー リストやテーブル リストの参照、スキーマの取得、データのプレビューなど、セルフホステッド統合ランタイムを使用したインタラクティブな作成は Private Link を経由します。
- (現在サポートされていません) 自動更新を有効にした場合、セルフホステッド統合ランタイムの新しいバージョンをダウンロード センターから自動的にダウンロードできます。

> [!NOTE]
> 現在サポートされていない機能については、引き続き、仮想ネットワーク内のドメインとポートまたはご利用の企業ファイアウォールを超えて構成する必要があります。 

> [!WARNING]
> リンクされたサービスを作成する場合は、必ず資格情報を Azure Key Vault 内に格納してください。 そうしないと、Azure Data Factory 内で Private Link サービスを有効にしても機能しません。

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>Azure Data Factory 用の Private Link を設定する方法
プライベート エンドポイントは、Azure portal、PowerShell、または Azure CLI を使用して作成できます。

[ポータル](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


また、Azure portal 内でご利用の Azure Data Factory に移動し、プライベート エンドポイント (PE) を作成することもできます。

![プライベート エンドポイントの作成](./media/data-factory-private-link/create-private-endpoint.png)


この Azure Data Factory へのパブリック アクセスをブロックし、Private Link 経由のアクセスのみを許可する場合は、Azure portal 内で Azure Data Factory のネットワーク アクセスを無効にすることができます。

![プライベート エンドポイントの作成](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> パブリック ネットワーク アクセスの無効化は、Azure Integration Runtime および SSIS Integration Runtime ではなく、セルフホステッド統合ランタイムにのみ適用されます。

> [!NOTE]
> パブリック ネットワーク アクセスを無効にした後も、ユーザーはパブリック ネットワークを介して Azure Data Factory ポータルにアクセスできます。

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory UIを使用してData Factoryを作成する](quickstart-create-data-factory-portal.md)

- [Azure Data Factory の概要](introduction.md)

- [Azure Data Factory でのビジュアルの作成](author-visually.md)

