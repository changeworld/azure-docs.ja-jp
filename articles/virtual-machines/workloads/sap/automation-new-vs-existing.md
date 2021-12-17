---
title: 新規および既存のデプロイ用のオートメーション フレームワークの構成
description: 新規と既存の両方のシナリオに対応するように Azure 上の SAP デプロイ自動化フレームワークを構成する方法。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 0f5d426cc6291d6c7cc527668e8d009d1590c366
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725213"
---
# <a name="configuring-for-new-and-existing-deployments"></a>新規および既存のデプロイの構成

新規と既存の両方のデプロイ シナリオで、[Azure 上の SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)を使用できます。 

新しいデプロイ シナリオでは、オートメーション フレームワークは既存の Azure インフラストラクチャを使用しません。 デプロイ プロセスによって、仮想ネットワーク、サブネット、キー コンテナーなどが作成されます。

既存のデプロイ シナリオでは、オートメーション フレームワークは既存の Azure インフラストラクチャを使用します。 たとえば、このデプロイでは既存の仮想ネットワークが使用されます。
## <a name="new-deployment-scenarios"></a>新しいデプロイ シナリオ

新しいリソースを作成する新しいデプロイ シナリオの例を次に示します。

> [!IMPORTANT]
> ご使用のシナリオの必要に応じて、すべての構成例を変更します。
### <a name="new-deployment"></a>新しいデプロイ

このシナリオでは、オートメーション フレームワークによってすべての Azure コンポーネントが作成され、[Deployer](automation-deployment-framework.md#deployment-components) が使用されます。 このデプロイ例には、次が含まれています。

- 西ヨーロッパ Azure リージョンの 2 つの環境
  - 管理 (`MGMT`) はコントロール プレーンをホストする
  - 開発 (`DEV`) は開発環境をホストする
- Deployer
- SAP ライブラリ
- 以下を備えた SAP システム (`SID X00`)
  - 2 台のアプリケーション サーバー
  - 高可用性 (HA) セントラル サービス インスタンス
  - SUSE 12 SP5 を使用する単一ノード HANA バックエンドを持つ Web ディスパッチャー

| コンポーネント       | パラメーター ファイルの場所                                                       |
| --------------- | ----------------------------------------------------------------------------- |
| Deployer        | DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars |
| ライブラリ         | LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                    |
| ワークロード ゾーン   | LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE/DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars  |
| システム          | SYSTEM/DEV-WEEU-SAP01-X00/DEV-WEEU-SAP01-X00.tfvars                           |


このシナリオをテストするには以下を行います。 

[SAP デプロイ自動化フレームワーク](https://github.com/Azure/sap-hana/) リポジトリを複製し、パラメーター ファイルのルート フォルダーにサンプル ファイルをコピーします。

```bash
cd ~/Azure_SAP_Automated_Deployment
mkdir -p WORKSPACES/DEPLOYER
cp sap-hana/deploy/samples/WORKSPACES/DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE WORKSPACES/DEPLOYER/. -r

mkdir -p WORKSPACES/LIBRARY
cp sap-hana/deploy/samples/WORKSPACES/LIBRARY/MGMT-WEEU-SAP_LIBRARY WORKSPACES/LIBRARY/. -r

mkdir -p WORKSPACES/LANDSCAPE
cp sap-hana/deploy/samples/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE WORKSPACES/LANDSCAPE/. -r

mkdir -p WORKSPACES/SYSTEM
cp sap-hana/deploy/samples/WORKSPACES/SYSTEM/DEV-WEEU-SAP01-X00 WORKSPACES/SYSTEM/. -r
cd WORKSPACES
```
    
Deployer とライブラリをインストールして、コントロール プレーンを準備します。 サンプル値は、必ずサービス プリンシパルの情報に置き換えてください。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

export DEPLOYMENT_REPO_PATH="${HOME}/Azure_SAP_Automated_Deployment/sap-hana/"
export ARM_SUBSCRIPTION_ID="${subscriptionID}"

$DEPLOYMENT_REPO_PATH/scripts/prepare_region.sh
    --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars \
    --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                     \
    --subscription $subscriptionID                                                                          \
    --spn_id $appID                                                                                         \
    --spn_secret $spn_secret                                                                                \
    --tenant_id $tenant
    --auto-approve
```

また、PowerShell を使用してデプロイを実行できます。

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

$Subscription=<subscriptionID>
$SPN_id=<appID>
$SPN_password=<password>
$Tenant_id=<tenant>

New-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-DEP01-INFRASTRUCTURE\MGMT-WEEU-DEP01-INFRASTRUCTURE.tfvars 
-LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars
-Subscription $Subscription
-SPN_id $SPN_id
-SPN_password $SPN_password
-Tenant_id $Tenant_id
```

Bash または PowerShell スクリプトを実行して、ワークロード ゾーンをデプロイします。 

サンプルの資格情報は、必ずサービス プリンシパルの情報に置き換えてください。 コントロール プレーンのデプロイで使用したのと同じサービス プリンシパル資格情報を使用できます。ただし、実稼働環境のデプロイでは、ワークロード ゾーンごとに異なるサービス プリンシパルを使用することをお勧めします。

```bash

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh \
    --parameterfile DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars       \
    --deployer_environment 'MGMT'                              \           
    --subscription $subscriptionID                             \
    --spn_id $appID                                            \
    --spn_secret $spn_secret                                   \
    --tenant_id $tenant                                        \
    --auto-approve
```

```powershell

cd \Azure_SAP_Automated_Deployment\WORKSPACES\LANDSCAPE\DEV-WEEU-SAP01-INFRASTRUCTURE

$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"

New-SAPWorkloadZone --parameterfile .\DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars 
    -DeployerEnvironment MGMT
    -Subscription $subscription
    -SPN_id $appId
    -SPN_password $spn_secret
    -Tenant_id $tenant_id
```

SAP システムをデプロイします。 Bash または PowerShell コマンドを実行します。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-WEEU-SAP01-X00

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh --parameterfile DEV-WEEU-SAP01-X00.tfvars --type sap_system --auto-approve
```

```powershell
Import-Module "SAPDeploymentUtilities.psd1"
cd \Azure_SAP_Automated_Deployment\WORKSPACES\SYSTEM\DEV-WEEU-SAP01-X00

New-SAPSystem --parameterfile .\DEV-WEEU-SAP01-X00.tfvars
        -Type sap_system
```
## <a name="existing-example-scenarios"></a>既存のシナリオ例

既存の Azure リソースを使用する既存のシナリオの例を次に示します。

> [!IMPORTANT]
> ご使用のシナリオの必要に応じて、すべての構成例を変更します。
> すべての '<arm_resource_id>' プレースホルダーを更新します。

### <a name="existing-environment-scenario"></a>既存の環境シナリオ

このシナリオでは、オートメーション フレームワークによって既存の Azure コンポーネントが使用され、[Deployer](automation-deployment-framework.md#deployment-components) が使用されます。 これらの既存のコンポーネントには、リソース グループ、ストレージ アカウント、仮想ネットワーク、サブネット、ネットワーク セキュリティ グループが含まれています。 このデプロイ例には、次が含まれています。

- 米国東部 2 リージョンの 2 つの環境
  - 管理 (`MGMT`) はコントロール プレーンをホストする
  - 品質保証 (`QA`) は SAP QA 環境をホストする
- Deployer
- SAP ライブラリ
- 以下を備えた SAP システム (`SID X01`)
  - 2 台のアプリケーション サーバー
  - HA セントラル サービス インスタンス
  - Windows Server 2016 を実行している Microsoft SQL server バックエンドを使用するデータベース
  - Web ディスパッチャー 

| コンポーネント       | パラメーター ファイルの場所                                                       |
| --------------- | ----------------------------------------------------------------------------- |
| Deployer        | DEPLOYER/MGMT-EUS2-DEP01-INFRASTRUCTURE/MGMT-EUS2-DEP01-INFRASTRUCTURE.tfvars |
| ライブラリ         | LIBRARY/MGMT-EUS2-SAP_LIBRARY/MGMT-EUS2-SAP_LIBRARY.tfvars                    |
| ワークロード ゾーン   | LANDSCAPE/QA-EUS2-SAP03-INFRASTRUCTURE/QA-EUS2-SAP03-INFRASTRUCTURE.tfvars    |
| システム          | SYSTEM/QA-EUS2-SAP03-X01/QA-EUS2-SAP03-X01.tfvars                             |


パラメーター ファイルのルート フォルダーにサンプル ファイルをコピーします。

```bash
cd ~/Azure_SAP_Automated_Deployment
mkdir -p WORKSPACES/DEPLOYER
cp sap-hana/deploy/samples/WORKSPACES/DEPLOYER/MGMT-EUS2-DEP01-INFRASTRUCTURE WORKSPACES/DEPLOYER/. -r
    
mkdir -p WORKSPACES/LIBRARY
cp sap-hana/deploy/samples/WORKSPACES/LIBRARY/MGMT-EUS2-SAP_LIBRARY WORKSPACES/LIBRARY/. -r
    
mkdir -p WORKSPACES/LANDSCAPE
cp sap-hana/deploy/samples/WORKSPACES/LANDSCAPE/QA-EUS2-SAP03-INFRASTRUCTURE WORKSPACES/LANDSCAPE/. -r
    
mkdir -p WORKSPACES/SYSTEM
cp sap-hana/deploy/samples/WORKSPACES/SYSTEM/QA-EUS2-SAP03-X01 WORKSPACES/SYSTEM/. -r
cd WORKSPACES
```

サンプル tfvars ファイルにはプレースホルダー '"<azure_resource_id>"' があります。これは、リソース グループ、仮想ネットワーク、サブネットの実際の Azure リソース ID に置き換える必要があります。

Deployer と SAP ライブラリをインストールするコントロール プレーンをデプロイします。 Bash または PowerShell コマンドを実行します。 サンプルの資格情報は、必ずサービス プリンシパルの情報に置き換えてください。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

export DEPLOYMENT_REPO_PATH="${HOME}/Azure_SAP_Automated_Deployment/sap-hana/"
export ARM_SUBSCRIPTION_ID="${subscriptionID}"

$DEPLOYMENT_REPO_PATH/scripts/prepare_region.sh
    --deployer_parameter_file DEPLOYER/MGMT-EUS2-DEP01-INFRASTRUCTURE/MGMT-EUS2-DEP01-INFRASTRUCTURE.tfvars  \
    --library_parameter_file LIBRARY/MGMT-EUS2-SAP_LIBRARY/MGMT-EUS2-SAP_LIBRARY.tfvars                      \                      
    --subscription $subscriptionID                                                                           \
    --spn_id $appID                                                                                          \
    --spn_secret $spn_secret                                                                                 \
    --tenant_id $tenant
    --auto-approve
```

```powershell

cd \Azure_SAP_Automated_Deployment\WORKSPACES


$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"

New-SAPAutomationRegion 
    -DeployerParameterfile .\DEPLOYER\MGMT-EUS2-DEP01-INFRASTRUCTURE\MGMT-EUS2-DEP01-INFRASTRUCTURE.json 
    -LibraryParameterfile .\LIBRARY\MGMT-EUS2-SAP_LIBRARY\MGMT-EUS2-SAP_LIBRARY.json 
    -Subscription $subscription 
    -SPN_id $appId 
    -SPN_password $spn_secret 
    -Tenant_id $tenant_id
    -Silent      
```

Bash または PowerShell スクリプトを実行して、ワークロード ゾーンをデプロイします。 

サンプルの資格情報は、必ずサービス プリンシパルの情報に置き換えてください。 コントロール プレーンのデプロイで使用したのと同じサービス プリンシパル資格情報を使用できます。ただし、実稼働環境のデプロイでは、ワークロード ゾーンごとに異なるサービス プリンシパルを使用することをお勧めします。

```bash

cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/QA-EUS2-SAP03-INFRASTRUCTURE

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh \
    --parameterfile QA-EUS2-SAP03-INFRASTRUCTURE.tfvars        \
    --deployer_environment MGMT                                \           
    --subscription $subscriptionID                             \
    --spn_id $appID                                            \
    --spn_secret $spn_secret                                   \
    --tenant_id $tenant                                        \
    --auto-approve
```

```powershell

cd \Azure_SAP_Automated_Deployment\WORKSPACES\LANDSCAPE\QA-EUS2-SAP03-INFRASTRUCTURE

$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"

New-SAPWorkloadZone --parameterfile .\QA-EUS2-SAP03-INFRASTRUCTURE.tfvars 
    -DeployerEnvironment MGMT
    -Subscription $subscription
    -SPN_id $appId
    -SPN_password $spn_secret
    -Tenant_id $tenant_id
```


QA 環境に SAP システムをデプロイします。 Bash または PowerShell コマンドを実行します。


```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/QA-EUS2-SAP03-X01

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh --parameterfile QA-EUS2-SAP03-X01.tfvars --type sap_system --auto-approve
```

```powershell
cd \Azure_SAP_Automated_Deployment\WORKSPACES\SYSTEM\QA-EUS2-SAP03-X01

New-SAPSystem --parameterfile .\QA-EUS2-SAP03-tfvars.json -Type sap_system
```
## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ハンズオン ラボの手順](automation-tutorial.md)
