<properties
   pageTitle="Azure リソースの推奨される名前付け規則 | ガイダンス | Microsoft Azure"
   description="Azure リソースの推奨される名前付け規則。仮想マシン、ストレージ アカウント、ネットワーク、仮想ネットワーク、サブネット、その他の Azure エンティティに名前を付ける方法"
   services=""
   documentationCenter="na"
   authors="masimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/01/2016"
   ms.author="masimms"/>
   
# Azure リソースの推奨される名前付け規則

Microsoft Azure におけるどのリソースの名前の選択も、以下の理由から重要です。

- 後で名前を変更するのは困難である。
- 名前は、そのリソースの種類の要件を満たす必要がある。

同様に、一貫した名前付け規則に従えば、リソースを特定しやすくなるほか、ソリューションにおけるリソースのロールも理解しやすくなります。

この記事では、Azure リソースの名前付け規則と制限事項の概要を示すと共に、名前付け規則に関する推奨事項のベースライン セットを提供します。これらの推奨事項は、各自のニーズを満たす独自の規則を作るうえでの叩き台として利用できます。

名前付け規則で成功を収めるうえで鍵となるのは、アプリケーションおよび組織全体で規則を策定して準拠し、Azure プラットフォームでさらにアプリケーションとサービスをデプロイする際に変更していくことです。

## サブスクリプションへの名前付け

Azure サブスクリプションに名前を付ける際、詳細な名前にしておくと、各サブスクリプションのコンテキストと用途がはっきりわかるようになります。さまざまなサブスクリプションが利用される可能性のある環境であれば、共通の名前付け規則に従うことで、わかりやすさが格段に増します。

サブスクリプションに名前を付ける際に推奨されるパターンは次のとおりです。

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- "Company" は多くの場合、各サブスクリプションで同じにします。ただし、一部の企業には、組織構造内に子会社があります。こうした企業は中央の IT グループで管理されていることがありますが、その場合は、親会社の名前 (*Contoso*) と子会社の名前 (*North Wind*) の両方を付けて区別できます。

- "Department" は個人のグループが働いている組織内での名前です。名前空間内でのこの項目は省略してもかまいません。企業のサイズによっては、この細かいレベルで区分しなくてもよいためです。

- "Product Line" は部署内で遂行される職務や製品の個別の名前です。一般に、内部向けのサービスやアプリケーションでは省略してもかまいませんが、簡単に区別および識別できる必要のある外部向けのサービスには使用することを強くお勧めします (課金レコードの明確な区別などのため)。

- "Environment" は、"Dev"、"QA"、"Prod" など、アプリケーションまたはサービスのデプロイ ライフサイクルを示す名前です。

| 会社 | 学科 | Product Line または Service | 環境 | フルネーム |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Production | Contoso SocialGaming AwesomeService Production |
| Contoso | SocialGaming | AwesomeService | Dev | Contoso SocialGaming AwesomeService Dev |
| Contoso | IT | InternalApps | Production | Contoso IT InternalApps Production |
| Contoso | IT | InternalApps | Dev | Contoso IT InternalApps Dev |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc -->

## 接辞による明確化

Azure のリソースに名前を付けるときは、リソースの種類とコンテキストを識別するために、一般的なプレフィックスまたはサフィックスを使用することをお勧めします。種類、メタデータ、コンテキストに関する情報はいずれもプログラムで利用できますが、一般的な接辞を使えば、視覚的に区別しやすくなります。名前付け規則に接辞を導入する場合は、接辞を名前の先頭に付ける (プレフィックス) のか、末尾に付ける (サフィックス) のかを明確に指定することが重要です。

たとえば、計算エンジンをホストするサービスの名前として考えられる名前を 2 つ、次に示します。

- SvcCalculationEngine (プレフィックス)
- CalculationEngineSvc (サフィックス)

接辞では、特定のリソースを説明するさまざまな特徴を示すことができます。一般的に使用される例を次に示します。

| 特徴 | 例 | メモ |
| ------ | ------- | ----- |
| 環境 | dev、prod、qa | リソースの環境を識別 |
| Location (場所) | uw (米国西部)、ue (米国東部) | リソースの展開先のリージョンを識別 |
| インスタンス | 01、02 | 複数の名前付きインスタンスが存在するリソースの場合 (Web サーバーなど)。 |
| 製品またはサービス | service | リソースがサポートする製品、アプリケーション、サービスを識別 |
| 役割 | sql、web、messaging | 関連付けられているリソースのロールを識別 |

企業またはプロジェクトの特定の名前付け規則を策定する際には、一般的な接辞のセットと接辞の位置 (サフィックスまたはプリフィックス) を選択することが重要です。

## 名前付け規則と制限事項

Azure のリソースまたはサービスの種類に応じて、一連の名前付けの制限とスコープが決まります。どの名前付け規則やパターンも、求められる名前付け規則とスコープに準拠する必要があります。たとえば、VM の名前は DNS 名にマップされ (Azure 全体で一意になる必要がある)、VNET の名前のスコープは、それが作成されるリソース グループになります。

通常は、名前の最初と最後の文字には特殊文字 (`-` や `_`) を使用しないようにしてください。これらの文字はほとんどの検証ルールにパスしません。

| カテゴリ | サービスまたはエンティティ | Scope | Length | 大文字小文字の区別 | 有効な文字 | 推奨パターン | 例 |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| リソース グループ | リソース グループ | グローバル | 1 ～ 64 | 大文字と小文字は区別されない | 英数字、アンダースコア、ハイフン | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| リソース グループ | 可用性セット | リソース グループ | 1 ～ 80 | 大文字と小文字は区別されない | 英数字、アンダースコア、ハイフン | `<service-short-name>-<context>-as` | `profx-sql-as` |
| 全般 | タグ | 関連付けられたエンティティ | 512 (名前)、256 (値) | 大文字と小文字は区別されない | 英数字 | `"key" : "value"` | `"department" : "Central IT"` |
| コンピューティング | 仮想マシン | リソース グループ | 1 ～ 15 | 大文字と小文字は区別されない | 英数字、アンダースコア、ハイフン | `<name>-<role>-<instance>` | `profx-sql-001` |
| Storage | ストレージ アカウント名 (データ) | グローバル | 3 ～ 24 | 小文字 | 英数字 | `<service short name><type><number>` | `profxdata001` |
| Storage | ストレージ アカウント名 (ディスク) | グローバル | 3 ～ 24 | 小文字 | 英数字 | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Storage | コンテナー名 | ストレージ アカウント | 3 ～ 63 |	小文字 | 英数字とダッシュ | `<context>` | `logs` |
| Storage | BLOB 名 | コンテナー | 1 ～ 1,024 | 大文字小文字は区別される | 任意の URL 文字 | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Storage | キュー名 | ストレージ アカウント | 3 ～ 63 | 小文字 | 英数字とダッシュ | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Storage | テーブル名 | ストレージ アカウント | 3 ～ 63 |大文字と小文字は区別されない | 英数字 | `<service short name>-<context>` | `awesomeservice-logs` |
| Storage | ファイル名 | ストレージ アカウント | 3 ～ 63 | 小文字 | 英数字 | `<variable based on blob usage>` | `<variable based on blob usage>` |
| ネットワーク | Virtual Network (VNet) | リソース グループ | 2 ～ 80 | 大文字と小文字は区別されない | 英数字、ダッシュ、アンダー スコア、ピリオド | `<service short name>-[section]-vnet` | `profx-vnet` |
| ネットワーク | サブネット | 親 VNet | 2 ～ 80 | 大文字と小文字は区別されない | 英数字、アンダー スコア、ダッシュ、ピリオド | `<role>-subnet` | `gateway-subnet` |
| ネットワーク | ネットワーク インターフェイス | リソース グループ | 1 ～ 80 | 大文字と小文字は区別されない | 英数字、ダッシュ、アンダー スコア、ピリオド | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| ネットワーク | ネットワーク セキュリティ グループ | リソース グループ | 1 ～ 80 | 大文字と小文字は区別されない | 英数字、ダッシュ、アンダー スコア、ピリオド | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| ネットワーク | ネットワーク セキュリティ グループの規則 | リソース グループ | 1 ～ 80 | 大文字と小文字は区別されない | 英数字、ダッシュ、アンダー スコア、ピリオド | `<descriptive context>` | `sql-allow` |
| ネットワーク | パブリック IP アドレス | リソース グループ | 1 ～ 80 | 大文字と小文字は区別されない | 英数字、ダッシュ、アンダー スコア、ピリオド | `<vm or service name>-pip` | `profx-sql1-pip` |
| ネットワーク | Load Balancer | リソース グループ | 1 ～ 80 | 大文字と小文字は区別されない | 英数字、ダッシュ、アンダー スコア、ピリオド | `<service or role>-lb` | `profx-lb` |
| ネットワーク | 負荷分散規則の構成 | Load Balancer | 1 ～ 80 | 大文字と小文字は区別されない | 英数字、ダッシュ、アンダー スコア、ピリオド | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | TODO | TODO
-->

## タグによるリソースの整理

Azure Resource Manager では、コンテキストを識別し、自動化を合理化するために、任意のテキスト文字列を使用したエンティティへのタグ付けがサポートされています。たとえば、`"sqlVersion: "sql2014ee"` などのタグでは、自動化されたスクリプトを実行する目的で SQL Server 2014 Enterprise エディションが実行されている VM をデプロイ内で識別できます。タグは、コンテキストを明確化するために、選択した名前付け規則と共に使用してください。

> [AZURE.TIP] タグには、リソース グループをまたいで適用できるため、異なるデプロイ間でエンティティどうしを関連付けることができるという利点もあります。

各リソースまたはリソース グループには、最大で **15** 個のタグを設定できます。タグ名は 512 文字まで、タグ値は 256 文字までに制限されます。

リソースへのタグ付けの詳細については、「[タグを使用した Azure リソースの整理](../resource-group-using-tags.md)」を参照してください。

一般的なタグ付けの使用例は次のとおりです。

- **課金**: リソースをグループ化し、課金またはチャージバック コードに関連付けます。
- **サービスのコンテキストの識別**: 一般的な操作とグループ化のために、リソース グループ間でリソースのグループを識別します。
- **アクセス制御とセキュリティ コンテキスト**: ポートフォリオ、システム、サービス、アプリ、インスタンスなどに基づいて管理者のロールを識別します。 

> [AZURE.TIP] タグは早い段階から頻繁に設定してください。データを集め終えてから改良を加えるよりも、ベースラインのタグ付けのスキームを設けておいて、徐々に調整していく方が適切です。

以下に、一般的なタグ付けのアプローチの例を示します。

| タグ名 | キー | 例 | Comment (コメント) |
| -------- | --- | ------- | ------- |
| 請求先/内部チャージバック ID | billTo | `IT-Chargeback-1234` | 内部 I/O または課金コード |
| オペレーターまたは直接責任者 (DRI) | managedBy | `joe@contoso.com` | エイリアスまたは電子メール アドレス |
| プロジェクト名 | project-name | `myproject` | プロジェクトまたは製品ラインの名前 |
| プロジェクトのバージョン | project-version | `3.4` | プロジェクトまたは製品ラインのバージョン |
| 環境 | environment | `<Production, Staging, QA >` | 環境の識別子 | 
| レベル | tier | `Front End, Back End, Data` | レベルまたはロール/コンテキストの識別 |
| データ プロファイル | dataProfile | `Public, Confidential, Restricted, Internal` | リソースに格納されているデータの機密性 |
 
## ヒントとコツ

アプリケーションの種類によっては、特定の種類のリソースに対する名前付けと規則にさらに注意が必要になるものもあります。これに関する詳細とコンテキストは、以下に列挙しています。

### Virtual Machines

特に、大規模なトポロジでは、仮想マシンの名前を工夫することで、各マシンのロールと用途の識別が格段に容易になるうえ、スクリプトを作成する際の予測可能性が高まります。

> [AZURE.WARNING] Azure のすべての仮想マシンに、Azure リソース名とオペレーティング システムのホスト名の両方が設定されていることに注意してください。リソース名とホスト名が異なる場合は、これらの VM の管理が難しくなる (たとえば、ホスト名が構成されているオペレーティング システムが既に含まれる .vhd から仮想マシンを作成する場合) 可能性があるため、これは避けてください。

- [Windows Server VM の名前付け規則](https://support.microsoft.com/ja-JP/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

###	ストレージ アカウントとストレージ エンティティ

ストレージ アカウントには、VM のディスクのサポートと、BLOB、キュー、テーブルへのデータの格納という 2 つの主要な用途があります。VM ディスクに使用されるストレージ アカウントについては、親 VM の名前に関連付けるという名前付け規則に従う必要があります (また、ハイエンド VM SKU 向けに複数のストレージ アカウントが必要になる可能性があるため、数値のサフィックスも活用してください)。

> [AZURE.TIP] ストレージ アカウントについては、データ用であるか、ディスク用であるかにかかわらず、複数のストレージ アカウントを活用できるような名前付け規則に従う必要があります (つまり、必ず数値のサフィックスを使用してください)

Azure ストレージ アカウントの BLOB データにアクセスするためのカスタム ドメイン名を構成できます。BLOB サービスの既定のエンドポイントは `https://mystorage.blob.core.windows.net` です。

ただし、カスタム ドメイン (www.contoso.com など) をストレージ アカウントの BLOB エンドポイントにマップしている場合、ユーザーはそのドメインを使って、ストレージ アカウントの BLOB データにもアクセスできます。たとえば、カスタム ドメイン名を使えば、`http://mystorage.blob.core.windows.net/mycontainer/myblob` には `http://www.contoso.com/mycontainer/myblob` としてアクセスできます。

この機能の構成の詳細については、「[BLOB ストレージ エンドポイントのカスタム ドメイン名の構成](../storage/storage-custom-domain-name.md)」を参照してください。

BLOB、コンテナー、テーブルへの名前付けの詳細については、以下を参照してください。

- [コンテナー、BLOB、メタデータの名前付けと参照](https://msdn.microsoft.com/library/dd135715.aspx)
- [キューとメタデータの名前付け](https://msdn.microsoft.com/library/dd179349.aspx)
- [テーブルの名前付け](https://msdn.microsoft.com/library/azure/dd179338.aspx)

BLOB 名には任意の文字の組み合わせを含めることができますが、URL の予約文字は適切にエスケープする必要があります。BLOB 名の末尾をピリオド (.)、スラッシュ (/)、それらの連続または組み合わせたものにしないでください。通常、スラッシュは、**仮想**ディレクトリの区切り記号です。BLOB 名では、バックスラッシュ (\\) を使わないでください。クライアント API では許容されるものの、正常にハッシュできず、署名が一致しなくなります。

ストレージ アカウントまたはコンテナーの名前は、作成後に変更することはできません。新しい名前を使用するには、削除したうえで新たに作成する必要があります。

> [AZURE.TIP] 新しいサービスまたはアプリケーションの開発に着手する前に、すべてのストレージ アカウントと種類の名前付け規則を策定することをお勧めします。

## 例 - N 層サービスのデプロイ

この例では、N 層サービスの構成を定義します。これは、フロントエンド IIS サーバー (Windows Server VM にホスト)、SQL Server (2 つの Windows Server VM にホスト)、ElasticSearch クラスター (6 つの Linux VM にホスト) のほか、関連付けられているストレージ アカウント、仮想ネットワーク、リソース グループ、ロード バランサーで構成されます。

このアプリケーションのコンテキストに応じた規則の定義から始めます。

| エンティティ | 規則 | 説明 |
| ------ | ---------- | ------------ |  
| サービス名 | `profx` | デプロイするアプリケーションまたはサービスの短い名前 |
| 環境 | `prod` | 運用環境でのデプロイを目的とする (QA やテストなどの目的ではない) |

このベースラインを基に、リソースの種類ごとの規則を策定できます。

| リソースの種類 | 規則のベース | 例 |
| ------------- | --------------- | ------- |
| [サブスクリプション] | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| リソース グループ | `servicename-rg` | `profx-rg` |
| Virtual Network | `servicename-vnet` | `profx-vnet` |
| サブネット | `role-subnet` | `sql-vnet` |
| Load Balancer | `servicename-lb` | `profx-lb` |
| 仮想マシン | `servicename-role[number]` | `profx-sql0` |
| ストレージ アカウント | `<vmnamenodashes>st<num>` | `profxsql0st0` |

これを以下の図に示します。

![application topology diagram](media/guidance-naming-conventions/guidance-naming-convention-example.png "Sample Application Topology")

## サンプル - 上記のサンプルをデプロイするための Azure CLI スクリプト

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

	# Create the network interface card for this VM
	azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

	# Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
	azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
		--boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
	create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```

<!---HONumber=AcomDC_0302_2016-->