---
title: Azure Purview向けの Amazon RDS マルチクラウド スキャン コネクタ
description: この方法ガイドでは、Microsoft SQL データと PostgreSQL データの両方を含め、Amazon RDS データベースをスキャンする方法の詳細について説明します。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/18/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: fab51bd25489527c36e37d5f60233fa62256367c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722584"
---
# <a name="amazon-rds-multi-cloud-scanning-connector-for-azure-purview-public-preview"></a>Azure Purview向けの Amazon RDS マルチクラウド スキャン コネクタ (パブリック プレビュー)

Azure 管理範囲のマルチクラウドスキャンコネクタを使用すると、Azure storage サービスに加えて、アマゾンウェブサービスを含むクラウドプロバイダー全体で組織のデータを探索することができます。

この記事では、Azure 管理範囲を使用して、Amazon RDS に現在格納されている構造化データをスキャンする方法について説明します。これには、Microsoft SQL と PostgreSQL の両方のデータベースが含まれます。また、データに存在する機密情報の種類を検出できます。 情報の保護とデータのコンプライアンスを容易にするために、データが現在格納されている Amazon RDS データベースを識別する方法についても説明します。

このサービスの場合は、管理範囲を使用して、Azure 管理範囲用のマルチクラウドスキャンコネクタが実行される、AWS へのセキュリティで保護されたアクセスを Microsoft アカウント提供します。 Azure 管理範囲のマルチクラウドスキャンコネクタは、このアクセスを Amazon RDS データベースに対して使用してデータを読み取り、メタデータと分類のみを含むスキャン結果を Azure に報告します。 Purview の分類およびラベル付けレポートを使用して、データ スキャンの結果を分析して確認します。

> [!IMPORTANT]
> Azure 管理範囲のマルチクラウドスキャンコネクタは、Azure 管理範囲に対する個別のアドオンです。 Azure 管理範囲のマルチクラウドスキャンコネクタの使用条件は、Microsoft Azure Services を取得した契約に含まれています。 詳細については、 https://azure.microsoft.com/support/legal/ の「Microsoft Azure の法的情報」を参照してください。
>

> [!IMPORTANT]
> Amazon RDS の管理範囲サポートは現在プレビューです。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="purview-scope-for-amazon-rds"></a>Amazon RDS の管理範囲スコープ

- **サポートされているデータベースエンジン**: Amazon RDS 構造化データストレージは、複数のデータベースエンジンをサポートします。 Azure 管理範囲は、Microsoft SQL および PostgreSQL に対して/ベースの Amazon RDS をサポートしています。

- **サポートされる最大列**: 300 列を超える RDS テーブルのスキャンはサポートされていません。

- **パブリックアクセスのサポート**: 管理範囲では、AWS の VPC プライベートリンクを使用したスキャンのみがサポートされており、パブリックアクセススキャンは含まれていません。

- **サポートされているリージョン**: 管理範囲は、次の AWS リージョンにある Amazon RDS データベースのみをサポートします：

    - 米国東部 (オハイオ)
    - 米国東部 ( バージニア北部)
    - 米国西部 (北 カリフォルニア)
    - 米国西部 (オレゴン)
    - ヨーロッパ (フランクフルト)
    - アジア太平洋 (東京)
    - アジア太平洋 (シンガポール)
    - アジア太平洋 (シドニー)
    - ヨーロッパ (アイルランド)
    - ヨーロッパ (ロンドン)
    - ヨーロッパ (パリ)

- **IP アドレスの要件**: RDS データベースには静的 IP アドレスが必要です。 この記事で説明されているように、静的 IP アドレスを使用して AWS PrivateLink を構成します。

- **既知の問題**: 現在、次の機能はサポートされていません。

    - **[テスト接続]** ボタン。 スキャンステータスメッセージは、接続のセットアップに関連するエラーを示します。
    - スキャンするデータベース内の特定のテーブルを選択します。
    - [データ系列](concept-data-lineage.md)。

詳細については、次を参照してください。

- [Azure Purview を使用する、リソースのクォータの管理と引き上げ](how-to-manage-quotas.md)
- [Azure Purview でサポートされているデータ ソースとファイルの種類](sources-and-scans.md)
- [Purview アカウントのプライベート エンドポイントを使用する](catalog-private-link.md)

## <a name="prerequisites"></a>前提条件

Amazon RDS データベースを管理範囲データソースとして追加し、RDS データをスキャンする前に、次の前提条件を実行したことを確認します。

> [!div class="checklist"]
> * Azure Purview データ ソース管理者である必要があります。
> * Purviewアカウントが必要です。 [Azure 管理範囲 account インスタンス](create-catalog-portal.md)をまだ作成していない場合は、作成します。
> * データを含む Amazon RDS PostgreSQL または Microsoft SQL データベースが必要です。


## <a name="configure-aws-to-allow-purview-to-connect-to-your-rds-vpc"></a>管理範囲が RDS VPC に接続できるように AWS を構成する

Azure 管理範囲は、データベースが仮想プライベートクラウド (VPC) でホストされている場合にのみ、スキャンをサポートします。この場合、RDS データベースは同じ VPC 内からしかアクセスできません。

Azure 管理範囲 service 用の Azure マルチクラウドスキャンコネクタは、AWS の別の Microsoft アカウントで実行されます。 RDS データベースをスキャンするには、Microsoft AWS アカウントが VPC 内の RDS データベースにアクセスできる必要があります。 このアクセスを許可するには、RDS VPC (顧客アカウント内) と、Azure Purview 用マルチクラウド スキャン コネクタが実行されている VPC (Microsoft アカウント) との間で [AWS PrivateLink](https://aws.amazon.com/privatelink/) を構成する必要があります。

次の図は、顧客アカウントと Microsoft アカウントの両方のコンポーネントを示しています。 黄色で強調表示されているコンポーネントは、アカウントで RDS VPC を接続するために作成する必要があるコンポーネントです。ここで、Azure Purview 用マルチクラウド スキャン コネクタは Microsoft アカウント で実行されます。

:::image type="content" source="media/register-scan-amazon-rds/vpc-architecture.png" alt-text="VPC アーキテクチャにおける Azure 管理範囲サービスのマルチクラウドスキャンコネクタの図。" border="false":::


> [!IMPORTANT]
> お客様のプライベートネットワーク用に作成された AWS リソースには、お客様の AWS 請求に対して追加のコストが発生します。
>

### <a name="configure-aws-privatelink-using-a-cloudformation-template"></a>CloudFormation テンプレートを使用して AWS PrivateLink を構成する

次の手順では、AWS CloudFormation テンプレートを使用して AWS PrivateLink を構成し、管理範囲が RDS VPC に接続できるようにする方法について説明します。 この手順は AWS で実行され、AWS 管理者を対象としています。

この cloudformation テンプレートは、 [Azure GitHub リポジトリ](https://github.com/Azure/Azure-Purview-Starter-Kit/tree/main/Amazon/AWS/RDS)からダウンロードでき、ターゲットグループ、ロードバランサー、およびエンドポイントサービスの作成に役立ちます。

- **同じ VPC 内に複数の rds サーバーがある場合は**、この手順を1回実行して、 [すべての rds サーバー IP アドレスとポートを指定](#parameters)します。 この場合、CloudFormation 出力には、RDS サーバーごとに異なるポートが含まれます。

    [これらの rds サーバーを管理範囲のデータソースとして登録](#register-an-amazon-rds-data-source)する場合は、実際の rds サーバーポートではなく、出力に含まれるポートを使用します。

- **複数の vpcs に RDS サーバーがある場合** は、各 vpcs に対してこの手順を実行します。


> [!TIP]
> この手順は手動でも実行できます。 詳細については、「 [CONFIGURE AWS PrivateLink 手動 (advanced)](#configure-aws-privatelink-manually-advanced)」を参照してください。
>

**CloudFormation テンプレートを使用して RDS データベースを準備するに** は、次のようにします。

1. Azure GitHub リポジトリから、この手順に必要な cloudformation [RDSPrivateLink_CloudFormation](https://github.com/Azure/Azure-Purview-Starter-Kit/tree/main/Amazon/AWS/RDS)をダウンロードします：

    1. [リンクされた [GitHub] ページ](https://github.com/Azure/Azure-Purview-Starter-Kit/blob/main/Amazon/AWS/RDS/Amazon_AWS_RDS_PrivateLink_CloudFormation.zip)の右側で、 **[ダウンロード]** を選択して zip ファイルをダウンロードします。

    1. .zip ファイルをローカルの場所に抽出して、 **RDSPrivateLink_CloudFormation.yaml** ファイルにアクセスできるようにします。

1. AWS ポータルで、 **Cloudformation** サービスに移動します。 ページの右上にある [新しいリソースで **スタックを作成する** >  **(standard)** ] を選択します。

1. **[前提条件-テンプレートの準備]** ページで、 **[テンプレートの準備ができ]** ました を選択します。

1. **[テンプレートの指定]** セクションで、 **[テンプレートファイルのアップロード]** を選択します。 **[ファイルの選択]** を選択し、先ほどダウンロードした **RDSPrivateLink_CloudFormation.yaml** ファイルに移動して、 **[次へ]** を選択して続行します。

1. **[スタック名]** セクションで、スタックの名前を入力します。 この名前は、プロセスの後半で作成されるリソース名に対して、自動的に追加されたサフィックスと共に使用されます。 そのため、次のようになります。

    - スタックにわかりやすい名前を使用してください。
    - スタック名が19文字以内であることを確認します。

1. <a name="parameters"></a>**パラメーター** 領域で、AWS の RDS データベースから利用可能なデータを使用して、次の値を入力します。

    |名前  |説明  |
    |---------|---------|
    |**エンドポイント & ポート**    | RDS エンドポイントの URL とポートの解決済み IP アドレスを入力します。 例: `192.168.1.1:5432` <br><br>- **RDS プロキシが構成されている場合** は、関連するデータベースのプロキシの読み取り/書き込みエンドポイントの IP アドレスを使用します。 Purviewを使用する場合は、IP アドレスが静的であるため、RDS プロキシを使用することをお勧めします。<br><br>- **同じ VPC の背後に複数のエンドポイントがある場合** は、最大10個のコンマ区切りのエンドポイントを入力します。  この場合、1つのロードバランサーが VPC に対して作成されます。これにより、AWS の Azure Purviewの Amazon RDS マルチクラウドスキャンコネクタから、VPC 内のすべての RDS エンドポイントへの接続が可能になります。       |
    |**ネットワーク**     | VPC ID を入力してください        |
    |**VPC IPv4 CIDR**     | 「VPC の CIDR」の値を入力します。 この値は、RDS データベースページの VPC リンクを選択すると確認できます。 例: `192.168.0.0/16`        |
    |**サブネット**     |ご利用の VPC に関連付けられているすべてのサブネットを選択します。         |
    |**Security**     | RDS データベースに関連付けられている VPC セキュリティ グループを選択します。         |
    |     |         |

    完了したら、[次へ] を **選択して** 続行します。

1. [スタックの構成 **] オプションの設定は** 、この手順では省略可能です。

    環境に合った設定を必要に応じて定義します。 詳細については、AWS ドキュメントにアクセス **する詳細** リンクを選択してください。 完了したら、[次へ] を **選択して** 続行します。

1. [確認 **]** ページで、選択した値が環境に合っている必要があります。 必要な変更を加え、完了したら **[スタックの作成]** を選択します。

1. 作成されるリソースを監視します。 完了すると、この手順に関連するデータが次のタブに表示される:

    - **イベント**: CloudFormation テンプレートによって実行されたイベント/アクティビティを表示します
    - **リソース**: 新しく作成されたターゲット グループ、ロード バランサー、エンドポイント サービスが表示されます
    - **出力**: **ServiceName の値と** 、RDS サーバーの IP アドレスとポートが表示されます

        複数の RDS サーバーが構成されている場合は、別のポートが表示されます。 この場合 [、RDS](#register-an-amazon-rds-data-source) データベースを Purview データ ソースとして登録するときに、実際の RDS サーバー ポートではなく、ここに示すポートを使用します。

1. [出力 **] タブ** で **、ServiceName キーの値を** クリップボードにコピーします。

    [RDS](#register-an-amazon-rds-data-source)データベースを Purview データ ソースとして登録するときに、Azure Purview ポータルで **ServiceName** キーの値を使用します。 そこで、エンドポイント サービス **を介して** プライベート ネットワークConnect **ServiceName キーを入力** します。

## <a name="register-an-amazon-rds-data-source"></a>Amazon RDS データ ソースを登録する

**Amazon RDS サーバーを Azure Purview データ ソースとして追加するには**:

1. Azure Purview で、[登録] ページに **移動Data Map** 登録アイコン ]![ を選択します。 ](./media/register-scan-amazon-s3/register-button.png) 。

1.  [ソース] **ページで、[** 登録] を **選択します。** 右側に **表示される** ソースの登録 ページで、データベース タブを選択し **、Amazon RDS (PostgreSQL)** または **Amazon RDS (SQL)** を選択します。

    :::image type="content" source="media/register-scan-amazon-rds/register-amazon-rds.png" alt-text="Amazon RDS (PostgreSQL) を選択する [ソースの登録] ページのスクリーンショット。":::

1. ソースの詳細を入力する：

    |フィールド  |説明  |
    |---------|---------|
    |**名前**     |  ソースのわかりやすい名前を入力します (例: ) `AmazonPostgreSql-Ups`       |
    |**サーバー名**     | RDS データベースの名前を次の構文で入力します： `<instance identifier>.<xxxxxxxxxxxx>.<region>.rds.amazonaws.com`  <br><br>Amazon RDS ポータルからこの URL をコピーし、その URL に AWS リージョンが含まれる必要があります。      |
    |**[ポート]**     |  RDS データベースへの接続に使用するポートを入力する：<br><br>        - PostgreSQL: `5432`<br> - Microsoft SQL:`1433`<br><br>        [CloudFormation](#configure-aws-privatelink-using-a-cloudformation-template)テンプレートを使用して AWS PrivateLink を構成し、同じ VPC に複数の RDS サーバーがある場合は、読み取り RDS サーバー ポートではなく、[CloudFormation **出力**] タブに一覧表示されているポートを使用します。       |
    |**Connect経由でプライベート ネットワークに接続する**     |  前の **手順の最後** に取得した ServiceName キー値 [を入力します](#configure-aws-privatelink-using-a-cloudformation-template)。 <br><br>RDS データベースを手動で準備した場合は、「手順5: エンドポイント サービスを作成する」の最後で取得したサービス名[の値を使用します](#step-5-create-an-endpoint-service)。       |
    |**コレクション** (省略可能)     |  データ ソースを追加するコレクションを選択します。 詳細については、[Azure Purview でのデータ ソースの管理 (プレビュー)](manage-data-sources.md) に関するページを参照してください。       |
    |     |         |

1. 続行する準備ができたら、 **[次へ]** を選択します。

RDS データ ソースが [ソース] マップまたは一覧に表示されます。 次に例を示します。

:::image type="content" source="media/register-scan-amazon-rds/amazon-rds-in-sources.png" alt-text="[ソース] ページの Amazon RDS データ ソースのスクリーンショット。":::

## <a name="create-purview-credentials-for-your-rds-scan"></a>RDS スキャン用の Purview 資格情報を作成する

Amazon RDS データ ソースでサポートされている資格情報には、Azure KeyVault シークレットに格納されているパスワードを使用したユーザー名/パスワード認証のみ含まれます。

### <a name="create-a-secret-for-your-rds-credentials-to-use-in-purview"></a>Purview で使用する RDS 資格情報のシークレットを作成する

1.  パスワードをシークレットとして Azure KeyVault に追加します。 詳細については、「 を使用してシークレットを設定および取得する」を[Key VaultをAzure portal。](../key-vault/secrets/quick-create-portal.md)

1.  Get アクセス許可と List アクセス許可を使用して、KeyVault **に** アクセス ポリシー **を** 追加します。 次に例を示します。

    :::image type="content" source="media/register-scan-amazon-rds/keyvault-for-rds.png" alt-text="Purview の RDS のアクセス ポリシーのスクリーンショット。":::

    ポリシーのプリンシパルを定義するときに、Purview アカウントを選択します。 次に例を示します。

    :::image type="content" source="media/register-scan-amazon-rds/select-purview-as-principal.png" alt-text="Purview アカウントをプリンシパルとして選択しているスクリーンショット。":::

    [保存 **] を** 選択して、アクセス ポリシーの更新プログラムを保存します。 詳細については、「アクセス ポリシーの割 [り当て」Azure Key Vault参照してください](/azure/key-vault/general/assign-access-policy-portal)。

1. Azure Purview で、KeyVault 接続を追加して KeyVault を RDS シークレットに接続し、Purview に接続します。 詳細については [、「Azure Purview でのソース認証の資格情報」を参照してください](manage-credentials.md)。

### <a name="create-your-purview-credential-object-for-rds"></a>RDS 用の Purview 資格情報オブジェクトを作成する

Azure Purview で、Amazon RDS アカウントをスキャンするときに使用する資格情報オブジェクトを作成します。

1. Purview 管理 **領域で、** セキュリティ を選択 **し、資格情報** > **の新規 に** > **アクセスします**。

1. 認証 **SQLとして 認証** を選択します。 次に、RDS 資格情報Key Vault格納する場所の詳細を入力します(例: Key Vaultシークレット)。

    次に例を示します。

    :::image type="content" source="media/register-scan-amazon-rds/new-credential-for-rds.png" alt-text="RDS の新しい資格情報のスクリーンショット。":::

詳細については [、「Azure Purview でのソース認証の資格情報」を参照してください](manage-credentials.md)。

## <a name="scan-an-amazon-rds-database"></a>Amazon RDS データベースをスキャンする

RDS データベースの Azure Purview スキャンを構成するには:

1.  [Purview **ソース] ページで** 、スキャンする Amazon RDS データ ソースを選択します。

1.  [ :::image type="icon" source="media/register-scan-amazon-s3/new-scan-button.png" border="false":::**新しいスキャン] を** 選択して、スキャンの定義を開始します。 右側に表示されるウィンドウで、次の詳細を入力し、[続行] を **選択します**。

    - **名前**: スキャンのわかりやすい名前を入力します。
    - **[データベース名**]: スキャンするデータベースの名前を入力します。 Purview の外部から使用できる名前を見つけ、登録されている RDS サーバー内の各データベースに対して個別のスキャンを作成する必要があります。
    - **資格情報**: RDS データベースにアクセスするために Azure Purview 用のマルチクラウド スキャン コネクタ用に前に作成した資格情報を選択します。

1.  [スキャン **ルール セットの選択] ウィンドウ** で、使用するスキャン ルール セットを選択するか、新しく作成します。 詳細については、「[スキャン ルール セットを作成する](create-a-scan-rule-set.md)」を参照してください。

1.  [ **スキャン トリガーの設定]** ウィンドウで、スキャンを 1 回実行するか、定期的に実行するか選択し、[続行] を **選択します**。

1.  [スキャン **の確認] ウィンドウで** 詳細を確認し、[保存して実行] を選択するか、[保存] を選択 **して** 後で実行します。

スキャンの実行中に、[更新] を **選択** してスキャンの進行状況を監視します。

> [!NOTE]
> Amazon RDS PostgreSQL データベースを操作する場合は、フル スキャンだけがサポートされます。 増分スキャンはサポートされていません。PostgreSQL には [最終変更時刻] **の値が設定** されていません。 
>

## <a name="explore-scanning-results"></a>スキャン結果を確認する

Amazon RDS データベースで Purview スキャンが完了したら、[Purview]領域にドリルダウンData Mapしてスキャン履歴を表示します。 データ ソースを選択して詳細を表示し、 **[スキャン]** タブを選択して、現在実行中または完了したスキャンを表示します。

Purview の他の領域を使用して、Amazon RDS データベースなど、データ資産内のコンテンツに関する詳細を確認します:

- **カタログ 内の RDS データを探索します**。 Purview カタログには、すべてのソースの種類にわたる統合ビューが表示され、RDS スキャンの結果は Azure SQL と同様の方法で表示されます。 フィルターを使用してカタログを参照したり、資産を参照したり、階層内を移動することができます。 詳細については、次を参照してください。

    - [チュートリアル:Azure Purview (プレビュー) で資産を参照してその系列を確認する](tutorial-browse-and-view-lineage.md)
    - [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
    - [Azure SQL Database の登録とスキャン](register-scan-azure-sql-database.md)

- **分析情報レポートを表示** して、コンテンツについての分類、秘密度ラベル、ファイルの種類、および詳細情報を表示します。

    すべての管理範囲 insights レポートには、Amazon RDS のスキャン結果と、Azure データソースから得られたその他の結果が含まれています。 該当する場合は、レポートのフィルター選択オプションに **AMAZON RDS** の資産の種類が追加されます。

    詳細については、「[Azure Purview の分析情報についての理解](concept-insights.md)」を参照してください。

- **スキャン** や **用語集** の領域など、**他の管理範囲機能で RDS データを表示** します。 詳細については、次を参照してください。

    - [スキャン ルール セットを作成する](create-a-scan-rule-set.md)
    - [チュートリアル:Azure Purview (プレビュー) で用語集の用語を作成してインポートする](tutorial-import-create-glossary-terms.md)


## <a name="configure-aws-privatelink-manually-advanced"></a>AWS PrivateLink を手動で構成する (詳細)

この手順では、Azure 管理範囲に接続するために、VPC で RDS データベースを準備するために必要な手動手順について説明します。

既定では、この記事で既に説明したように、代わりに CloudFormation テンプレートを使用することをお勧めします。 詳細については、「 [CloudFormation テンプレートを使用して AWS PrivateLink を構成](#configure-aws-privatelink-using-a-cloudformation-template)する」を参照してください。

### <a name="step-1-retrieve-your-amazon-rds-endpoint-ip-address"></a>ステップ1: Amazon RDS endpoint IP アドレスを取得する

Amazon VPC 内でホストされている Amazon RDS エンドポイントの IP アドレスを見つけます。 この IP アドレスは、後でターゲットグループを作成するときに使用します。

**RDS エンドポイントの IP アドレスを取得するに** は:

1.  Amazon RDS で、RDS データベースに移動し、エンドポイントの URL を特定します。 これは、**エンドポイント** 値として **[接続 & セキュリティ]** の下にあります。

    > [!TIP]
    > 次のコマンドを使用して、エンドポイント内のデータベースの一覧を取得することだ： `aws rds describe-db-instances`
    >

1.  エンドポイント URL を使用して、Amazon RDS データベースの IP アドレスを検索します。 たとえば、次のいずれかの方法を使用することだ：

    - **Ping**: `ping <DB-Endpoint>`

    - **nslookup**: `nslookup <Db-Endpoint>`

    - **オンライン nslookup**。 検索ボックスにデータベース **エンドポイント** の値を入力し、 **[DNS レコードの検索]** を選択します。 **NSLookup.io** は、次の画面で IP アドレスを表示します。

### <a name="step-2-enable-your-rds-connection-from-a-load-balancer"></a>ステップ 2: ロードバランサーからの RDS 接続を有効にする

後で作成するロードバランサーから RDS 接続が許可されるようにするには、次の手順を実行することだ：

1.  **VPC IP の範囲を検索** します。

    Amazon RDS で、RDS データベースに移動します。 **[接続 & セキュリティ]** 領域で、 **VPC** リンクを選択して IP 範囲 (IPv4 CIDR) を検索します。

    **[VPCs]** 領域で、IP 範囲が **IPv4 CIDR** 列に表示されます。

    > [!TIP]
    > CLI を使用してこの手順を実行するには、次のコマンドを使用します： `aws ec2 describe-vpcs`
    >
    > 詳細については、「 [ec2-AWS CLI 1.19.105 Command Reference (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/ec2/)」を参照してください。
    >

1.  <a name="security-group"></a>**この IP 範囲のセキュリティグループを作成** します。

    1. で Amazon EC2 コンソールを開き、[ https://console.aws.amazon.com/ec2/**セキュリティグループ**] に移動します。

    1. **[セキュリティグループの作成]** を選択し、セキュリティグループを作成します。次の詳細情報が含まれていることを確認します。

        - **セキュリティグループ名**: わかりやすい名前を入力します
        - **説明**: セキュリティグループの説明を入力します
        - **VPC**: RDS データベース VPC を選択します

    1.  **[受信の規則]** で、 **[規則の追加]** を選択し、次の詳細を入力します：

        - **種類**: **[カスタム TCP]** を選択する
        - **ポート範囲**: RDS データベースポートを入力します
        - **[ソース]** : **[カスタム]** を選択し、前の手順で指定した VPC IP の範囲を入力します。

    1.  ページの一番下までスクロールし、 **[セキュリティグループの作成]** を選択します。

1.  **新しいセキュリティグループを RDS に関連付け** ます。

    1.  Amazon RDS で、RDS データベースに移動し、 **[変更]** を選択します。

    1.  下へスクロールして **[接続]** セクションを表示し、 **[セキュリティグループ]** フィールドに、 [前の手順](#security-group)で作成した新しいセキュリティグループを追加します。 次に、ページの一番下までスクロールし、[続行] を選択し **ます。**

    1.  **[変更のスケジュール]** セクションで、 **[直ちに適用]** する を選択して、セキュリティグループを直ちに更新します。

    1.  **[DB インスタンスの変更]** を選択します。

> [!TIP]
> CLI を使用してこの手順を実行するには、次のコマンドを使用します。
>
> - `aws  ec2 create-security-group--description <value>--group-name <value>[--vpc-id <value>]`
>
>     詳細については、「 [AWS CLI 1.19.105 Command Reference」 (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html)を参照してください：
>
> - `aws rds --db-instance-identifier <value> --vpc-security-group-ids <value>`
>
>     詳細については、「 [modify-db-instance-AWS CLI 1.19.105 Command Reference (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)」を参照してください。
>

### <a name="step-3-create-a-target-group"></a>ステップ3: ターゲットグループを作成する

**AWS でターゲットグループを作成するに** は:

1.  で Amazon EC2 コンソールを開き、[ https://console.aws.amazon.com/ec2/**負荷分散** > **ターゲットグループ**] に移動します。

1.  **[ターゲットグループの作成]** を選択し、ターゲットグループを作成します。次の詳細情報が含まれていることを確認することだ：

    - **ターゲットの種類**: **IP アドレス** を選択します (省略可能)
    - **プロトコル**: **[TCP]** を選択します。
    - **ポート**: RDS データベースポートを入力します
    - **VPC**: RDS データベース VPC を入力します

    > [!NOTE]
    > Rds データベースのポートと VPC の値は、RDS データベースページの **[接続 & セキュリティ]** で確認できます

    終了したら、 **[次へ]** を選択します。

1.  **[ターゲットの登録]** ページで、RDS データベース IP アドレスを入力し、 **[次の保留中として含める]** を選択します。

1.  **ターゲット** テーブルに新しいターゲットが表示されたら、ページの下部にある **[ターゲットグループの作成]** を選択します。

> [!TIP]
> CLI を使用してこの手順を実行するには、次のコマンドを使用します。
>
> - `aws elbv2 create-target-group --name <tg-name> --protocol <db-protocol> --port <db-port> --target-type ip --vpc-id <db-vpc-id>`
>
>     詳細については、「 [AWS CLI 2.2.7 Command Reference」 (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-target-group.html)を参照してください。
>
> - `aws elbv2 register-targets --target-group-arn <tg-arn> --targets Id=<db-ip>,Port=<db-port>`
>
>     詳細については、「 [register-targets-AWS CLI 2.2.7 Command Reference」 (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/register-targets.html)を参照してください。
>

### <a name="step-4-create-a-load-balancer"></a>手順 4:ロード バランサーの作成

[新しいネットワークロードバランサーを作成](#create-load-balancer)して、RDS IP アドレスにトラフィックを転送するか、既存のロードバランサーに[新しいリスナーを追加](#listener)することができます。

<a name="create-load-balancer"></a>**トラフィックを RDS IP アドレスに転送するネットワークロードバランサーを作成するには、次のようにし** ます。

1.  で Amazon EC2 コンソールを開き、負荷 https://console.aws.amazon.com/ec2/**分散** > **ロードバランサー** に移動します。

1.  [ネットワーク Load Balancer を **作成 Load Balancer**] を選択し  >  、次の値を選択または入力します：

    - **スキーム**: **[内部]** を選択します

    - **VPC**: RDS データベース VPC を選択します

    - **マッピング**: すべての AWS リージョンに対して RDS が定義されていることを確認してから、これらのリージョンをすべて選択してください。 この情報は、[ [RDS データベース](#step-1-retrieve-your-amazon-rds-endpoint-ip-address)] ページの **[可用性ゾーン]** の値、 **[接続 & セキュリティ]** タブで確認できます。

    - **リスナーとルーティング**:

        - *プロトコル*: **[TCP]** を選択します
        - *ポート*: **[RDS DB ポート]** を選択します
        - *既定のアクション*:[前のステップ](#step-3-create-a-target-group)で作成したターゲットグループを選択します

1.  ページの下部にある **[作成]** を選択して  > **ロードバランサーを表示** Load Balancer ます。

1.  新しい Load Balancer の **[State]** 列がアクティブになるまで、数分待ってから画面を更新し **ます。**


> [!TIP]
> CLI を使用してこの手順を実行するには、次のコマンドを使用します。
> - `aws elbv2 create-load-balancer --name <lb-name> --type network --scheme internal --subnet-mappings SubnetId=<value>`
>
>    詳細については、「 [AWS CLI 2.2.7 Command Reference」 (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-load-balancer.html)を参照してください。
>
> - `aws elbv2 create-listener --load-balancer-arn <lb-arn> --protocol TCP --port 80 --default-actions Type=forward,TargetGroupArn=<tg-arn>`
>
>    詳細については、「 [AWS-CLI 2.2.7 コマンドリファレンス」 (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-listener.html)を参照してください。
>

<a name="listener"></a>**既存のロードバランサーにリスナーを追加するに** は、次のようにします。

1. で Amazon EC2 コンソールを開き、負荷 https://console.aws.amazon.com/ec2/**分散** > **ロードバランサー** に移動します。

1. ロードバランサー > リスナーの > **追加リスナー** を選択します。

1. **[リスナー]** タブの **[プロトコル: ポート]** 領域で、 **[TCP]** を選択し、リスナーの新しいポートを入力します。


> [!TIP]
> CLI を使用してこの手順を実行するには、次のコマンドを使用します。 `aws elbv2  create-listener --load-balancer-arn <value> --protocol <value>  --port <value> --default-actions Type=forward,TargetGroupArn=<target_group_arn>`
>
> 詳細については、AWS のドキュメントを [参照してください](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-listener.html)。
>

### <a name="step-5-create-an-endpoint-service"></a>手順 5: エンドポイントサービスを作成する

[Load Balancer が作成さ](#step-4-create-a-load-balancer)れ、その状態が **[アクティブ]** になったら、エンドポイントサービスを作成できます。

**エンドポイントサービスを作成するに** は:

1.  で Amazon VPC コンソールを開き https://console.aws.amazon.com/vpc/ 、 **[仮想プライベートクラウド > エンドポイントサービス]** に移動します。

1.  **[エンドポイントサービスの作成]** を選択し、 **[使用できるロードバランサー]** ボックスの一覧で、 [前の手順](#step-4-create-a-load-balancer)で作成した新しいロードバランサー、または新しいリスナーを追加したロードバランサーを選択します。

1.  **[エンドポイントサービスの作成]** ページで、 **[エンドポイントの受け入れを要求]** する オプションの選択を解除します。

1.  ページの下部にある [サービスの **作成** を閉じる] を選択し  > ます。

1.  **[エンドポイントサービス]** ページに戻ります。

    1. 作成した新しいエンドポイント サービスを選択します。
    1. [プリンシパルの **許可] タブで、[** プリンシパルの追加 **] を選択します**。
    1. **[ARN に追加するプリンシパル>] フィールドに、「」** と入力します `arn:aws:iam::181328463391:root` 。
    1. [プリンシパル **の追加] を選択します**。

    > [!NOTE]
    > ID を追加する場合は、アスタリスク (*****) を使用して、すべてのプリンシパルのアクセス許可を追加します。 これにより、すべての AWS アカウントのすべてのプリンシパルが、エンドポイント サービスへのエンドポイントを作成できます。 詳細については、AWS のドキュメントを [参照してください](https://docs.aws.amazon.com/vpc/latest/privatelink/add-endpoint-service-permissions.html)。

> [!TIP]
> CLI を使用してこの手順を実行するには、次のコマンドを使用します。
>
> - `aws ec2 create-vpc-endpoint-service-configuration --network-load-balancer-arns  <lb-arn>  --no-acceptance-required`
>
>    詳細については、「 [AWS-CLI 2.2.7 コマンドリファレンス」 (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-vpc-endpoint-service-configuration.html)を参照してください。
>
> - `aws ec2 modify-vpc-endpoint-service-permissions --service-id <endpoint-service-id> --add-allowed-principals <purview-scanner-arn>`
>
>    詳細については、「 [AWS CLI 2.2.7 Command Reference」 (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/modify-vpc-endpoint-service-permissions.html)を参照してください。
>

<a name="service-name"></a>**Azure Purview で使用するサービス名をコピーするには**:

エンドポイントサービスを作成した後、[ RDSデータベースをPurviewデータソースとして登録する](#register-an-amazon-rds-data-source)ときに、AzurePurviewポータルで **サービス名** の値をコピーできます。

選択した **エンドポイント サービスの** [詳細 **]** タブで[サービス名] を見つける。

> [!TIP]
> CLI を使用してこの手順を実行するには、次のコマンドを使用します： `Aws ec2 describe-vpc-endpoint-services`
>
> 詳細については、「 [AWS CLI 2.2.7 Command Reference」 (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-vpc-endpoint-services.html)を参照してください。
>

## <a name="troubleshoot-your-vpc-connection"></a>VPC 接続のトラブルシューティング

このセクションでは、Azure Purview で VPC 接続を構成するときに発生する可能性のある一般的なエラーと、それらをトラブルシューティングして解決する方法について説明します。

### <a name="invalid-vpc-service-name"></a>無効な VPC サービス名

または が Azure `Invalid VPC service name``Invalid endpoint service` Purview に表示される場合は、次の手順に従ってトラブルシューティングを行います：

1. ご利用の VPC サービス名が正しいか確認します。 次に例を示します。

    :::image type="content" source="media/register-scan-amazon-rds/locate-service-name.png" alt-text="AWS の VPC サービス名のスクリーンショット。":::

1. Microsoft ARN が許可されているプリンシパルに一覧表示されている必要があること： `arn:aws:iam::181328463391:root`

    詳細については、「[ステップ5: エンドポイント サービスを作成する」を参照してください](#step-5-create-an-endpoint-service)。

1. RDS データベースがサポートされているリージョンの 1 つに一覧表示されている必要があります。 詳細については [、「Amazon RDS の Purview スコープ」を参照してください](#purview-scope-for-amazon-rds)。

### <a name="invalid-availability-zone"></a>無効な可用性ゾーン

Azure Purview に のエラーが表示される場合は、次の 3 つのリージョンのうち少なくとも 1 つについて `Invalid Availability Zone` RDS が定義されている必要があります。

- **us-east-1a**
- **us-east-1b**
- **us-east-1c**

詳細については、AWS のドキュメントを [参照してください](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-getting-started.html)。

### <a name="rds-errors"></a>RDS エラー

Azure Purview に次のエラーが表示される場合があります。

- `Unknown database`. この場合、定義されたデータベースは存在しません。 構成されているデータベース名が正しいか確認します

- `Failed to login to the Sql data source. The given auth credential does not have permission on the target database.` この場合、ユーザー名とパスワードが正しくありません。 資格情報を確認し、必要に応じて更新します。


## <a name="next-steps"></a>次のステップ

Azure Purview の分析情報レポートの詳細について学習します。

> [!div class="nextstepaction"]
> [Azure Purview の分析情報についての理解](concept-insights.md)