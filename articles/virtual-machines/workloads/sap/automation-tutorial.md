---
title: SAP デプロイ自動化フレームワークを使用したエンタープライズ スケーリング
description: Azure で SAP デプロイ自動化フレームワークを使用して、デプロイに対してエンタープライズ スケーリングを実行する方法。
author: hdamecharla
ms.author: hdamecharla
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: tutorial
ms.service: virtual-machines-sap
ms.openlocfilehash: ab67d4ecf6588b2fd95abfc6702b58a19029e137
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725150"
---
# <a name="enterprise-scale-for-sap-automation-framework-deployment---hands-on-lab"></a>SAP オートメーション フレームワーク のデプロイのエンタープライズ スケーリング - ハンズオン ラボ


このチュートリアルは、[Azure で SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)を使用して、デプロイに対してエンタープライズ スケーリングを実行する方法を示しています。 この例では、Azure Cloud Shell を使用してコントロール プレーン インフラストラクチャをデプロイします。 デプロイ機能仮想マシン (VM) は、残りのインフラストラクチャと SAP HANA 構成を作成します。 このチュートリアルで使用するために、[オートメーション フレームワークの GitHub リポジトリ](https://github.com/Azure/sap-hana/)には、 `sap-level-up` という名前の機能ロックされたブランチがあります。

このラボでは次の作業を行います

> [!div class="checklist"]
> * コントロール プレーンのデプロイ (デプロイ機能インフラストラクチャおよびライブラリ)
> * ワークロード ゾーンのデプロイ (ランドスケープ、システム)
> * BOM のダウンロード/アップロード
> * 標準および SAP 固有の OS 設定を構成する
> * HANA DB をインストールする
> * SCS サーバーをインストールする
> * HANA DB を読み込む
> * プライマリ アプリケーション サーバーをインストールする

オートメーション フレームワークを使用した Azure への SAP のデプロイには、主に 3 つの手順があります。

1. リージョンの準備。 この手順では、指定された Azure リージョンで SAP オートメーション フレームワークをサポートするコンポーネントをデプロイします。 この手順の一部を次に示します。
   1. デプロイ環境の作成
   2. Terraform 状態ファイルの共有ストレージの作成
   3. SAP インストール メディア用の共有ストレージの作成

2. ワークロード ゾーンの準備。 この手順では、仮想ネットワークやキー コンテナーなどのワークロード ゾーン コンポーネントをデプロイします。

3. システムのデプロイ。 この手順には、SAP システムのインフラストラクチャが含まれます。

デプロイ自動化プロセスにはいくつかのワークフローがあります。 ただし、このチュートリアルでは、デプロイを容易に行うための 1 つのワークフローに焦点を当てます。 Bash を使用して、このワークフローである SAP-S4HANA スタンドアロン環境をデプロイできます。 このチュートリアルでは、デプロイの一般的な階層とさまざまなフェーズについて説明します。
### <a name="environment-overview"></a>環境の概要

SAP on Azure デプロイ オートメーション フレームワークには、次の 2 つの主要なコンポーネントがあります。

- デプロイ インフラストラクチャ (コントロール プレーン)
- SAP インフラストラクチャ (SAP ワークロード)

次の図は、コントロール プレーンとアプリケーション プレーンの間の依存関係を示しています。

:::image type="content" source="./media/automation-deployment-framework/control-plane-sap-infrastructure.png" alt-text="コントロール プレーンとアプリケーション プレーン間の依存関係":::

フレームワークでは、インフラストラクチャのデプロイに Terraform を使用し、オペレーティング システムとアプリケーションの構成に Ansible を使用します。 次の図は、コントロール プレーンとワークロード ゾーンの論理的な分離を示しています。

:::image type="content" source="./media/automation-deployment-framework/automation-diagram-full.png" alt-text="SAP デプロイ オートメーション フレームワーク環境を示す図。":::

#### <a name="management-zone"></a>管理ゾーン

管理ゾーンには、他の環境のデプロイ元のコントロール プレーン インフラストラクチャが含まれます。 管理ゾーンがデプロイされた後は、再デプロイが必要になることはほとんどありません。

:::image type="content" source="./media/automation-deployment-framework/control-plane.png" alt-text="ダイアグラム コントロール プレーン。":::

**デプロイ機能** は、SAP オートメーション フレームワークの実行エンジンです。 この事前構成済みの仮想マシン (VM) は、Terraform と Ansible のコマンドの実行に使用されます。

**SAP ライブラリ** は、Terraform 状態ファイル用の永続ストレージと、コントロール プレーン用にダウンロードされた SAP インストール メディアを提供します。

Terraform `.tfvars` 変数ファイルでデプロイ機能とライブラリを構成します。 「[コントロール プレーンの構成](automation-configure-control-plane.md)」を参照してください

#### <a name="workload-zone"></a>ワークロード ゾーン

通常、SAP アプリケーションには複数のデプロイ レベルがあります。 たとえば、開発、品質保証、実稼働のレベルがある場合があります。 SAP デプロイ オートメーション フレームワークでは、これらのレベルをワークロード ゾーンと呼びます。

:::image type="content" source="./media/automation-deployment-framework/workload-zone.png" alt-text="ワークロード ゾーン。":::

**SAP ワークロード ゾーン** には、SAP VM のネットワーク コンポーネントと共有コンポーネントが含まれています。 これらのコンポーネントには、ルート テーブル、ネットワーク セキュリティ グループ、仮想ネットワーク (VNet) が含まれます。 ランドスケープは、デプロイを異なる環境に分割する機会を提供します。 [ワークロード ゾーンの構成](automation-configure-workload-zone.md)に関するページを参照してください

システムのデプロイは、Web、アプリ、データベースのレベルなどを含む、SAP アプリケーションを実行する仮想マシンで構成されます。 [SAP システムの構成](automation-configure-system.md)に関するページ参照してください

## <a name="hands-on-lab"></a>ハンズオン ラボ

### <a name="prerequisites"></a>前提条件

[SAP デプロイ オートメーション フレームワーク リポジトリ](https://github.com/Azure/sap-hana)は、GitHub で利用できます。

> [!IMPORTANT]
> 開始する前に、必ず既定のブランチから **sap-level-up** に変更してください。

デプロイ機能に接続するには SSH クライアントが必要です。 使いやすいと思う SSH クライアントを使用します。

#### <a name="review-the-azure-subscription-quota"></a>Azure サブスクリプション クォータを確認する

Microsoft Azure Subscription に、選択されたリージョンの DdSV4 & EdsV4 ファミリ SKU 用の十分なコア クォータがあることを確認してください。 VM ファミリで使用可能なコア数は、それぞれ約 50 で十分です。

#### <a name="suser-account-for-sap-software-download"></a>SAP ソフトウェア ダウンロード用の S.User アカウント

SAP ソフトウェアをダウンロードするには、ソフトウェアのダウンロード特権を持つ有効な SAP ユーザー アカウント (SAP ユーザーまたは S ユーザーのアカウント) が必要です。

## <a name="set-up-cloud-shell"></a>Cloud Shell の設定

[Azure Cloud Shell](https://shell.azure.com) に移動します

Azure アカウントにログインします。

```cloudshell-interactive
az login
```

ログインを認証します。 メッセージが表示されるまでウィンドウを閉じないでください。

必要に応じて、アクティブなサブスクリプションを変更します。
  
```cloudshell-interactive
az account set -s <Subscription ID>
```

アクティブなサブスクリプションが変更されたことを確認します。

```cloudshell-interactive
az account list --query "[?isDefault]"
```
**or**

```cloudshell-interactive
az  account list -o table | grep True
```

デプロイ フォルダーを作成し、リポジトリを複製します。

```cloudshell-interactive
mkdir -p ~/Azure_SAP_Automated_Deployment

cd ~/Azure_SAP_Automated_Deployment

git clone https://github.com/Azure/sap-hana.git \
    --single-branch --branch=sap-level-up

cd ~/Azure_SAP_Automated_Deployment/sap-hana
```

必要に応じて、Terraform と Cloud Shell のインスタンスで使用できる Azure コマンド ライン インターフェイス (Azure CLI) のバージョンを確認します。

```bash
./util/check_workstation.sh
```

オートメーション フレームワークを実行するには、次のバージョンに更新します。

1. `az` バージョン 2.28.0 以降

1. `terraform` バージョン 1.0.8 以降。 必要に応じて、[Terraform の手順を使用して アップグレード](https://www.terraform.io/upgrade-guides/0-12.html)します。


## <a name="create-service-principal"></a>サービス プリンシパルの作成

SAP オートメーション デプロイ フレームワークでは、デプロイにサービス プリンシパルが使用されます。 コントロール プレーンのデプロイのサービス プリンシパルを次のように作成します。 サービス プリンシパルを作成するアクセス許可を持つアカウントを使用してください。

> [!NOTE] 
> サービス プリンシパルの名前を選択する場合は、その名前が Azure テナント内で一意なものであることを確認してください。


サービス プリンシパルへ共同作成者とユーザー アクセス管理者のアクセス許可を付与します。

```cloudshell-interactive
az ad sp create-for-rbac --role="Contributor" \
  --scopes="/subscriptions/<subscriptionID>" \
  --name="<environment>-Deployment-Account"
```

出力結果を確認します。 次に例を示します。

```json
{
    "appId": "<AppID>",
    "displayName": "<environment>-Deployment-Account ",
    "name": "<AppID>,
    "password": "<AppSecret>",
    "tenant": "<Tenant ID>"
}
```

出力の詳細をコピーします。 `appId`、`password`、`Tenant` の各フィールドの値を必ず保存してください。

出力は次のパラメーターにマップされます。 これらのパラメーターは、後の手順でオートメーション コマンドと一緒に使用します。

| **パラメーターの入力名** | **出力名** |
|--------------------------|-----------------|
| `spn_id`                 | `appId`         |
| `spn_secret`             | `password`      |
| `tenant_id`              | `tenant`        |

サービス プリンシパルに **ユーザー アクセス管理者** ロールを割り当てます。

```bash
az role assignment create --assignee <appId> \
  --role "User Access Administrator"
```

## <a name="view-configuration-files"></a>構成ファイルを表示する

>[!IMPORTANT]
> GitHub リポジトリは常に読み取り専用として扱います。 `WORKSPACES` フォルダーのコピーで作業してし、 構成を変更します。 この方法では、リポジトリが変更された場合に、構成が安定した状態で維持されます。

ローカル ワークスペース ディレクトリにサンプル構成をコピーします。

```bash
cd ~/Azure_SAP_Automated_Deployment

cp -Rp ./sap-hana/deploy/samples/WORKSPACES ./
```

Cloud Shell から VS Code を開く 

```cloudshell-interactive
code .
```

**[ワークスペース]** ディレクトリを展開します。 サブフォルダーには、**DEPLOYER**、**LANDSCAPE**、**LIBRARY**、**SYSTEM**、**BOMS** の 5 つがあります。 これらの各フォルダーを展開して、リージョンのデプロイ構成ファイルを検索します。

使用している Azure リージョンに対応する、適切な 4 文字のコードを見つけます。

| リージョン名        | 地域コード |
|--------------------|-------------|
| オーストラリア東部     | AUEA        |
| カナダ中部     | CACE        |
| 米国中部         | CEUS        |
| 米国東部            | EAUS        |
| 北ヨーロッパ       | NOEU        |
| 南アフリカ北部 | SANO        |
| 東南アジア     | SOEA        |
| 英国南部           | UKSO        |
| 米国西部 2          | WES2        |

適切なサブフォルダーにある Terraform 変数ファイルを見つけます。 たとえば、**デプロイ機能** terraform 変数ファイルは次のようになります。

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example, PROD and NP.
environment="MGMT"
# The location/region value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"

# management_network_address_space is the address space for management virtual network
management_network_address_space="10.10.20.0/25"
# management_subnet_address_prefix is the address prefix for the management subnet
management_subnet_address_prefix="10.10.20.64/28"
# management_firewall_subnet_address_prefix is the address prefix for the firewall subnet
management_firewall_subnet_address_prefix="10.10.20.0/26"

deployer_enable_public_ip=true
firewall_deployment=true
```

今後デプロイ時に編集するために、Terraform 変数ファイルの場所をメモしておいてください。

## <a name="deploy-control-plane"></a>コントロールプレーンのデプロイ

[Prepare_region](bash/automation-prepare-region.md)スクリプトを使用して、デプロイ機能とライブラリをデプロイします。 これらのデプロイ機能部分は、選択されたオートメーション領域のコントロールプレーンを構成します。 

- デプロイは、インフラストラクチャのデプロイ、状態の更新、Terraform 状態ファイルのライブラリ ストレージ アカウントへのアップロードのサイクルを通じて行われます。 これらの手順はすべて 1 つのデプロイ スクリプトにパッケージ化されています。 このスクリプトには、デプロイ機能とライブラリの構成ファイルの場所と、次のようなその他のパラメーターが必要です。

たとえば、前に説明したように 4 文字の名前 `NOEU` を使用して、デプロイ場所として **[北ヨーロッパ]** を選択します。 サンプルのデプロイ機能構成ファイル `MGMT-NOEU-DEP00-INFRASTRUCTURE.tfvars` は、`~/Azure_SAP_Automated_Deployment/WORKSPACES/DEPLOYER/MGMT-NOEU-DEP00-INFRASTRUCTURE` フォルダーにあります。

サンプルの SAP ライブラリ構成ファイル `MGMT-NOEU-SAP_LIBRARY.tfvars` は、`~/Azure_SAP_Automated_Deployment/WORKSPACES/LIBRARY/MGMT-NOEU-SAP_LIBRARY` フォルダーにあります。

1. デプロイ機能と SAP ライブラリを作成します。 また、サービス プリンシパルの詳細をデプロイ キー コンテナーに追加します。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret="<password>"
tenant_id=<tenant>

export DEPLOYMENT_REPO_PATH="${HOME}/Azure_SAP_Automated_Deployment/sap-hana"
export ARM_SUBSCRIPTION_ID="${subscriptionID}"

${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                     \
    --deployer_parameter_file DEPLOYER/MGMT-NOEU-DEP00-INFRASTRUCTURE/MGMT-NOEU-DEP00-INFRASTRUCTURE.tfvars  \
    --library_parameter_file LIBRARY/MGMT-NOEU-SAP_LIBRARY/MGMT-NOEU-SAP_LIBRARY.tfvars                      \
    --subscription $subscriptionID                                                                           \
    --spn_id $appID                                                                                          \
    --spn_secret $spn_secret                                                                                 \
    --tenant_id $tenant_id
```

認証の問題が発生した場合は、`az logout` を実行してログアウトします。 `token-cache` をクリアした後、`az login` を実行して再認証します。

オートメーション フレームワークによって Terraform 操作 `plan` と `apply` が実行されるのを待ちます。

デプロイ機能のデプロイは約 15 分から 20 分間実行される可能性があります。

[Azure ポータル](https://portal.azure.com)にアクセスします。  

**[リソース グループ]** を選択します。 デプロイ機能のインフラストラクチャとライブラリの新しいリソースグループを探します。 たとえば、`MGMT-[region]-DEP00-INFRASTRUCTURE` や `MGMT-[region]-SAP_LIBRARY`す。

デプロイ機能と SAP ライブラリのリソースグループの内容を以下に示します。

:::image type="content" source="media/automation-tutorial/deployer-resource-group.png" alt-text="デプロイ機能リソース":::
    
:::image type="content" source="media/automation-tutorial/sap-library-resource-group.png" alt-text="ライブラリ リソース":::

Terraform 状態ファイルは現在、名前に 'tfstate' が含まれているストレージアカウント内にあります。 ストレージ アカウントには、デプロイ機能およびライブラリ状態ファイルを含む ' tfstate' という名前のコンテナーがあります。 コントロール プレーンのデプロイが成功した後の 'tfstate' コンテナーの内容の一覧を次に示します。
    
:::image type="content" source="media/automation-tutorial/terraform-state-files.png" alt-text="コントロールプレーンの tfstate ファイル":::

### <a name="common-issues-and-solutions"></a>一般的な問題と解決方法

デプロイ機能モジュールの作成で次のエラーが発生した場合は、スクリプトを実行するときに、**ワークスペース** ディレクトリにいることを確認してください。
  
```text
Incorrect parameter file.
The file must contain the environment attribute!!
```

次のエラーは一時的なエラーです。 同じコマンド `prepare_region.sh` を再実行します。
  
 ```text
Error: file provisioner error
..
timeout - last error: dial tcp
```


スクリプト `prepare_region.sh` の実行直後に認証の問題が発生する場合は、次のように実行します。

```bash
az logout

az login
```

## <a name="connect-to-deployer-vm"></a>デプロイ機能 VM へ接続する

リージョンの準備が完了すると、Terraform 状態ファイルがリモート バックエンド `azurerm` に移動します。 デプロイ機能 VM に接続するためのすべてのシークレットは、デプロイ機能のリソース グループ内のキー コンテナーで利用できます。

デプロイ機能 VM に接続できることを確認します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[キー コンテナー]** を選択または検索します。

1. **[キー コンテナー]** ページで、デプロイ機能キー コンテナーを見つけます。 名前は `MGMT[Region]DEP00user` で始まります。 必要な場合は、**リソース グループ** でフィルター処理できます。

1. キー コンテナーのメニューで、 **[設定]** の下にある **[シークレット]** を選択します。

1. シークレットのページで、現在のバージョンを選択します。 **シークレット値** をコピーします。

1. プレーン テキスト エディターを開きます。 シークレット値をコピーします。 
 
1. SSH キーが保持されている場所にファイルを保存します。 たとえば、「 `C:\\Users\\<your-username>\\.ssh` 」のように入力します。
 
1. ファイルを保存します。 **[保存の種類]** を入力するように求められたら、**SSH** がオプションでない場合は **[すべてのファイル]** を選択します。 たとえば、 `deployer.ssh`を使用します。

1. 任意の SSH クライアントを介してデプロイ機能 VM に接続します。 先ほどメモしたパブリック IP アドレスと、ダウンロードした SSH キーを使用します。 PuTTY を使用している場合は、PuTTYGen を使用して最初に SSH キー ファイルを変換します。 

> [!NOTE] 
>既定のユーザー名は *azureadm* です

- デプロイ機能 VM に接続すると、ワークロード ゾーンをプロビジョニングできるようになります。


> [!IMPORTANT]
> 残りのタスクは、デプロイ機能で実行する必要があります

## <a name="get-sap-software-using-the-bom"></a>BOM を使用した SAP ソフトウェアの取得

オートメーション フレームワークには、SAP の部品表 (BOM) をダウンロードするためのツールが用意されています。 SAP ライブラリは、SAP のデプロイに必要なすべてのメディアのアーカイブとして機能します。

SAP の部品表 (BOM) は、SAP のメンテナンス プランナーを模倣しています。 関連する製品識別子とダウンロード Url のパッケージがあります。 デプロイ機能 VM は、処理済みの BOM を読み取り、ストレージ アカウントからファイルをダウンロードして SAP Central Services (SCS) にインストールします。

BOM ファイルのサンプル抽出は次のようになります。

```yaml

---
name:    'S41909SPS03_v0005ms'
target:  'S/4 HANA 1909 SPS 03'
version: 6

product_ids:
  dbl:       NW_ABAP_DB:S4HANA1909.CORE.HDB.ABAP
  scs:       NW_ABAP_ASCS:S4HANA1909.CORE.HDB.ABAP
  scs_ha:    NW_ABAP_ASCS:S4HANA1909.CORE.HDB.ABAPHA
  pas:       NW_ABAP_CI:S4HANA1909.CORE.HDB.ABAP
  pas_ha:    NW_ABAP_CI:S4HANA1909.CORE.HDB.ABAPHA
  app:       NW_DI:S4HANA1909.CORE.HDB.PD
  app_ha:    NW_DI:S4HANA1909.CORE.HDB.ABAPHA
  web:       NW_Webdispatcher:NW750.IND.PD
  ers:       NW_ERS:S4HANA1909.CORE.HDB.ABAP
  ers_ha:    NW_ERS:S4HANA1909.CORE.HDB.ABAPHA

materials:
  dependencies:
    - name:     HANA_2_00_055_v0004ms

  media:
    # SAPCAR 7.22
    - name:         SAPCAR
      archive:      SAPCAR_1010-70006178.EXE
      checksum:     dff45f8df953ef09dc560ea2689e53d46a14788d5d184834bb56544d342d7b
      filename:     SAPCAR
      permissions:  '0755'
      url:          https://softwaredownloads.sap.com/file/0020000002208852020

    # Kernel
    - name:         "Kernel Part I ; OS: Linux on x86_64 64bit ; DB: Database independent"
```


この例の構成では、リソース グループは `MGMT-NOEU-DEP00-INFRASTRUCTURE` です。 デプロイ機能キー コンテナー名には、`MGMTNOEUDEP00user` が含まれます。 この情報は、デプロイ機能のキー コンテナー シークレットを構成するために使用します。

SAP ユーザー アカウントのユーザー名を持つシークレットを追加します。 `<keyvault-name>` の部分は、デプロイ機能キー コンテナーの名前に置き換えます。 また、`<sap-username>` も SAP ユーザー名に置き換えます。

```bash
az keyvault secret set --name "S-Username" --vault-name "<keyvault-name>" --value "<sap-username>";
```

SAP ユーザー アカウントのパスワードを持つシークレットを追加します。 `<keyvault-name>` をデプロイ機能キー コンテナーの名前に、`<sap-password>` を SAP パスワードに置き換えます。

```bash
az keyvault secret set --name "S-Password" --vault-name "<keyvault-name>" --value "<sap-password>";
```

次に、ダウンロード プロセス用に SAP パラメーターファイルを構成します。 次に、Ansible プレイブックを使用して、SAP ソフトウェアをダウンロードします。 次のコマンドを実行します。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/BOMS

vi sap-parameters.yaml
```
  
`kv_name parameter` で、デプロイ機能リソース グループのキー コンテナーの名前を入力します
  
  ファイルは次の構成例のようになります。

```yaml

bom_base_name:                 S41909SPS03_v0006ms
kv_name:                       MGMTNOEUDEP00user99F 

```
    
Ansible プレイブックを実行します。 プレイブックを実行する方法の 1 つとして、[検証コントロール テスト] メニューを使用する方法があります。 検証コントロールのテスト メニュー スクリプトを実行します。
  
```bash
~/Azure_SAP_Automated_Deployment/sap-hana/deploy/ansible/validator_test_menu.sh
```
  
実行するプレイブックを選択します。
  
```bash
1) BoM Downloader
3) Quit
Please select playbook:
```

プレイブック `1) BOM Downloader` を選択して、BOM ファイルに記載されている SAP ソフトウェアをストレージ アカウントにダウンロードします。 `sapbits` コンテナーにインストール用のすべてのメディアが備わっていることを確認します。
    
## <a name="collect-workload-zone-information"></a>ワークロード ゾーン情報の収集

テキスト エディターで次の情報を収集します。

  - ライブラリ リソースグループ内の Terraform 状態ファイル ストレージ アカウントの名前。
    - 上記の例では、リソース グループは *MGMT-NOEU-SAP_LIBRARY* になります。
    - ストレージ アカウントの名前には、 *mgmtnoeutfstate* が含まれます。

  - デプロイ機能状態ファイルの名前は、[ライブラリ リソース グループ] にあります
    - ライブラリ リソース グループ -> 状態ストレージ アカウント -> コンテナー -> tfstate-> の順に進み、デプロイ機能状態ファイルの **名前** をコピーします。
    - 上の例から、BLOB の名前は *MGMT-NOEU-DEP00-INFRASTRUCTURE.terraform.tfstate* になります

  - デプロイ機能リソース グループのキーコンテナーの名前。
    - 上記の例では、リソース グループは *MGMT-NOEU-DEP00-INFRASTRUCTURE* になります。
    - キー コンテナーの名前には、*MGMTNOEUDEP00user* が含まれます。

デプロイ機能 VM のパブリック IP アドレス。 デプロイ機能のリソース グループに移動し、デプロイ機能 VM を開いて、パブリック IP アドレスをコピーします。
  
## <a name="prepare-the-workload-zone-deployment"></a>ワークロード ゾーンのデプロイを準備する

次の手順のために、デプロイ機能 VM に接続します。 これで、リポジトリのコピーができました。

ホーム ディレクトリの *Azure_SAP_Automated_Deployment* フォルダーに移動します。 既存の **ワークスペース** フォルダーを削除します。 後の例でコピーします。

```bash
rm -rf WORKSPACES
```

**sap-hana** フォルダーにアクセスします。 **sap-level-up** ブランチを確認してください。
  
```bash
cd ~/Azure_SAP_Automated_Deployment/sap-hana/

git checkout sap-level-up
```

リポジトリからサンプル構成ファイルをコピーします。

```bash
cd ~/Azure_SAP_Automated_Deployment/

cp -Rp ./sap-hana/deploy/samples/WORKSPACES ./
```

## <a name="deploy-the-workload-zone"></a>ワークロード ゾーンをデプロイする


SAP ワークロード ゾーンをデプロイするには、 [install_workloadzone](bash/automation-install_workloadzone.md) スクリプトを使用します。 

デプロイ機能 VM で、`Azure_SAP_Automated_Deployment` フォルダーに移動します。
  
```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-XXXX-SAP01-INFRASTRUCTURE
```

例の領域 'northeurope' から、フォルダーは次のようになります。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-NOEU-SAP01-INFRASTRUCTURE
```
   
ワークロード ゾーン構成ファイルを開き、必要に応じてネットワークの論理名をネットワーク名と一致するように変更します。

ここでは、前の手順で収集した詳細情報が必要になります。 詳細情報を以下に示します。
  - デプロイ機能 tfstate ファイルの名前 (tfstate コンテナーにあります)
  - tfstate ストレージ アカウントの名前
  - デプロイ機能キー コンテナーの名前

ワークロード ゾーンのデプロイを開始します。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-NOEU-SAP01-INFRASTRUCTURE

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret="<password>"
tenant_id=<tenant>
storageaccount=<storageaccountName>
statefile_subscription=<subscriptionID>
key_vault=<vaultID>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh              \
    --parameterfile ./DEV-NOEU-SAP01-INFRASTRUCTURE.tfvars                  \
    --deployer_environment MGMT                                             \
    --deployer_tfstate_key MGMT-NOEU-DEP00-INFRASTRUCTURE.terraform.tfstate \
    --keyvault $key_vault                                                   \
    --storageaccountname $storageaccount                                    \
    --state_subscription $statefile_subscription                            \
    --subscription $subscriptionID                                          \
    --spn_id $appID                                                         \
    --spn_secret $spn_secret                                                \
    --tenant_id $tenant_id
```

ワークロード ゾーンのデプロイが自動的に開始します。

デプロイが完了するまで待ちます。 新しいリソース グループが Azure portal に表示されます。

## <a name="deploy-sap-system-infrastructure"></a>SAP システム インフラストラクチャのデプロイ

ワークロードゾーンが完成したら、SAP システム インフラストラクチャ リソースをデプロイできます。 SAP システムによって、SAP アプリケーション用の VM とサポート コンポーネントが作成されます。
SAP システムをデプロイするには、 [installler.sh](bash/automation-installer.md) スクリプトを使用します。 

SAP システムによって、次のデプロイが行われます。

- データベース層。データベース VM、そのディスク、Azure Standard Load Balancer がデプロイされます。 この層で HANA データベースまたは AnyDB データベースを実行できます。
- SCS 層。顧客が定義した数の VM と Azure Standard Load Balancer がデプロイされます。
- アプリケーション層。 VM とそのディスクがデプロイされます。
- Web ディスパッチャー層。

SAP システムをデプロイします。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-XXXX-SAP01-X00

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh \
  --parameterfile DEV-XXXX-SAP01-X00.tfvars         \
  --type sap_system                                 \
  --auto-approve
```
  
`northeurope` の例のデプロイ コマンドは次のようになります。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-NOEU-SAP01-X00

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh  \
  --parameter_file DEV-NOEU-SAP01-X00.tfvars         \
  --type sap_system                                  \
  --auto-approve
```

システム リソース グループが Azure portal に存在するようになったことを確認します。

## <a name="sap-application-installation"></a>SAP アプリケーションのインストール

SAP アプリケーションのインストールは、Ansible プレイブックを通じて行われます。 

[システムのデプロイ] フォルダーに移動します。

```bash
cd ~/Azure_ SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-NOEU-SAP01-X00/
```

現在のフォルダーに `sap-parameters.yaml`、`SID_host.yaml` のファイルがあることを確認します。

スタンドアロンの SAP S/4HANA システムの場合、順番に実行するプレイブックが 8 つあります。 メニュー システムを使用して、次のプレイブックをトリガーできます。 

プレイブックをトリガーして実行します。

### <a name="playbook-os-config"></a>プレイブック: OS 構成

このプレイブックを選択すると、すべてのマシンで汎用 OS 構成のセットアップが実行されます。これには、ソフトウェア リポジトリ、パッケージ、サービスなどの構成が含まれます。

### <a name="playbook-sap-specific-os-config"></a>プレイブック: SAP 固有の OS 構成

このプレイブックを選択すると、すべてのマシンで SAP OS 構成のセットアップが実行されます。これには、ボリューム グループとファイル システムの作成、ソフトウェア リポジトリ、パッケージ、サービスの構成が含まれます。

### <a name="playbook-bom-processing"></a>プレイブック: BOM 処理

このプレイブックを選択すると、SCS 仮想マシンに SAP ソフトウェアがダウンロードされます。 
    
### <a name="playbook-hana-db-install"></a>プレイブック: HANA DB のインストール

このプレイブックでは、HANA データベース インスタンスをインストールします。

### <a name="playbook-scs-install"></a>プレイブック: SCS のインストール
  
このプレイブックでは、SAP セントラル サービスをインストールします。 高可用性が構成されている場合は、プレイブックでも、ERS インスタンスをインストールして、Pacemaker を構成します。

### <a name="playbook-db-load"></a>プレイブック: DB の読み込み

このプレイブックでは、プライマリ アプリケーション サーバーからデータベースの読み込みタスクを呼び出します。

### <a name="playbook-pas-install"></a>プレイブック: PAS のインストール

このプレイブックでは、プライマリ アプリケーション サーバーをインストールします。
  
### <a name="playbook-app-install"></a>プレイブック: アプリのインストール

このプレイブックでは、追加のアプリケーション サーバーをインストールします。

これで、スタンドアロンの HANA システムがデプロイおよび構成されました。高可用性 SAP HANA データベースを構成する必要がある場合は、HANA HA プレイブックを実行します

### <a name="playbook-hana-ha-playbook"></a>プレイブック: Hana HA プレイブック

このプレイブックでは、HANA データベースの HANA システム レプリケーション (HSR) と Pacemaker を構成します。

## <a name="clean-up-installation"></a>インストールのクリーンアップ

> [!NOTE]
> 完了後に、このチュートリアルから SAP インストールをクリーンアップすることが重要です。 そうしないと、リソースに関連するコストが引き続き発生します。

デプロイした SAP インフラストラクチャ全体を削除するには、次のことを行う必要があります。

> [!div class="checklist"]
> * SAP システム インフラストラクチャ リソースの削除
> * すべてのワークロード ゾーンを削除する (横)
> * コントロール プレーンを削除する

デプロイ機能 VM からの SAP インフラストラクチャ リソースとワークロードゾーンの削除を実行します。 Cloud Shell からコントロール プレーンの削除を実行します。

開始する前に、Azure アカウントにログインします。 次に、正しいサブスクリプションを使用していることを確認します。

### <a name="remove-sap-infrastructure"></a>SAP インフラストラクチャの削除

`SYSTEM` フォルダー内の `DEV-NOEU-SAP01-X00` サブフォルダーに移動します。 その後、次のコマンドを実行します。
  
```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-NOEU-SAP01-X00

${DEPLOYMENT_REPO_PATH}/deploy/scripts/remover.sh          \
      --parameter_file DEV-NOEU-SAP01-X00.tfvars           \
      --type sap_system
```

### <a name="remove-sap-workload-zone"></a>SAP ワークロードゾーンの削除

`LANDSCAPE` フォルダー内の `DEV-XXXX-SAP01-INFRASTRUCTURE` サブフォルダーに移動します。 次のコマンドを実行します。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-NOEU-SAP01-INFRASTRUCTURE

${DEPLOYMENT_REPO_PATH}/deploy/scripts/remover.sh          \
      --parameter_file DEV-NOEU-SAP01-INFRASTRUCTURE.tfvars           \
      --type sap_landscape
```

### <a name="remove-control-plane"></a>コントロール プレーンの削除

[Cloud Shell](https://shell.azure.com) にサインインします。

`WORKSPACES` フォルダーに移動します。
  
```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/
```

次の 2 つの環境変数をエクスポートします。

```bash
export DEPLOYMENT_REPO_PATH=~/Azure_SAP_Automated_Deployment/sap-hana

export ARM_SUBSCRIPTION_ID=<subscriptionID>
```

次のコマンドを実行します。

```bash
${DEPLOYMENT_REPO_PATH}/deploy/scripts/remove_region.sh                                                    \
  --deployer_parameter_file DEPLOYER/MGMT-NOEU-DEP00-INFRASTRUCTURE/MGMT-NOEU-DEP00-INFRASTRUCTURE.tfvars  \
  --library_parameter_file LIBRARY/MGMT-NOEU-SAP_LIBRARY/MGMT-NOEU-SAP_LIBRARY.tfvars
```

すべてのリソースがクリーンアップされたことを確認します。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [コントロール プレーンを構成する](automation-configure-control-plane.md)


