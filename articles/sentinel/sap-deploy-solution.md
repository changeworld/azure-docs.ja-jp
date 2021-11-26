---
title: SAP の継続的な脅威監視をデプロイする | Microsoft Docs
description: SAP 環境用の Microsoft Sentinel ソリューションをデプロイする方法について説明します。
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: e8d58104e2a622482280ea0a546dbacc95cdc832
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520106"
---
#  <a name="deploy-sap-continuous-threat-monitoring-preview"></a>SAP の継続的な脅威監視をデプロイする (プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel Continuous Threat Monitoring for SAP をデプロイするプロセスについて順を追って説明します。

> [!IMPORTANT]
> Microsoft Sentinel SAP ソリューションは、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="overview"></a>概要

[Microsoft Sentinel ソリューション](sentinel-solutions.md)には、脅威検出、ブック、ウォッチリストなど、バンドルされたセキュリティ コンテンツが含まれています。 これらのソリューションでは、単一のプロセスを使用して、特定のデータ コネクタの Microsoft Sentinel セキュリティ コンテンツをオンボードできます。

Microsoft Sentinel SAP データ コネクタを使用すると、ビジネス層とアプリケーション層内の高度な脅威について SAP システムを監視できます。

SAP データ コネクタは、SAP システム ランドスケープ全体から 14 個のアプリケーション ログをストリーム配信します。 このデータ コネクタは、NetWeaver RFC 呼び出しを介して Advanced Business Application Programming (ABAP) から、OSSAP 制御インターフェイスを介してファイル ストレージ データからログを収集します。 SAP データ コネクタにより、基になる SAP インフラストラクチャを監視する機能が Microsoft Sentinel に追加されます。

SAP ログを Microsoft Sentinel に取り込むには、お使いの SAP 環境に Microsoft Sentinel SAP データ コネクタがインストールされている必要があります。 このチュートリアルで説明するように、デプロイでは、Azure 仮想マシンで Docker コンテナーを使用することをお勧めします。

SAP データ コネクタをデプロイしたら、SAP ソリューションのセキュリティ コンテンツをデプロイして、組織の SAP 環境に関する分析情報を取得し、関連するセキュリティ運用機能を向上させます。

この記事では、次の方法について学習します。

> [!div class="checklist"]
> * SAP データ コネクタのデプロイ用に SAP システムを準備する。
> * Docker コンテナーと Azure 仮想マシン (VM) を使用して SAP データ コネクタをデプロイする。
> * SAP ソリューションのセキュリティ コンテンツを Microsoft Sentinel にデプロイする。

> [!NOTE]
> セキュリティで保護されたネットワーク通信 (SNC) 接続を介して SAP データ コネクタをデプロイするには追加の手順が必要です。 詳細については、「[SNC を使用して Microsoft Sentinel SAP データ コネクタをデプロイする](sap-solution-deploy-snc.md)」を参照してください。
>
## <a name="prerequisites"></a>前提条件

このチュートリアルの説明に従って、Microsoft Sentinel SAP データ コネクタとセキュリティ コンテンツをデプロイするには、次の前提条件を満たしている必要があります。

| 領域 | 説明 |
| --- | --- |
|**Azure の前提条件** | **Microsoft Sentinel へのアクセス**。 [SAP データ コネクタをデプロイする](#deploy-your-sap-data-connector)ときに、このチュートリアルで使用する Microsoft Sentinel ワークスペース ID とキーをメモしておきます。 <br><br>Microsoft Sentinel からこれらの詳細を表示するには、 **[設定]**  >  **[ワークスペース設定]**  >  **[エージェント管理]** の順に移動します。 <br><br>**Azure リソースを作成する機能**。 詳細については、[Azure Resource Manager のドキュメント](../azure-resource-manager/management/manage-resources-portal.md)を参照してください。 <br><br>**Azure キー コンテナーへのアクセス**。 このチュートリアルでは、Azure キー コンテナーを使用して資格情報を格納するための推奨手順について説明します。 詳しくは、[Azure Key Vault のドキュメント](../key-vault/index.yml)をご覧ください。 |
|**システムの前提条件** | **ソフトウェア**。 SAP データ コネクタのデプロイ スクリプトによって、ソフトウェアの前提条件が自動的にインストールされます。 詳細については、「[自動的にインストールされるソフトウェア](#automatically-installed-software)」を参照してください。 <br><br> **システム接続**。 SAP データ コネクタ ホストとして機能している VM が次にアクセスできることを確認します。 <br>- Microsoft Sentinel <br>- Azure キー コンテナー <br>- SAP 環境ホスト。*32xx*、*5xx13*、および *33xx* (*xx* は SAP インスタンス番号) の TCP ポートを介してアクセス。 <br><br>SAP ソフトウェアのダウンロード ページにアクセスするために、SAP ユーザー アカウントも持っていることを確認します。<br><br>**システム アーキテクチャ**。 SAP ソリューションは Docker コンテナーとして VM にデプロイされます。各 SAP クライアントには独自のコンテナー インスタンスが必要です。 推奨されるサイズ変更については、「[仮想マシンの推奨されるサイズ設定](sap-solution-detailed-requirements.md#recommended-virtual-machine-sizing)」を参照してください。 <br>VM と Microsoft Sentinel ワークスペースは、異なる Azure サブスクリプションに含めることができ、異なる Azure AD テナントに含めることもできます。|
|**SAP 前提条件** | **サポートされている SAP バージョン**。 [SAP_BASIS バージョン 750 SP13](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows) 以降を使用することをお勧めします。 <br><br>このチュートリアルの特定の手順では、古い SAP バージョン ([SAP_BASIS 740](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows)) で作業している場合の代替手順が示されています。<br><br> **SAP システムの詳細**。 このチュートリアルで使用するために、次の SAP システムの詳細をメモしておきます。<br>- SAP システムの IP アドレス<br>- SAP システム番号 (`00` など)<br>- SAP NetWeaver システムの SAP システム ID (`NPL` など) <br>- SAP クライアント ID (`001` など)。<br><br>**SAP NetWeaver インスタンスへのアクセス**。 SAP インスタンスへのアクセスには、次のいずれかのオプションを使用する必要があります。 <br>- [SAP ABAP ユーザー/パスワード](#configure-your-sap-system)。 <br>- SAP CRYPTOLIB PSE を使用する X509 証明書を持つユーザー。 このオプションには、専門家による手動の手順が必要になる場合があります。<br><br>**SAP チームからのサポート**。  SAP システムをソリューションのデプロイ用に[正しく構成](#configure-your-sap-system)できるようにするには、SAP チームのサポートが必要です。 |
| | |

### <a name="automatically-installed-software"></a>自動的にインストールされるソフトウェア

[SAP データ コネクタ デプロイ スクリプト](#deploy-your-sap-data-connector)では、*sudo* (root) 特権を使用して、次のソフトウェアを VM にインストールします。

- [Unzip](https://www.microsoft.com/en-us/p/unzip/9mt44rnlpxxt?activetab=pivot:overviewtab)
- [NetCat](https://sectools.org/tool/netcat/)
- [Python 3.6 以降](https://www.python.org/downloads/)
- [Python 3-pip](https://pypi.org/project/pip/)
- [Docker](https://www.docker.com/)

## <a name="configure-your-sap-system"></a>SAP システムを構成する

この手順では、SAP システムに正しい前提条件がインストールされ、Microsoft Sentinel SAP データ コネクタのデプロイ用に構成されていることを確認する方法について説明します。

> [!IMPORTANT]
> 正しい構成にするには、SAP チームと一緒にこの手順を実行してください。
>

**SAP データ コネクタ用に SAP システムを構成するには**:

1. お使いのバージョンに応じて、次の SAP ノートがシステムにデプロイされていることを確認してください。

    | SAP&nbsp;BASIS&nbsp;のバージョン | 必要なノート |
    | --- | --- |
    | - 750 SP01 から SP12<br>- 751 SP01 から SP06<br>- 752 SP01 から SP03 | 2641084: セキュリティ監査ログ データのための標準化された読み取りアクセス |
    | - 700 から 702<br>- 710 から 711、730、731、740、750 | 2173545: CD: CHANGEDOCUMENT_READ_ALL |
    | - 700 から 702<br>- 710 から 711、730、731、740<br>- 750 から 752 | 2502336: CD (ドキュメントの変更): RSSCD100 - データベースからではなく、アーカイブからのみ読み取る |
    | | |

   新しいバージョンでは追加のノートは不要です。 詳細については、[SAP サポートの Launchpad サイト](https://support.sap.com/en/index.html)を参照してください。 SAP ユーザー アカウントでログインします。

1. [Microsoft Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR)から、次の SAP 変更要求のいずれかをダウンロードしてインストールします。

    - **SAP バージョン 750 以降**: SAP 変更要求 *144 (NPLK900144)* をインストールします。
    - **SAP バージョン 740**: SAP 変更要求 *146 (NPLK900146)* をインストールします。

    この手順を実行するときは、必ずバイナリ モードを使用してファイルを SAP システムに転送し、**STMS_IMPORT** SAP トランザクション コードを使用してください。

    > [!NOTE]
    > SAP の **[Import Options]\(インポート オプション\)** 領域に、 **[Ignore Invalid Component Version]\(無効なコンポーネント バージョンを無視する\)** オプションが表示される場合があります。 表示されたら、続行する前にこのオプションを選択します。
    >

1. SAP 変更要求 *14 (NPLK900163)* をインポートして、 **/MSFTSEN/SENTINEL_CONNECTOR** という名前の新しい SAP ロールを作成します。 **STMS_IMPORT** SAP トランザクション コードを使用します。

    次のような必要なアクセス許可を使用してロールが作成されていることを確認します。

    :::image type="content" source="media/sap/required-sap-role-authorizations.png" alt-text="Microsoft Sentinel SAP データ コネクタに必要な SAP ロールのアクセス許可。":::

    詳細については、[ABAP ユーザーの承認](sap-solution-detailed-requirements.md#required-abap-authorizations)に関するセクションを参照してください。

1. SAP データ コネクタ用に非ダイアログの RFC/NetWeaver ユーザーを作成し、新しく作成した */MSFTSEN/SENTINEL_CONNECTOR* ロールをアタッチします。

    - ロールをアタッチしたら、ロールのアクセス許可がユーザーに配布されていることを確認します。
    - このプロセスでは、ABAP ユーザーのユーザー名とパスワードを使用する必要があります。 新しいユーザーが作成され、必要なアクセス許可が付与されたら、ABAP ユーザー パスワードを必ず変更してください。

1. SAP NetWeaver RFC SDK 7.50 for Linux on x86_64 64 BIT バージョンをダウンロードして VM に配置します。これはインストール プロセスで必要になります。

    たとえば、SDK を見つけるには、[SAP ソフトウェアのダウンロード サイト](https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=01200314690100002214&V=MAINT) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip** の順に選択します。 **LINUX ON X86_64 65BIT** オプションを必ずダウンロードしてください。 SCP などを使用して、ファイルを VM にコピーします。

    SAP ソフトウェアのダウンロード ページにアクセスするには、SAP ユーザー アカウントが必要です。

1. (省略可能) SAP *Auditlog* ファイルは、システム全体で使用され、複数の SAP クライアントをサポートします。 ただし、Microsoft Sentinel SAP ソリューションの各インスタンスでサポートされる SAP クライアントは 1 つだけです。

    そのため、マルチクライアントの SAP システムがある場合は、データの重複を避けるために、SAP ソリューションをデプロイするクライアントでのみ *Auditlog* ファイルを有効にすることをお勧めします。


## <a name="deploy-a-linux-vm-for-your-sap-data-connector"></a>SAP データ コネクタ用の Linux VM をデプロイする

この手順では、Azure CLI を使用して Ubuntu サーバー 18.04 LTS VM をデプロイし、[システム マネージド ID ](../active-directory/managed-identities-azure-resources/index.yml)を割り当てる方法について説明します。

> [!TIP]
> データ コネクタは、RHEL バージョン 7.7 以降または SUSE バージョン 15 以降にデプロイすることもできます。 OS とパッチのレベルは完全に最新である必要があることに注意してください。
>

Ubuntu VM をデプロイして準備するには、次の手順を実行します。

1. 例として次のコマンドを使用します。 リソース グループ名と VM 名の値を必ず挿入してください。

    ```azurecli
    az vm create  --resource-group [resource group name]   --name [VM Name] --image UbuntuLTS  --admin-username azureuser --data-disk-sizes-gb 10 – --size Standard_DS2 --generate-ssh-keys  --assign-identity
    ```

1. 新しい VM に、次をインストールします。

    - Python バージョン 3.8 以降を備えた [Venv](https://docs.python.org/3.8/library/venv.html)。
    - [Azure CLI](/cli/azure/) バージョン 2.8.0 以降。

> [!IMPORTANT]
> 他の VM と同様に、組織のセキュリティのベスト プラクティスを必ず適用してください。
>

詳細については、「[クイック スタート: Azure CLI を使用して Linux 仮想マシンを作成する](../virtual-machines/linux/quick-create-cli.md)」を参照してください。

## <a name="create-a-key-vault-for-your-sap-credentials"></a>SAP 資格情報のキー コンテナーを作成する

このチュートリアルでは、新しく作成した、または専用の [Azure キー コンテナー](../key-vault/index.yml)を使用して、SAP データ コネクタの資格情報を格納します。

Azure キー コンテナーを作成または専用化するには、次の手順を実行します。

1. 新しい Azure キー コンテナーを作成するか、既存のものを選択して SAP データ コネクタのデプロイ専用にします。

    たとえば、新しいキー コンテナーを作成するには、次のコマンドを実行します。 キー コンテナー リソース グループの名前を必ず使用し、キー コンテナー名を入力してください。

    ```azurecli
    kvgp=<KVResourceGroup>

    kvname=<keyvaultname>

    #Create a key vault
    az keyvault create \
      --name $kvname \
      --resource-group $kvgp
    ```

1. 次のいずれかの方法を使用して、GET、LIST、SET の各アクセス許可を含むアクセス ポリシーを VM のマネージド ID に割り当てます。

    - **Azure portal**:

        1. Azure キー コンテナーで、 **[アクセス ポリシー]**  >  **[アクセス ポリシーの追加 - シークレットのアクセス許可: Get、List、Set]**  >  **[プリンシパルの選択]** の順に選択します。  
        1. [VM の名前](#deploy-a-linux-vm-for-your-sap-data-connector)を入力し、 **[追加]**  >  **[保存]** の順に選択します。

        詳細については、[Key Vault](../key-vault/general/assign-access-policy-portal.md) のドキュメントを参照してください。

    - **Azure CLI**:

        1. 次のコマンドを実行して、[VM のプリンシパル ID](#deploy-a-linux-vm-for-your-sap-data-connector) を取得します。 Azure リソース グループの名前を必ず入力してください。  

            ```azurecli
            VMPrincipalID=$(az vm show -g [resource group] -n [Virtual Machine] --query identity.principalId -o tsv)
            ```  

            次の手順で使用するプリンシパル ID が表示されます。

        1. 次のコマンドを実行して、VM のアクセス許可をキー コンテナーに割り当てます。 リソース グループの名前と、前の手順で返されたプリンシパル ID 値を必ず入力してください。

            ```azurecli
            az keyvault set-policy -n [key vault] -g [resource group] --object-id $VMPrincipalID --secret-permissions get list set
            ```

## <a name="deploy-your-sap-data-connector"></a>SAP データ コネクタをデプロイする

Microsoft Sentinel SAP データ コネクタのデプロイ スクリプトでは、[新しく作成された VM](#deploy-a-linux-vm-for-your-sap-data-connector) に、[必要なソフトウェア](#automatically-installed-software)がインストールされ、次にコネクタがインストールされます。 また、資格情報が[専用のキー コンテナー](#create-a-key-vault-for-your-sap-credentials)に格納されます。

SAP データ コネクタのデプロイ スクリプトは、[Microsoft Sentinel GitHub リポジトリ > DataConnectors > SAP](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh) に保存されています。

SAP データ コネクタ デプロイ スクリプトを実行するには、次の項目が必要です。

- 「[前提条件](#prerequisites)」に記載されている Microsoft Sentinel ワークスペースの詳細。
- 「[前提条件](#prerequisites)」に記載されている SAP システムの詳細。
- sudo 特権を持つ VM ユーザーへのアクセス。
- 「[SAP システムを構成する](#configure-your-sap-system)」で作成した、 **/MSFTSEN/SENTINEL_CONNECTOR** ロールが適用された SAP ユーザー。
- SAP チームの支援。

SAP ソリューション デプロイ スクリプトを実行するには、次の手順を実行します。

1. 次のコマンドを実行して、SAP ソリューションを VM にデプロイします。

    ```azurecli
    wget -O sapcon-sentinel-kickstart.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh && bash ./sapcon-sentinel-kickstart.sh
    ```

1. 画面の指示に従って、SAP とキー コンテナーの詳細を入力し、デプロイを実行します。 デプロイが完了すると、次の確認メッセージが表示されます。

    ```azurecli
    The process has been successfully completed, thank you!
    ```

    Microsoft Sentinel によって、構成済みの期間 (初期化時刻の 24 時間前まで) の SAP ログの取得が開始されます。

1. データ コネクタがデータを送信していることを確認するために、システム ログを確認することをお勧めします。 次を実行します。

    ```bash
    docker logs -f sapcon-[SID]
    ```

## <a name="deploy-sap-security-content"></a>SAP セキュリティ コンテンツをデプロイする

Microsoft Sentinel の **[ソリューション]** および **[ウォッチリスト]** 領域から、[SAP セキュリティ コンテンツ](sap-solution-security-content.md)をデプロイします。

**Microsoft Sentinel - Continuous Threat Monitoring for SAP** ソリューションを使用すると、Microsoft Sentinel の **[データ コネクタ]** 領域に SAP データ コネクタを表示できます。 また、**SAP - System Applications and Products** ブックと SAP 関連の分析ルールもデプロイされます。

SAP 関連のウォッチリストを Microsoft Sentinel ワークスペースに手動で追加します。

SAP ソリューションのセキュリティ コンテンツをデプロイするには、次の手順を実行します。

1. Microsoft Sentinel の左ペインで **[ソリューション (プレビュー)]** を選択します。

    **[ソリューション]** ページに、フィルター処理された検索可能なソリューションの一覧が表示されます。

1. SAP ソリューション ページを開くには、 **[Microsoft Sentinel - Continuous Threat Monitoring for SAP (プレビュー)]** を選択します。

    :::image type="content" source="media/sap/sap-solution.png" alt-text="[Microsoft Sentinel - Continuous Threat Monitoring for SAP (プレビュー)] ソリューション ペインのスクリーンショット。":::

1. ソリューション デプロイ ウィザードを起動するには、 **[作成]** を選択し、ソリューションをデプロイする Azure サブスクリプション、リソース グループ、Log Analytics ワークスペースの詳細を入力します。

1. **[次へ]** を選択して、**データ コネクタ** の **[分析]** タブと **[ブック]** タブを切り替えます。ここでは、このソリューションでデプロイされるコンポーネントについて学習できます。

    ブックの既定の名前は **SAP - System Applications and Products - Preview** です。 必要に応じて、[ブック] タブで変更します。

    詳細については、「[Microsoft Sentinel SAP ソリューション: セキュリティ コンテンツ リファレンス (パブリック プレビュー)](sap-solution-security-content.md)」を参照してください。

1. **[確認と作成] タブ** ペインで、 **[検証に成功しました]** というメッセージが表示されるのを待ってから、 **[作成]** を選択してソリューションをデプロイします。

    > [!TIP]
    > **[テンプレートのダウンロード]** リンクを選択して、ソリューションをコードとしてデプロイすることもできます。

1. デプロイが完了すると、右上に確認メッセージが表示されます。

    新しくデプロイされたコンテンツを表示するには:

    - **脅威の管理** > **ブック** > [組み込みの SAP ブック](sap-solution-security-content.md#built-in-workbooks)を検索する **ブック**。
    - **[構成]**  >  **[分析]** の順に選択し、一連の [SAP 関連の分析ルール](sap-solution-security-content.md#built-in-analytics-rules)を見つけます。

1. 検索、検出規則、脅威ハンティング、および応答プレイブックで使用する、SAP 関連のウォッチリストを追加します。 これらのウォッチリストにより、Microsoft Sentinel SAP Continuous Threat Monitoring ソリューションの構成が提供されます。 次の操作を行います。

    a. Microsoft Sentinel GitHub リポジトリ (https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists ) から SAP ウォッチリストをダウンロードします。  
    b. Microsoft Sentinel の **[ウォッチリスト]** 領域で、Microsoft Sentinel ワークスペースにウォッチリストを追加します。 ダウンロードした CSV ファイルをソースとして使用し、必要に応じてそれらをお使いの環境に合わせてカスタマイズします。  

    [ ![Microsoft Sentinel に追加された SAP 関連のウォッチリスト。](media/sap/sap-watchlists.png) ](media/sap/sap-watchlists.png#lightbox)

    詳細については、「[Microsoft Sentinel ウォッチリストを使用する](watchlists.md)」および[使用可能な SAP ウォッチリスト](sap-solution-security-content.md#available-watchlists)に関するセクションを参照してください。

1. Microsoft Sentinel で、**Microsoft Sentinel Continuous Threat Monitoring for SAP** データ コネクタに移動して接続を確認します。

    [ ![Microsoft Sentinel Continuous Threat Monitoring for SAP データ コネクタ ページのスクリーンショット。](media/sap/sap-data-connector.png) ](media/sap/sap-data-connector.png#lightbox)

    SAP ABAP ログは、Microsoft Sentinel の **[ログ]** ページの **[カスタム ログ]** の下に表示されます。

    [ ![Microsoft Sentinel の [カスタム ログ] 領域にある SAP ABAP ログのスクリーンショット。](media/sap/sap-logs-in-sentinel.png) ](media/sap/sap-logs-in-sentinel.png#lightbox)

    詳細については、「[Microsoft Sentinel SAP ソリューション ログ リファレンス (パブリック プレビュー)](sap-solution-log-reference.md)」を参照してください。


## <a name="update-your-sap-data-connector"></a>SAP データ コネクタを更新する

以前のバージョンの SAP データ コネクタで既に実行されている Docker コンテナーがある場合は、SAP データ コネクタの更新スクリプトを実行して、使用可能な最新の機能を取得します。

Microsoft Sentinel GitHub リポジトリから、関連するデプロイ スクリプトの最新バージョンがインストールされていることを確認します。 

次を実行します。

```azurecli
wget -O sapcon-instance-update.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-instance-update.sh && bash ./sapcon-instance-update.sh
```

マシン上の SAP データ コネクタ Docker コンテナーが更新されます。 

次に示すものなど、使用可能な他の更新プログラムがないか必ず確認してください。

- 関連する SAP 変更要求 ([Microsoft Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR)内)。
- Microsoft Sentinel SAP セキュリティ コンテンツ (**Microsoft Sentinel Continuous Threat Monitoring for SAP** ソリューション内)
- 関連するウォッチリスト ([Microsoft Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists)内)

## <a name="collect-sap-hana-audit-logs"></a>SAP HANA の監査ログを収集する

SAP HANA データベースの監査ログを Syslog で構成している場合は、Syslog ファイルを収集するように Log Analytics エージェントも構成する必要があります。

1. [SAP Launchpad サポート サイト](https://launchpad.support.sap.com/#/notes/0002624117)からアクセスできる *SAP ノート 0002624117* の説明に従って、Syslog を使用するように SAP HANA 監査ログ証跡が構成されていることを確認します。 詳細については、次を参照してください。

    - [SAP HANA の監査証跡 - ベスト プラクティス](https://archive.sap.com/documents/docs/DOC-51098)
    - [監査に関する推奨事項](https://help.sap.com/viewer/742945a940f240f4a2a0e39f93d3e2d4/2.0.05/en-US/5c34ecd355e44aa9af3b3e6de4bbf5c1.html)

1. オペレーティング システムの Syslog ファイルに、関連する HANA データベース イベントがないか確認します。

1. マシンに Log Analytics エージェントをインストールして構成します。

    a. HANA データベース オペレーティング システムに sudo 特権を持つユーザーとしてサインインします。  
    b. Azure portal で、Log Analytics ワークスペースに移動します。 左ペインの **[設定]** で、 **[エージェント管理]**  >  **[Linux サーバー]** の順に選択します。  
    c. **[Linux 用エージェントのダウンロードとオンボード]** で、ボックスに表示されているコードをターミナルにコピーし、スクリプトを実行します。

    Log Analytics エージェントがマシンにインストールされ、ワークスペースに接続されます。 詳細については、「[Linux コンピューターに Log Analytics エージェントをインストールする](../azure-monitor/agents/agent-linux.md)」、および Microsoft GitHub リポジトリの [OMS エージェント for Linux](https://github.com/microsoft/OMS-Agent-for-Linux) を参照してください。

1. **[エージェント管理] > [Linux サーバー]** タブを更新して、**1 台の Linux コンピューターが接続されている** ことを確認します。

1. 左ペインの **[設定]** で、 **[エージェント構成]** を選択し、 **[Syslog]** タブを選択します。

1. **[Add facility]\(ファシリティの追加\)** を選択して、収集するファシリティを追加します。 

    > [!TIP]
    > HANA データベース イベントが保存されるファシリティは、ディストリビューションごとに変わる可能性があるため、すべてのファシリティを追加し、Syslog ログと照合して、関係のないものを削除することをお勧めします。
    >

1. Microsoft Sentinel で、取り込んだログに HANA データベース イベントが表示されるようになったことを確認します。

## <a name="next-steps"></a>次の手順

Microsoft Sentinel SAP ソリューションの詳細を確認します。

- [SNC を使用して Microsoft Sentinel SAP データ コネクタをデプロイする](sap-solution-deploy-snc.md)
- [エキスパートの構成オプション、オンプレミス デプロイ、SAPControl のログ ソース](sap-solution-deploy-alternate.md)
- [Microsoft Sentinel SAP ソリューションの詳細な SAP 要件](sap-solution-detailed-requirements.md)
- [Microsoft Sentinel SAP ソリューションのログ リファレンス](sap-solution-log-reference.md)
- [Microsoft Sentinel SAP ソリューション: 組み込みのセキュリティ コンテンツ](sap-solution-security-content.md)
- [Microsoft Sentinel SAP ソリューションのデプロイのトラブルシューティング](sap-deploy-troubleshoot.md)

詳細については、[Microsoft Sentinel ソリューション](sentinel-solutions.md)に関する記事を参照してください。
