---
title: Azure で自動化フレームワークを使用して SAP のデプロイを計画する
description: Azure で SAP デプロイ自動化フレームワークを使用するための準備をします。 手順には、資格情報の管理、DevOps 構造、デプロイ シナリオの計画が含まれます。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 5f31987c1ce3cdde11cf97fa994ed8c030a0020e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725177"
---
# <a name="plan-your-deployment-of-sap-automation-framework"></a>SAP 自動化フレームワークのデプロイを計画する

[Azure での SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)の実行には、いくつかの考慮事項があります。これには、デプロイ資格情報の管理、仮想ネットワークの設計などのトピックが含まれます。

一般的な SAP on Azure の設計に関する考慮事項については、「[SAP 導入シナリオの概要](/azure/cloud-adoption-framework/scenarios/sap)」を参照してください

> [!NOTE]
> Terraform のデプロイでは、[SAP デプロイ自動化フレームワーク リポジトリ](https://github.com/Azure/sap-hana/)から Microsoft が提供する Terraform テンプレートを使用します。 このテンプレートでは、デプロイを実行するために、システム固有の情報を含むパラメーター ファイルを使用します。

## <a name="credentials-management"></a>資格情報の管理

自動化フレームワークでは、デプロイに Azure Active Directory (Azure AD) の[サービス プリンシパル](#service-principal-creation)を使用します。 [ワークロード ゾーン](#workload-zone-structure)ごとに異なるデプロイ資格情報を使用できます。 フレームワークでは、これらの資格情報を Azure Key Vault の[配置機能の](automation-deployment-framework.md#deployment-components)キー コンテナーに保持します。 その後、フレームワークではデプロイ処理中にこれらの資格情報を動的に取得します。

自動化フレームワークでは、VM の作成時に提供された既定の仮想マシン (VM) アカウントの資格情報も使用されます。 これらの資格情報を次に示します。

| 資格情報 | スコープ | Storage | 識別子 | 説明 |
| ---------- | ----- | ------- | ---------- | ----------- |
| ローカル ユーザー | 配置機能 | - | 現在のユーザー | 配置機能をブートストラップします。 |
| [サービス プリンシパル](#service-principal-creation) | 環境 | 配置機能のキー コンテナー | 環境 ID | デプロイのアクティビティを行います。 |
| VM の資格情報 | 環境 | ワークロードのキー コンテナー | 環境 ID | 既定の VM ユーザーの情報を設定します。 |

### <a name="service-principal-creation"></a>サービス プリンシパルの作成

サービス プリンシパルを作成します。

1. サービス プリンシパルを作成するための適切な権限を持つアカウントを使用して、[Azure コマンド ライン インターフェイス (Azure CLI)](/cli/azure/) にサインインします。
1. `az ad sp create-for-rbac` コマンドを実行して、新しいサービス プリンシパルを作成します。 `--name` には説明名を使用してください。 次に例を示します。
    ```azurecli
    az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --name="DEV-Deployment-Account"
    ```
1. 出力をメモします。 次のステップで、アプリケーション ID (`appId`)、パスワード (`password`)、テナント ID (`tenant`) が必要となります。 次に例を示します。
    ```json
    {
        "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "displayName": "DEV-Deployment-Account",
        "name": "http://DEV-Deployment-Account",
        "password": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```
1. サービス プリンシパルにユーザー アクセス管理者ロールを割り当てます。 次に例を示します。 
    ```azurecli
    az role assignment create --assignee <your-application-ID> --role "User Access Administrator"
    ```

詳細については、[サービス プリンシパルの作成に関する Azure CLI のドキュメント](/cli/azure/create-an-azure-service-principal-azure-cli)を参照してください

## <a name="devops-structure"></a>DevOps の構造

Terraform 自動化テンプレートは、[SAP デプロイ自動化フレームワーク リポジトリ](https://github.com/Azure/sap-hana/)にあります。 ほとんどのユース ケースでは、このリポジトリを読み取り専用と見なし、Terraform テンプレートを変更しないようにしてください。

独自のパラメーター ファイルの場合は、管理しているソース管理リポジトリにこれらのファイルを保持することをお勧めします。 [SAP デプロイ自動化フレームワーク リポジトリ](https://github.com/Azure/sap-hana/)と独自のリポジトリを同じルート フォルダーに複製します。 次に、[適切なフォルダー構造を作成します](#folder-structure)。

> [!IMPORTANT]
> パラメーター ファイルの名前が Terraform 状態ファイルの名前になります。 このため、必ず一意のパラメーター ファイル名を使用してください。

### <a name="folder-structure"></a>フォルダー構造

次のフォルダー階層の例では、自動化フレームワーク ファイルと共に構成ファイルを構成する方法を示しています。 最初の最上位レベルのフォルダー (**sap-hana** と呼ばれます) には、ほとんどのユース ケースで変更する必要のない自動化フレームワークのファイルがあります。 2 番目の最上位フォルダー (**WORKSPACES** と呼ばれます) には、デプロイ設定の構成ファイルを含むサブフォルダーがあります。

| [フォルダー名] | 内容 | 説明 |
| ----------- | -------- | ----------- |
| なし (ルート レベル) | 構成ファイル、テンプレート ファイル | このデプロイ環境から管理しているすべてのシステムのルート フォルダー。 |
| CONFIGURATION | 共有されている構成ファイル | 複数の場所からカスタム構成ファイルを参照するための共有フォルダー。 たとえば、カスタム ディスク サイズ設定構成ファイルなどです。 |
| DEPLOYER | 配置機能の構成ファイル | 環境で管理されるすべてのデプロイの[配置機能構成ファイル](automation-configure-control-plane.md)を含むフォルダー。 各サブフォルダーには、**環境 - リージョン - 仮想ネットワーク** の名前付け規則に従って名前を付けます。 たとえば、**PROD-WEEU-DEP00-INFRASTRUCTURE** です。 |
| LIBRARY | SAP ライブラリの構成ファイル | 環境で管理されるすべてのデプロイの [SAP ライブラリ構成ファイル](automation-configure-control-plane.md)を含むフォルダー。 各サブフォルダーには、**環境 - リージョン - 仮想ネットワーク** の名前付け規則に従って名前を付けます。 たとえば、**PROD-WEEU-SAP-LIBRARY** です。 |
| LANDSCAPE | ランドスケープ デプロイ用の構成ファイル | 環境で管理される[すべてのワークロード ゾーンの構成ファイル](automation-deploy-workload-zone.md)を含むフォルダー。 各サブフォルダーには、**環境 - リージョン - 仮想ネットワーク** の名前付け規則に従って名前を付けます。 たとえば、**PROD-WEEU-SAP00-INFRASTRUCTURE** です。 |
| SYSTEM | SAP システムの構成ファイル | 環境で管理される[すべての SAP システム ID (SID) の構成ファイル](automation-configure-system.md)を含むフォルダー。 各サブフォルダーには、**環境 - リージョン - 仮想ネットワーク - SID** の名前付け規則に従って名前を付けます。 たとえば、**PROD-WEEU-SAPO00-ABC** です。 |

:::image type="content" source="./media/automation-plan-deployment/folder-structure.png" alt-text="SAP HANA と複数のワークロード環境の個別のフォルダーを示す、フォルダー構造の例のスクリーンショット。":::

## <a name="supported-deployment-scenarios"></a>サポートされているデプロイ シナリオ

自動化フレームワークでは、[新規と既存の両方のシナリオへのデプロイ](automation-new-vs-existing.md)がサポートされます。

## <a name="azure-regions"></a>Azure Azure リージョン

ソリューションをデプロイする前に、使用する Azure リージョンを検討することが重要です。 特定のシナリオに応じて、さまざまな Azure リージョンがスコープに含まれる場合があります。

自動化フレームワークでは、複数の Azure リージョンでのデプロイがサポートされています。 各リージョンでは次のものがホストされます。

- インフラストラクチャのデプロイ
- 状態ファイルとインストール メディアを含む SAP ライブラリ
- 1 から N 個のワークロード ゾーン
- ワークロード ゾーンの 1 から N 個の SAP システム

## <a name="deployment-environments"></a>デプロイ環境

1 つのリージョンで複数のワークロード ゾーンをサポートしている場合は、デプロイ環境と SAP ライブラリの一意の識別子を使用します。 ワークロード ゾーンの識別子は使用しないでください。 たとえば、管理目的では `MGMT` を使用します。

自動化フレームワークでは、ワークロード ゾーンとは別のサブスクリプションにデプロイ環境と SAP ライブラリを配置することもサポートされます。

デプロイ環境には、次のサービスが用意されています。

- Terraform のデプロイと Ansible の構成が実行されるデプロイ VM。
- Terraform のデプロイで使用されるサービス プリンシパルの ID 情報を含むキー コンテナー。
- 送信インターネット接続を提供する Azure Firewall コンポーネント。

デプロイ構成ファイルでは、リージョン、環境名、仮想ネットワーク情報を定義します。 次に例を示します。

```json
{
    "infrastructure": {
        "environment": "MGMT",
        "region": "westeurope",
        "vnets": {
            "management": {
                "address_space": "0.0.0.0/25",
                "subnet_mgmt": {
                    "prefix": "0.0.0.0/28"
                },
                "subnet_fw": {
                    "prefix": "0.0.0.0/26"
                }
            }
        }
    },
    "options": {
        "enable_deployer_public_ip": true
    },
    "firewall_deployment": true
}
```

詳細については、[配置機能を構成する方法の詳細な説明](automation-configure-control-plane.md)を参照してください。

## <a name="sap-library-configuration"></a>SAP ライブラリの構成

SAP ライブラリでは、SAP インストール メディア、部品表 (BOM) ファイル、Terraform 状態ファイル用のストレージが提供されます。 構成ファイルでは、SAP ライブラリのリージョンと環境の名前を定義します。 パラメーターの情報と例については、[自動化用に SAP ライブラリを構成する方法](automation-configure-control-plane.md)に関するページを参照してください。

## <a name="workload-zone-structure"></a>ワークロード ゾーンの構造

ほとんどの SAP 構成では、異なるアプリケーション層に対して複数の[ワークロード ゾーン](automation-deployment-framework.md#deployment-components)があります。 たとえば、開発、品質保証、実稼働用に異なるワークロード ゾーンがある場合があります。

各ワークロード ゾーンで、次のサービスへのアクセスを作成または許可します。

* 仮想ネットワーク、サブネット、ネットワーク セキュリティ グループ用の Azure Virtual Network。
* システム資格情報とデプロイのサービス プリンシパル用の Azure Key Vault。
* ブート診断とクラウド監視用の Azure Storage アカウント。

ワークロード ゾーンのレイアウトを設計する前に、次の質問について検討してください。

* シナリオで必要なワークロード ゾーンの数はいくつですか?
* どのリージョンにワークロードをデプロイする必要がありますか?
* どのような[デプロイメント シナリオ](#supported-deployment-scenarios)ですか?

詳細については、[自動化用にワークロード ゾーンのデプロイを構成する方法](automation-deploy-workload-zone.md)に関するページを参照してください。

## <a name="sap-system-setup"></a>SAP システムの設定

SAP システムには、SAP アプリケーションをホストするために必要なすべての Azure コンポーネントが含まれています。

SAP システムを構成する前に、次の質問について検討してください。

* どのデータベース バックエンドを使用しますか?
* 必要なデータベース サーバーの数はいくつですか?
* シナリオに高可用性は必要ですか?
* 必要なアプリケーション サーバーの数はいくつですか?
* 必要な Web ディスパッチャーの数はいくつですか (必要な場合) ? 
* 必要な中央サービス インスタンスの数はいくつですか?
* 必要な仮想マシン (VM) のサイズはどれくらいですか?
* 使用する VM イメージはどれですか? イメージは Azure Marketplace にありますか、カスタムですか?
* [新規または既存のデプロイ シナリオ](#supported-deployment-scenarios)にデプロイしますか?
* どのような IP 割り当て戦略ですか? Azure で IP を設定しますか、カスタム設定を使用しますか?

詳細については、[自動化用に SAP システムを構成する方法](automation-configure-system.md)に関するページを参照してください。

## <a name="deployment-flow"></a>デプロイ フロー

デプロイを計画する場合は、フロー全体を考慮することが重要です。 自動化フレームワークを使用した Azure への SAP のデプロイには、主に 3 つの手順があります。

1. リージョンの準備。 この手順では、指定された Azure リージョンで SAP 自動化フレームワークをサポートするコンポーネントをデプロイします。 この手順の一部を次に示します。
    1. デプロイ環境の作成
    1. Terraform 状態ファイルの共有ストレージの作成
    1. SAP インストール メディア用の共有ストレージの作成
1. ワークロード ゾーンの準備。 この手順では、仮想ネットワークやキー コンテナーなどの[ワークロード ゾーン コンポーネント](#workload-zone-structure)をデプロイします。
1. システムのデプロイ。 この手順には、[SAP システムのインフラストラクチャ](#sap-system-setup)が含まれます。

## <a name="orchestration-environment"></a>オーケストレーション環境

自動化フレームワークでは、次のサポートされている環境のいずれかからテンプレートとスクリプトを実行する必要があります。

* Azure Cloud Shell
* Azure でホストされている Linux VM
* ローカル Windows コンピューター上の PowerShell

## <a name="naming-conventions"></a>名前付け規則

自動化フレームワークでは、既定の名前付け規則が使用されます。 カスタムの名前付け規則を使用する場合は、デプロイ前にカスタム名を計画して定義します。

## <a name="disk-sizing"></a>ディスクのサイズ変更

[カスタム ディスク サイズを構成する](automation-configure-extra-disks.md)場合は、デプロイ前にカスタム セットアップを計画してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [自動化フレームワークの手動デプロイについて](automation-manual-deployment.md)
