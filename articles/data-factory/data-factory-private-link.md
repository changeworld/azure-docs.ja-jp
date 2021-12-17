---
title: Azure Data Factory 用の Azure Private Link
description: Azure Data Factory 内での Azure Private Link の機能について説明します。
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/16/2021
ms.openlocfilehash: 68c43d149da28f7a140ac4f4fd32d76fe53f3e2d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663309"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Data Factory 用の Azure Private Link

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure Private Link を使用すると、プライベート エンドポイント経由で Azure のさまざまなサービスとしてのプラットフォーム (PaaS) のデプロイに接続できます。 プライベート エンドポイントは、特定の仮想ネットワークおよびサブネット内のプライベート IP アドレスです。 Private Link 機能をサポートしている PaaS デプロイの一覧については、「[Private Link のドキュメント](../private-link/index.yml)」を参照してください。 

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

Azure Data Factory サービスへの通信は、Private Link を経由し、セキュリティで保護されたプライベート接続が提供されます。 

:::image type="content" source="./media/data-factory-private-link/private-link-architecture.png" alt-text="Azure Data Factory アーキテクチャの Private Link の図。":::

上記の各通信チャネルに対して Private Link サービスを有効にすると、次の機能が提供されます。
- **サポート対象**:
   - 送信方向の通信をすべてブロックした場合でも、ご利用の仮想ネットワーク内でデータ ファクトリの作成と監視を行うことができます。
   - セルフホステッド統合ランタイムと Azure Data Factory サービス間のコマンド通信をプライベート ネットワーク環境内で安全に実行できます。 セルフホステッド統合ランタイムと Azure Data Factory サービス間のトラフィックは Private Link を経由します。 
- **現在、サポートされていません**:
   - テスト接続、フォルダー リストやテーブル リストの参照、スキーマの取得、データのプレビューなど、セルフホステッド統合ランタイムを使用したインタラクティブな作成が Private Link を経由します。
   - 自動更新を有効にする場合に Microsoft ダウンロード センターから自動的にダウンロードできる、セルフホステッド統合ランタイムの新しいバージョンは、この時点ではサポートされていません。

   > [!NOTE]
   > 現在サポートされていない機能については、引き続き、前述の仮想ネットワーク内のドメインとポートまたは企業ファイアウォールを構成する必要があります。 

   > [!NOTE]
   > プライベート エンドポイント経由の Azure Data Factory への接続は、データ ファクトリのセルフホステッド統合ランタイムにのみ適用されます。 Azure Synapse についてはサポートされていません。

> [!WARNING]
> Azure Data Factory で Private Link を有効にし、同時にパブリック アクセスをブロックする場合、リンクされたサービスを作成するときに、資格情報が Azure Key Vault に格納されていることを確認してください。 そうしないと、資格情報が機能しません。

## <a name="dns-changes-for-private-endpoints"></a>プライベート エンドポイントの DNS の変更
プライベート エンドポイントを作成すると、Data Factory の DNS CNAME リソース レコードは、プレフィックス "privatelink" を持つサブドメイン内のエイリアスに更新されます。 既定では、"privatelink" サブドメインに対応する[プライベート DNS ゾーン](../dns/private-dns-overview.md)も作成されます。これには、プライベート エンドポイントの DNS A リソース レコードが含まれます。

プライベート エンドポイントを持つ VNet の外部からデータ ファクトリのエンドポイント URL を解決すると、データ ファクトリ サービスのパブリック エンドポイントに解決されます。 プライベート エンドポイントをホストしている VNet から解決されると、ストレージ エンドポイント URL はプライベート エンドポイントの IP アドレスに解決されます。

上の図の例のように、プライベート エンドポイントをホストしている VNet の外部から解決されると、Data Factory の 'DataFactoryA' の DNS リソース レコードは次のようになります。

| 名前 | 種類 | 値 |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}.datafactory.azure.net | CNAME   | DataFactoryA.{region}.datafactory.azure.net |
| DataFactoryA.{region}.datafactory.azure.net | CNAME   | <データ ファクトリ サービスのパブリック エンドポイント> |
| <データ ファクトリ サービスのパブリック エンドポイント>  | A | <データ ファクトリ サービスのパブリック IP アドレス> |

DataFactoryA の DNS リソース レコードは、プライベート エンドポイントをホストしている VNet 内で解決されると、次のようになります。

| 名前 | 種類 | 値 |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}.datafactory.azure.net | CNAME   | DataFactoryA.{region}.privatelink.datafactory.azure.net |
| DataFactoryA.{region}.privatelink.datafactory.azure.net   | A | <プライベート エンドポイントの IP アドレス> |

ネットワーク上でカスタム DNS サーバーを使用している場合、クライアントが Data Factory エンドポイントの FQDN をプライベート エンドポイントの IP アドレスに解決できる必要があります。 プライベート リンク サブドメインを VNet のプライベート DNS ゾーンに委任するように DNS サーバーを構成するか、プライベート エンドポイントの IP アドレスを使用して "DataFactoryA.{region}.datafactory.azure.net" の A レコードを構成する必要があります。

プライベート エンドポイントをサポートするように独自の DNS サーバーを構成する方法の詳細については、次の記事を参照してください。
- [Azure 仮想ネットワーク内のリソースの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-a-private-endpoint-link-for-azure-data-factory"></a>Azure Data Factory 用のプライベート エンドポイント リンクをセットアップする

このセクションでは、Azure Data Factory のプライベート エンドポイント リンクを設定します。

次に示すように、データ ファクトリの作成手順で、パブリック エンドポイントまたはプライベート エンドポイントを使用してセルフホステッド統合ランタイム (SHIR) を Azure Data Factory に接続するかどうかを選択できます。 

:::image type="content" source="./media/data-factory-private-link/disable-public-access-shir.png" alt-text="セルフホステッド統合ランタイムのパブリック アクセスをブロックしているスクリーンショット。":::

ネットワーク ブレードの Data Factory ポータル ページから作成した後、いつでも選択を変更できます。  プライベート エンドポイントを有効にしたら、プライベート エンドポイントをデータ ファクトリに追加する必要もあります。

プライベート エンドポイントには、リンク用の仮想ネットワークとサブネット、およびサブネット内の仮想マシンが必要です。これらは、プライベート エンドポイント リンクを介して接続する、セルフホステッド統合ランタイム (SHIR) を実行するために使用されます。

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成
プライベート エンドポイント リンクで使用する既存の仮想ネットワークがない場合は、それを作成し、サブネットを割り当てる必要があります。  

1. Azure portal (https://portal.azure.com ) にサインインします。
2. 画面の左上で、 **[リソースの作成] > [ネットワーク] > [仮想ネットワーク]** の順に選択するか、検索ボックスで **Virtual network** を検索します。

3. **[仮想ネットワークの作成]** の **[基本]** タブで次の情報を入力または選択します。

    | **設定**          | **Value**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **プロジェクトの詳細**  |                                                                 |
    | サブスクリプション     | Azure サブスクリプションを選択します。                                  |
    | リソース グループ   | 仮想ネットワークのリソース グループを選択します |
    | **インスタンスの詳細** |                                                                 |
    | 名前             | 仮想ネットワークの名前を入力します |
    | リージョン           | 重要: プライベート エンドポイントで使用するのと同じリージョンを選択します |

4. **[IP アドレス]** タブを選択するか、ページの下部にある **[Next: IP Addresses]\(次へ: IP アドレス\)** ボタンを選択します。

5. **[IP アドレス]** タブで、次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | IPv4 アドレス空間 | 「**10.1.0.0/16**」と入力します。 |

6. **[サブネット名]** で、 **[default]\(既定\)** という単語を選択します。

7. **[サブネットの編集]** に次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | サブネット名 | サブネットの名前を入力します |
    | サブネットのアドレス範囲 | 「**10.1.0.0/24**」と入力します。 |

8. **[保存]** を選択します。

9. **[確認と作成]** タブを選択するか、 **[確認と作成]** ボタンを選択します。

10. **［作成］** を選択します

### <a name="create-a-virtual-machine-for-the-self-hosted-integration-runtime-shir"></a>セルフホステッド統合ランタイム (SHIR) の仮想マシンを作成する
前の手順で作成した新しいサブネットでセルフホステッド統合ランタイムを実行するために、既存の仮想マシンを作成または割り当てる必要があります。

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** の順に選択するか、検索ボックスで「**仮想マシン**」を検索します。
   
2. **[仮想マシンの作成]** の **[Basic]** タブに、値を入力するか選択します。

    | 設定 | 値                                          |
    |-----------------------|----------------------------------|
    | **プロジェクトの詳細** |  |
    | サブスクリプション | Azure サブスクリプションを選択します。 |
    | リソース グループ | リソース グループの選択 |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 仮想マシンの名前を入力します |
    | リージョン | 上記の仮想ネットワークで使用するリージョンを選択します |
    | 可用性オプション | **[インフラストラクチャ冗長は必要ありません]** を選択します |
    | Image | **Windows Server 2019 Datacenter-Gen1** (または、セルフホステッド統合ランタイムをサポートするその他の Windows イメージ) を選択します。 |
    | Azure Spot インスタンス | **[いいえ]**  を選択します |
    | サイズ | VM サイズを選択するか、既定の設定を使用します |
    | **管理者アカウント** |  |
    | ユーザー名 | ユーザー名を入力します |
    | Password | [パスワード] を入力します |
    | [パスワードの確認入力] | パスワードを再入力します |

3. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。
  
4. [ネットワーク] タブで、次を選択または入力します。

    | 設定 | 値 |
    |-|-|
    | **ネットワーク インターフェイス** |  |
    | 仮想ネットワーク | 上記で作成した仮想ネットワークを選択します。 |
    | Subnet | 上記で作成したサブネットを選択します。 |
    | パブリック IP | **[なし]** を選択します。 |
    | NIC ネットワーク セキュリティ グループ | **Basic**|
    | パブリック受信ポート | **[なし]** を選択します。 |
   
5. **[Review + create]\(レビュー + 作成\)** を選択します。 
  
6. 設定を確認し、 **[作成]** を選択します。

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="create-the-private-endpoint"></a>プライベート エンドポイントを作成する 
最後に、データ ファクトリにプライベート エンドポイントを作成する必要があります。

1. Data Factory の Azure portal ページで、 **[ネットワーク]** ブレードと **[プライベート エンドポイント接続]** タブを選択し、 **[+ プライベート エンドポイント]** を選択します。 

:::image type="content" source="./media/data-factory-private-link/create-private-endpoint.png" alt-text="プライベート エンドポイントを作成するための [プライベート エンドポイント接続] ペインのスクリーンショット。":::

2. **[プライベート エンドポイントの作成]** の **[基本]** タブで、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | リソース グループの選択 |
    | **インスタンスの詳細** |  |
    | 名前  | エンドポイントの名前を入力します |
    | リージョン | 上記で作成した仮想ネットワークのリージョンを選択します |

3. **[リソース]** タブを選択するか、ページ下部の **[次へ: リソース]** ボタンを選択します。
    
4. **[リソース]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 接続方法 | **[マイ ディレクトリ内の Azure リソースに接続する]** を選択します。 |
    | サブスクリプション | サブスクリプションを選択します |
    | リソースの種類 | **[Microsoft.Datafactory/factories]** を選択します |
    | リソース | Select your data factory |
    | ターゲット サブリソース | セルフホステッド統合ランタイムと Azure Data Factory サービス間のコマンド通信用にプライベート エンドポイントを作成する場合は、 **[ターゲット サブリソース]** として **[datafactory]** を選択します。 仮想ネットワーク内でデータ ファクトリを作成および監視するためのプライベート エンドポイントを作成する場合は、 **[ターゲット サブリソース]** として **[ポータル]** を選択します。|

5. **[構成]** タブを選択するか、 **[次へ: 構成]** ボタン (画面の下部にあります) を選択します。

6. **[構成]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **ネットワーク** |  |
    | 仮想ネットワーク | 上記で作成した仮想ネットワークを選択します。 |
    | Subnet | 上記で作成したサブネットを選択します。 |
    | **プライベート DNS の統合** |  |
    | プライベート DNS ゾーンとの統合 | 既定値の **[はい]** のままにします。 |
    | サブスクリプション | サブスクリプションを選択します。 |
    | プライベート DNS ゾーン | 既定値の **[ privatelink.azurewebsites.net]** のままにします。
    

7. **[Review + create]\(レビュー + 作成\)** を選択します。

8. **[作成]** を選択します。

> [!NOTE]
> パブリック ネットワーク アクセスの無効化は、Azure Integration Runtime および SQL Server Integration Services (SSIS) Integration Runtime ではなく、セルフホステッド統合ランタイムにのみ適用されます。

> [!NOTE]
> ポータルのプライベート エンドポイントを作成した後も、ユーザーはパブリック ネットワークを介して Azure Data Factory ポータルにアクセスできます。

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory UIを使用してData Factoryを作成する](quickstart-create-data-factory-portal.md)
- [Azure Data Factory の概要](introduction.md)
- [Azure Data Factory でのビジュアルの作成](author-visually.md)
