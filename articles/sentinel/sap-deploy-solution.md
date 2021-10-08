---
title: SAP の継続的な脅威監視をデプロイする | Microsoft Docs
description: SAP 環境用の Azure Sentinel ソリューションをデプロイする方法について説明します。
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: how-to
ms.custom: mvc
ms.date: 07/06/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: 7b6f68eea2c177ad4e6776723ae0387c0e0da6a1
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361828"
---
#  <a name="deploy-sap-continuous-threat-monitoring-public-preview"></a>SAP の継続的な脅威監視をデプロイする (パブリック プレビュー)

この記事では、Azure Sentinel の継続的な脅威監視を SAP にデプロイするプロセスについて、順を追って説明します。

> [!IMPORTANT]
> Azure Sentinel SAP ソリューションは、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="overview"></a>概要

[Azure Sentinel ソリューション](sentinel-solutions.md)には、脅威検出、ブック、ウォッチリストなどのバンドルされたセキュリティ コンテンツが含まれています。 ソリューションを使用すると、単一のプロセスを使用して、特定のデータ コネクタの Azure Sentinel セキュリティ コンテンツをオンボードできます。

Azure Sentinel SAP データ コネクタを使用すると、ビジネスおよびアプリケーションの層内の高度な脅威について SAP システムを監視できます。

SAP データ コネクタは、SAP システム ランドスケープ全体から 14 もの多くのアプリケーション ログをストリーミングし、NetWeaver RFC 呼び出しを介して Advanced Business Application Programming (ABAP)、OSSAP 制御インターフェイスを介してファイル ストレージ データの両方からログを収集します。 SAP データ コネクタによって、基になる SAP インフラストラクチャを監視する機能が Azure Sentinels に追加されます。

SAP ログを Azure Sentinel に取り込むには、お使いの SAP 環境に Azure Sentinel SAP データ コネクタがインストールされている必要があります。 このチュートリアルで説明されているように、デプロイには Azure VM で Docker コンテナーを使用することをお勧めします。

SAP データ コネクタをデプロイしたら、SAP ソリューションのセキュリティ コンテンツをデプロイし、組織の SAP 環境に関する分析情報をスムーズに取得して、関連するセキュリティ運用機能を向上させます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * SAP データ コネクタのデプロイ用に SAP システムを準備する方法
> * Docker コンテナーと Azure VM を使用して SAP データ コネクタをデプロイする方法
> * SAP ソリューションのセキュリティ コンテンツを Azure Sentinel にデプロイする方法

> [!NOTE]
> セキュリティで保護された SNC 接続を使用して SAP データ コネクタをデプロイするには、追加の手順が必要です。 詳細については、「[SNC を使用して Azure Sentinel SAP データ コネクタをデプロイする](sap-solution-deploy-snc.md)」を参照してください。
>
## <a name="prerequisites"></a>前提条件

このチュートリアルで説明されているように、Azure Sentinel SAP データ コネクタとセキュリティ コンテンツをデプロイするには、次の前提条件を満たしている必要があります。

|領域  |説明  |
|---------|---------|
|**Azure の前提条件**     |  **Azure Sentinel へのアクセス**。 [SAP データ コネクタをデプロイ](#deploy-your-sap-data-connector)するときに、このチュートリアルで使用する Azure Sentinel ワークスペース ID とキーをメモしておきます。 <br>Azure Sentinel からこれらの詳細を表示するには、 **[設定]**  >  **[ワークスペース設定]**  >  **[エージェント管理]** の順に移動します。 <br><br>**Azure リソースを作成する機能**。 詳細については、[Azure Resource Manager のドキュメント](../azure-resource-manager/management/manage-resources-portal.md)を参照してください。 <br><br>**Azure Key Vault へのアクセス**。 このチュートリアルでは、Azure Key Vault を使用して資格情報を格納するための推奨手順について説明します。 詳しくは、[Azure Key Vault のドキュメント](../key-vault/index.yml)をご覧ください。       |
|**システムの前提条件**     |   **ソフトウェア**。 SAP データ コネクタのデプロイ スクリプトによって、ソフトウェアの前提条件が自動的にインストールされます。 詳細については、「[自動的にインストールされるソフトウェア](#automatically-installed-software)」を参照してください。 <br><br> **システム接続**。 SAP データ コネクタ ホストとして機能している VM が次にアクセスできることを確認します。 <br>- Azure Sentinel <br>- Azure Key Vault <br>- SAP 環境ホスト。*32xx*、*5xx13*、および *33xx* (*xx* は SAP インスタンス番号) の TCP ポートを介してアクセス。 <br><br>SAP ソフトウェアのダウンロード ページにアクセスするために、SAP ユーザー アカウントも持っていることを確認します。<br><br>**システム アーキテクチャ**。 SAP ソリューションは Docker コンテナーとして VM にデプロイされます。各 SAP クライアントには独自のコンテナー インスタンスが必要です。 推奨されるサイズ変更については、「[仮想マシンの推奨されるサイズ設定](sap-solution-detailed-requirements.md#recommended-virtual-machine-sizing)」を参照してください。 <br>VM と Azure Sentinel ワークスペースは、異なる Azure サブスクリプション、さらには異なる Azure AD テナントに含めることもできます。|
|**SAP 前提条件**     |   **サポートされている SAP バージョン**。 [SAP_BASIS バージョン 750 SP13](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows) 以降を使用することをお勧めします。 <br>以前の SAP バージョン [SAP_BASIS 740](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows) で作業している場合は、このチュートリアルの別の手順を選択します。<br><br> **SAP システムの詳細**。 このチュートリアルで使用するために、次の SAP システムの詳細をメモしておきます。<br>    - SAP システムの IP アドレス<br>- SAP システム番号 (`00` など)<br>    - SAP NetWeaver システムからの SAP システム ID。 たとえば、「 `NPL` 」のように入力します。 <br>- SAP クライアント ID (`001` など)。<br><br>**SAP NetWeaver インスタンスへのアクセス**。 SAP インスタンスへのアクセスには、次のいずれかのオプションを使用する必要があります。 <br>- [SAP ABAP ユーザー/パスワード](#configure-your-sap-system)。 <br>- SAP CRYPTOLIB PSE を使用する X509 証明書を持つユーザー。 このオプションには、専門家による手動の手順が必要な場合があります。<br><br>**SAP チームからのサポート**。  SAP システムがソリューションのデプロイ用に[正しく構成](#configure-your-sap-system)されるようにするには、SAP チームのサポートが必要です。   |
|     |         |


### <a name="automatically-installed-software"></a>自動的にインストールされるソフトウェア

[SAP データ コネクタのデプロイ スクリプト](#deploy-your-sap-data-connector)では、SUDO (root) 特権を使用して、次のソフトウェアを VM にインストールします。

- [Unzip](https://www.microsoft.com/en-us/p/unzip/9mt44rnlpxxt?activetab=pivot:overviewtab)
- [NetCat](https://sectools.org/tool/netcat/)
- [Python 3.6 以降](https://www.python.org/downloads/)
- [Python3-pip](https://pypi.org/project/pip/)
- [Docker](https://www.docker.com/)

## <a name="configure-your-sap-system"></a>SAP システムを構成する

この手順では、SAP システムに正しい前提条件がインストールされ、Azure Sentinel SAP データ コネクタのデプロイ用に構成されていることを確認する方法について説明します。

> [!IMPORTANT]
> 正しい構成にするには、SAP チームと一緒にこの手順を実行してください。
>

**SAP データ コネクタ用に SAP システムを構成するには**:

1. お使いのバージョンに応じて、次の SAP ノートがシステムにデプロイされていることを確認してください。

    |SAP Basis のバージョン  |必要なノート |
    |---------|---------|
    |- 750 SP01 から SP12<br>- 751 SP01 から SP06<br>- 752 SP01 から SP03     |  2641084: セキュリティ監査ログ データのための標準化された読み取りアクセス       |
    |- 700 から 702<br>- 710 から 711、730、731、740、750     | 2173545: CD: CHANGEDOCUMENT_READ_ALL        |
    |- 700 から 702<br>- 710 から 711、730、731、740<br>- 750 から 752     | 2502336: CD (ドキュメントの変更): RSSCD100 - データベースからではなく、アーカイブからのみ読み取る        |
    |     |         |

    新しいバージョンでは、追加の注意事項は必要ありません。 詳細については、SAP ユーザー アカウントでログインして、[SAP サポートの Launchpad サイト](https://support.sap.com/en/index.html)を参照してください。

1. Azure Sentinel GitHub リポジトリ (https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR: ) から、次の SAP 変更要求のいずれかをダウンロードしてインストールします。

    - **SAP バージョン 750 以降**: SAP 変更要求 *144 (NPLK900144)* をインストールします。
    - **SAP バージョン 740**: SAP 変更要求 *146 (NPLK900146)* をインストールします。

    この手順を実行するときは、必ずバイナリ モードを使用してファイルを SAP システムに転送し、**STMS_IMPORT** SAP トランザクション コードを使用してください。

    > [!NOTE]
    > SAP の **[Import Options]\(インポート オプション\)** 領域に、 **[Ignore Invalid Component Version]\(無効なコンポーネント バージョンを無視する\)** オプションが表示される場合があります。 表示されたら、続行する前にこのオプションを選択します。
    >

1. SAP 変更要求 *14 (NPLK900140)* をインポートして、 **/MSFTSEN/SENTINEL_CONNECTOR** という名前の新しい SAP ロールを作成します。 **STMS_IMPORT** SAP トランザクション コードを使用します。

    次のような必要なアクセス許可を使用してロールが作成されていることを確認します。

    :::image type="content" source="media/sap/required-sap-role-authorizations.png" alt-text="Azure Sentinel SAP データ コネクタに必要な SAP ロールのアクセス許可。":::

    詳細については、[ABAP ユーザーの承認](sap-solution-detailed-requirements.md#required-abap-authorizations)に関するセクションを参照してください。

1. SAP データ コネクタ用に非ダイアログの RFC/NetWeaver ユーザーを作成し、新しく作成した **/MSFTSEN/SENTINEL_CONNECTOR** ロールをアタッチします。

    - ロールをアタッチしたら、ロールのアクセス許可がユーザーに配布されていることを確認します。
    - このプロセスでは、ABAP ユーザーのユーザー名とパスワードを使用する必要があります。 新しいユーザーが作成され、必要なアクセス許可が付与されたら、ABAP ユーザー パスワードを必ず変更してください。

1. **SAP NetWeaver RFC SDK 7.50 for Linux on x86_64 64 BIT** バージョンをダウンロードして VM に配置します。これはインストール プロセスで必要になります。

    たとえば、SDK を見つけるには、[SAP ソフトウェアのダウンロード サイト](https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=01200314690100002214&V=MAINT) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip** の順に選択します。 **LINUX ON X86_64 65BIT** オプションを必ずダウンロードしてください。 SCP などを使用して、ファイルを VM にコピーします。

    SAP ソフトウェアのダウンロード ページにアクセスするには、SAP ユーザー アカウントが必要です。

1. (省略可能) SAP **Auditlog** ファイルは、システム全体で使用され、複数の SAP クライアントをサポートします。 ただし、Azure Sentinel SAP ソリューションの各インスタンスでサポートされるのは、1 つの SAP クライアントのみです。

    そのため、マルチクライアントの SAP システムがある場合は、データの重複を避けるために、SAP ソリューションをデプロイするクライアントに対してのみ **Auditlog** ファイルを有効にすることをお勧めします。


## <a name="deploy-a-linux-vm-for-your-sap-data-connector"></a>SAP データ コネクタ用の Linux VM をデプロイする

この手順では、Azure CLI を使用して Ubuntu サーバー 18.04 LTS VM をデプロイし、[システム マネージド ID ](../active-directory/managed-identities-azure-resources/index.yml)を割り当てる方法について説明します。

> [!TIP]
> データ コネクタは、RHEL バージョン 7.7 以降または SUSE バージョン 15 以降にもデプロイできます。 OS とパッチのレベルは完全に最新である必要があることに注意してください。
>

**Ubuntu VM をデプロイして準備するには**:

1. 次のコマンドを例として使用し、リソース グループと VM 名の値を挿入します。

    ```azurecli
    az vm create  --resource-group [resource group name]   --name [VM Name] --image UbuntuLTS  --admin-username azureuser --data-disk-sizes-gb 10 – --size Standard_DS2 --generate-ssh-keys  --assign-identity
    ```

1. 新しい VM に、次をインストールします。

    - Python バージョン 3.8 以降を備えた [Venv](https://docs.python.org/3.8/library/venv.html)。
    - [Azure CLI](/cli/azure/) バージョン 2.8.0 以降。

> [!IMPORTANT]
> 他の VM と同様に、組織のセキュリティのベスト プラクティスを適用するようにしてください。
>

詳細については、「[クイック スタート: Azure CLI を使用して Linux 仮想マシンを作成する](../virtual-machines/linux/quick-create-cli.md)」を参照してください。

## <a name="create-key-vault-for-your-sap-credentials"></a>SAP 資格情報のキー コンテナーを作成する

このチュートリアルでは、新しく作成した、または専用の [Azure Key Vault](../key-vault/index.yml) を使用して、SAP データ コネクタの資格情報を格納します。

**Azure Key Vault を作成または専用化するには**:

1. 新しい Azure Key Vault を作成するか、既存のものを選択して、SAP データ コネクタのデプロイ専用にします。

    たとえば、新しい Key Vault を作成するには、Key Vault リソース グループの名前を使用し、Key Vault 名を入力して、次のコマンドを実行します。

    ```azurecli
    kvgp=<KVResourceGroup>

    kvname=<keyvaultname>

    #Create Key Vault
    az keyvault create \
      --name $kvname \
      --resource-group $kvgp
    ```

1. GET、LIST、SET のアクセス許可を含むアクセス ポリシーを、以下のいずれかのメソッドを使用して VM のマネージド ID に割り当てます。

    - **Azure portal の使用**:

        Azure Key Vault で、 **[アクセス ポリシー]**  >  **[アクセス ポリシーの追加 - シークレットのアクセス許可: Get、List、Set]**  >  **[プリンシパルの選択]** の順に選択します。 自分の [VM の名前](#deploy-a-linux-vm-for-your-sap-data-connector)を入力し、 **[追加]**  >  **[保存]** の順に選択します。

        詳細については、[Key Vault](../key-vault/general/assign-access-policy-portal.md) のドキュメントを参照してください。

    - **Azure CLI を使用**:

        1. 次のコマンドを実行して、[VM のプリンシパル ID](#deploy-a-linux-vm-for-your-sap-data-connector) を取得し、Azure リソース グループの名前を入力します。

            ```azurecli
            VMPrincipalID=$(az vm show -g [resource group] -n [Virtual Machine] --query identity.principalId -o tsv)
            ```

            次の手順で使用するプリンシパル ID が表示されます。

        1. VM のアクセス許可を Key Vault に割り当てるには、リソース グループの名前と、前の手順で返されたプリンシパル ID 値を入力して、次のコマンドを実行します。

            ```azurecli
            az keyvault set-policy -n [key vault] -g [resource group] --object-id $VMPrincipalID --secret-permissions get list set
            ```
## <a name="deploy-your-sap-data-connector"></a>SAP データ コネクタをデプロイする

Azure Sentinel SAP データ コネクタのデプロイ スクリプトを使用すると、[必要なソフトウェア](#automatically-installed-software)がインストールされ、次にコネクタが[新しく作成された VM](#deploy-a-linux-vm-for-your-sap-data-connector) にインストールされます。資格情報は[専用のキー コンテナー](#create-key-vault-for-your-sap-credentials)に格納されます。

SAP データ コネクタのデプロイ スクリプトは、[Azure Sentinel GitHub リポジトリ > DataConnectors > SAP](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh) に格納されています。

SAP データ コネクタのデプロイ スクリプトを実行するには、次の詳細が必要です。

- 「[前提条件](#prerequisites)」セクションに記載されている Azure Sentinel ワークスペースの詳細。
- 「[前提条件](#prerequisites)」セクションに一覧表示されている SAP システムの詳細。
- SUDO 特権を持つ VM ユーザーへのアクセス。
- 「[SAP システムを構成する](#configure-your-sap-system)」で作成した、 **/MSFTSEN/SENTINEL_CONNECTOR** ロールが適用された SAP ユーザー。
- SAP チームの支援。

**SAP ソリューションのデプロイ スクリプトを実行するには**:

1. 次のコマンドを実行して、SAP ソリューションを VM にデプロイします。

    ```azurecli
    wget -O sapcon-sentinel-kickstart.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh && bash ./sapcon-sentinel-kickstart.sh
    ```

1. 画面の指示に従って、SAP と Key Vault の詳細を入力し、デプロイを完了します。 デプロイが完了すると、次の確認メッセージが表示されます。

    ```azurecli
    The process has been successfully completed, thank you!
    ```

    Azure Sentinel によって、構成された期間 (初期化時刻の 24 時間前まで) の SAP ログの取得が開始されます。

1. データ コネクタがデータを送信していることを確認するために、システム ログを確認することをお勧めします。 次を実行します。

    ```bash
    docker logs -f sapcon-[SID]
    ```

## <a name="deploy-sap-security-content"></a>SAP セキュリティ コンテンツをデプロイする

Azure Sentinel の **[ソリューション]** と **[ウォッチリスト]** の領域から、[SAP セキュリティ コンテンツ](sap-solution-security-content.md)をデプロイします。

**Azure Sentinel - Continuous Threat Monitoring for SAP** ソリューションを使用すると、SAP データ コネクタを Azure Sentinel の **[データ コネクタ]** 領域に表示することができ、**SAP - System Applications and Products** ブックと SAP 関連の分析ルールをデプロイできます。

SAP 関連のウォッチリストを Azure Sentinel ワークスペースに手動で追加します。

**SAP ソリューションのセキュリティ コンテンツをデプロイするには**:

1. Azure Sentinel のナビゲーション メニューから **[ソリューション (プレビュー)]** を選択します。

    **[ソリューション]** ページに、フィルター処理された検索可能なソリューションの一覧が表示されます。

1. **[Azure Sentinel - Continuous Threat Monitoring for SAP (プレビュー)]** を選択して、SAP ソリューション ページを開きます。

    :::image type="content" source="media/sap/sap-solution.png" alt-text="Azure Sentinel - Continuous Threat Monitoring for SAP (プレビュー) ソリューション。":::

1. **[作成]** を選択してソリューションのデプロイ ウィザードを起動し、ソリューションをデプロイする Azure サブスクリプション、リソース グループ、Log Analytics ワークスペースの詳細を入力します。

1. **[次へ]** を選択して、**データ コネクタ** の **[分析]** タブと **[ブック]** タブを切り替えます。ここでは、このソリューションでデプロイされるコンポーネントについて学習できます。

    ブックの既定の名前は **SAP - System Applications and Products - Preview** です。 必要に応じて、[ブック] タブで変更します。

    詳細については、「[Azure Sentinel SAP ソリューション: セキュリティ コンテンツ リファレンス (パブリック プレビュー)](sap-solution-security-content.md)」を参照してください。

1. **[確認と作成] タブ** で、 **[検証に成功しました]** メッセージが表示されるのを待ってから、 **[作成]** を選択してソリューションをデプロイします。

    > [!TIP]
    > **[テンプレートのダウンロード]** リンクを選択して、ソリューションをコードとしてデプロイすることもできます。

1. デプロイが完了すると、ページの右上に確認メッセージが表示されます。

    新しくデプロイされたコンテンツを表示するには:

    - **脅威の管理** > **ブック** > [組み込みの SAP ブック](sap-solution-security-content.md#built-in-workbooks)を検索する **ブック**。
    - **[構成]**  >  **[分析]** の順に選択し、一連の [SAP 関連の分析ルール](sap-solution-security-content.md#built-in-analytics-rules)を見つけます。

1. 検索、検出規則、脅威ハンティング、および応答プレイブックで使用する、SAP 関連のウォッチリストを追加します。 これらのウォッチリストにより、Azure Sentinel SAP Continuous Threat Monitoring ソリューションの構成が提供されます。

    1. Azure Sentinel GitHub リポジトリ (https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists ) から SAP ウォッチリストをダウンロードします。

    1. Azure Sentinel の **[ウォッチリスト]** 領域で、Azure Sentinel ワークスペースにウォッチリストを追加します。 ダウンロードした CSV ファイルをソースとして使用し、必要に応じてそれらをお使いの環境に合わせてカスタマイズします。 

        [ ![Azure Sentinel に追加された SAP 関連のウォッチリスト。](media/sap/sap-watchlists.png) ](media/sap/sap-watchlists.png#lightbox)

        詳細については、「[Azure Sentinel ウォッチリストを使用する](watchlists.md)」 と[使用可能な SAP ウォッチリスト](sap-solution-security-content.md#available-watchlists)を参照してください。

1. Azure Sentinel で、**Azure Sentinel Continuous Threat Monitoring for SAP** データ コネクタに移動し、接続を確認します。

    [ ![Azure Sentinel Continuous Threat Monitoring for SAP データ コネクタのページ。](media/sap/sap-data-connector.png) ](media/sap/sap-data-connector.png#lightbox)

    SAP ABAP ログは、Azure Sentinel の **[ログ]** ページの **[カスタム ログ]** の下に表示されます。

    [ ![Azure Sentinel のカスタム ログの下の SAP ABAP ログ。](media/sap/sap-logs-in-sentinel.png) ](media/sap/sap-logs-in-sentinel.png#lightbox)

    詳細については、「[Azure Sentinel SAP ソリューション ログ リファレンス (パブリック プレビュー)](sap-solution-log-reference.md)」を参照してください。


## <a name="update-your-sap-data-connector"></a>SAP データ コネクタを更新する

以前のバージョンの SAP データ コネクタで既に実行されている Docker コンテナーがある場合は、SAP データ コネクタの更新スクリプトを実行して、使用可能な最新の機能を取得します。

Azure Sentinel GitHub リポジトリから、関連するデプロイ スクリプトの最新バージョンがインストールされていることを確認します。 

次を実行します。

```azurecli
wget -O sapcon-instance-update.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-instance-update.sh && bash ./sapcon-instance-update.sh
```

マシン上の SAP データ コネクタ Docker コンテナーが更新されます。 

使用可能なその他の更新プログラムがないか、必ず確認してください。

- 関連する SAP 変更要求 ([Azure Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR)内)。
- Azure Sentinel SAP セキュリティ コンテンツ (**Azure Sentinel Continuous Threat Monitoring for SAP** ソリューション内)
- 関連するウォッチリスト ([Azure Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists)内)


## <a name="collect-sap-hana-audit-logs"></a>SAP HANA の監査ログを収集する

SAP HANA データベースの監査ログを Syslog で構成している場合は、Syslog ファイルを収集するように Log Analytics エージェントも構成する必要があります。

1. [SAP Launchpad サポート サイト](https://launchpad.support.sap.com/#/notes/0002624117)からアクセスできる *SAP ノート 0002624117* の説明に従って、Syslog を使用するように SAP HANA 監査ログ証跡が構成されていることを確認します。 詳細については、次を参照してください。

    - [SAP HANA の監査証跡 - ベスト プラクティス](https://archive.sap.com/documents/docs/DOC-51098)
    - [監査に関する推奨事項](https://help.sap.com/viewer/742945a940f240f4a2a0e39f93d3e2d4/2.0.05/en-US/5c34ecd355e44aa9af3b3e6de4bbf5c1.html)

1. オペレーティング システムの Syslog ファイルに、関連する HANA データベース イベントがないか確認します。

1. マシンに Log Analytics エージェントをインストールして構成します。

    1. HANA データベース オペレーティング システムに sudo 特権を持つユーザーとしてサインインします。
    1. Azure portal で、Log Analytics ワークスペースに移動します。 左側の **[設定]** で、 **[Agents management]\(エージェント管理\) > [Linux サーバー]** を選択します。
    1. **[Linux 用エージェントのダウンロードとオンボード]** のボックスに表示されているコードをターミナルにコピーし、スクリプトを実行します。

    Log Analytics エージェントがマシンにインストールされ、ワークスペースに接続されます。 詳細については、「[Linux コンピューターに Log Analytics エージェントをインストールする](../azure-monitor/agents/agent-linux.md)」と Microsoft GitHub リポジトリの [OMS エージェント for Linux](https://github.com/microsoft/OMS-Agent-for-Linux) を参照してください。

1. **[Agents management]\(エージェント管理\) > [Linux サーバー]** タブを更新し、**1 台の Linux コンピューターが接続されている** ことを確認します。

1. 左側の **[設定]** で、 **[Agents configuration]\(エージェントの構成\)** を選択し、 **[Syslog]** タブを選択します。

1. **[Add facility]\(ファシリティの追加\)** を選択して、収集するファシリティを追加します。 

    > [!TIP]
    > HANA データベース イベントが保存されるファシリティは、ディストリビューションごとに変わる可能性があるため、すべてのファシリティを追加し、Syslog ログと照合して、関係のないものを削除することをお勧めします。
    >

1. Azure Sentinel で、取り込んだログに HANA データベース イベントが表示されることを確認します。

## <a name="next-steps"></a>次のステップ

Azure Sentinel SAP ソリューションについてさらに詳しく説明します。

- [SNC を使用して Azure Sentinel SAP データ コネクタをデプロイする](sap-solution-deploy-snc.md)
- [エキスパートの構成オプション、オンプレミス デプロイ、SAPControl のログ ソース](sap-solution-deploy-alternate.md)
- [Azure Sentinel SAP ソリューションの詳細な SAP 要件](sap-solution-detailed-requirements.md)
- [Azure Sentinel SAP ソリューション ログ リファレンス](sap-solution-log-reference.md)
- [Azure Sentinel SAP ソリューション: 組み込みのセキュリティ コンテンツ](sap-solution-security-content.md)
- [Azure Sentinel SAP ソリューションのデプロイのトラブルシューティング](sap-deploy-troubleshoot.md)

詳細については、[Azure Sentinel ソリューション](sentinel-solutions.md)に関するページを参照してください。
