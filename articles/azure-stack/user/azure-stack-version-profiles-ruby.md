---
title: Azure Stack での Ruby による API バージョンのプロファイルの使用 | Microsoft Docs
description: Azure Stack での Ruby による API バージョンのプロファイルの使用について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 73c770f45666ce76b770796ac9714d007c2870cb
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247189"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Azure Stack での Ruby による API バージョンのプロファイルの使用

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

## <a name="ruby-and-api-version-profiles"></a>Ruby と API バージョンのプロファイル

Azure Stack Resource Manager 向けの Ruby SDK には､インフラストラクチャの構築と管理に役立つツールが用意されています。 SDK のリソース プロバイダーには､Ruby 言語を使用したコンピュートや仮想ネットワーク､ストレージがあります。 Ruby SDK に含まれている API プロファイルを使用すると、グローバルな Azure リソースと Azure Stack 上のリソース間を切り替えることで、ハイブリッド クラウド開発を行うことができます。

API プロファイルは、リソース プロバイダーとサービス バージョンを組み合わせたものです。 API プロファイルを使用すると、種類の異なる複数のリソースを組み合わせることができます。

- すべてのサービスの最新バージョンを使用するには、Azure SDK ロールアップ gem の **Latest** プロファイルを使用します。
- Azure Stack と互換性のあるサービスを使用するには、Azure SDK ロールアップ gem の **V2018_03_01** プロファイルを使用します。
- サービスの最新の **api-version** を使用するには、特定の gem の **Latest** プロファイルを使用します。 たとえば、コンピュート サービスの最新の **api-version** を単独で使用する場合は、**Compute** gem の **Latest** プロファイルを使用します。
- サービスの特定の **api-version** を使用するには、gem 内で定義された特定の API バージョンを使用します。

> [!NOTE]
> すべてのオプションを同じアプリケーション内で組み合わせることができます。

## <a name="install-the-azure-ruby-sdk"></a>Azure Ruby SDK のインストール

- 正式な指示に従って、[Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) をインストールします。
- 正式な指示に従って、[Ruby](https://www.ruby-lang.org/en/documentation/installation/) をインストールします。
  - インストール時には、**PATH 変数に Ruby を追加する**ためのオプションを選択します。
  - Ruby のインストール時にプロンプトが表示された場合は、開発キットをインストールします。
  - 次に、下記のコマンドを使用して bundler をインストールします。  
    `Gem install bundler`
- 使用できない場合は、サブスクリプションを作成し、サブスクリプション ID を保存して後で使用します。 サブスクリプションの作成手順については、[こちら](../azure-stack-subscribe-plan-provision-vm.md)をご覧ください。
- サービス プリンシパルを作成し、その ID とシークレットを保存します。 Azure Stack 用のサービス プリンシパルを作成する手順については、[こちら](../azure-stack-create-service-principals.md)をご覧ください。
- サブスクリプションでサービス プリンシパルのロールが共同作成者/所有者であることを確認します。 サービス プリンシパルにロールを割り当てる手順については、[こちら](../azure-stack-create-service-principals.md)をご覧ください。

## <a name="install-the-rubygem-packages"></a>Rubygem パッケージをインストールします

Azure Rubygem パッケージは直接インストールできます。

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

Azure Resource Manager Ruby SDK は現在プレビュー段階であり、今後のリリースで重大なインターフェイス変更が加えられる見込みです。 マイナー バージョンの番号が上がっただけでも、重大な変更を示している可能性があります。

## <a name="use-the-azuresdk-gem"></a>azure_sdk gem を使用する

この gem (**azure_sdk**) は、Ruby SDK でサポートされているすべての gem をまとめたロールアップです。 この gem は、すべてのサービスの最新バージョンをサポートする、 **Latest**  プロファイルで構成されています。 これにより、Azure Stack 用に作成された 2 つのバージョン管理されたプロファイル  **V2017_03_09** と **V2018_03_01**  プロファイルが導入されます。

azure_sdk ロールアップ gem は、次のコマンドを使用してインストールできます。  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>前提条件

Azure Stack で Ruby Azure SDK を使用するには、次の値を指定した後、環境変数に値を設定する必要があります。 環境変数を設定する方法については、表の後にある、オペレーティング システム別の手順を参照してください。

| 値 | 環境変数 | 説明 |
| --- | --- | --- | --- |
| テナント ID | AZURE_TENANT_ID | Azure Stack の[テナント ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview) の値。 |
| クライアント ID | AZURE_CLIENT_ID | このドキュメントの前のセクションでサービス プリンシパルが作成されたときに保存した、サービス プリンシパル アプリケーション ID。  |
| サブスクリプション ID | AZURE_SUBSCRIPTION_ID | [サブスクリプション ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) は Azure Stack 内のオファーにアクセスするために必要です。 |
| クライアント シークレット | AZURE_CLIENT_SECRET | サービス プリンシパルの作成時に保存した、サービス プリンシパル アプリケーション シークレット 。 |
| Resource Manager エンドポイント | ARM_ENDPOINT | 「[Azure Stack Resource Manager エンドポイント](#The-azure-stack-resource-manager-endpoint)」を参照してください。  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack Resource Manager エンドポイント

Microsoft Azure Resource Manager は、管理者が Azure リソースのデプロイ、管理、監視を行えるようにするための管理フレームワークです。 Azure Resource Manager では、これらのタスクを個別に処理するのではなく、グループとして単一の操作で処理することができます。

メタデータ情報は、Resource Manager エンドポイントから取得できます。 エンドポイントは、コードを実行するために必要な情報と共に、JSON ファイルを返します。

 > [!NOTE]  
 > Azure Stack Development Kit (ASDK) の **ResourceManagerUrl** は`https://management.local.azurestack.external/` になります。統合システムの **ResourceManagerUrl** は `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` になります。  
 > 必要なメタデータを取得するには、`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` になります。
  
 サンプルの JSON ファイルは、次のようになります。

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
   "graphEndpoint": "https://graph.windows.net/",  
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": {
     "loginEndpoint": "https://login.windows.net/",
     "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
   }
 }
```

### <a name="set-environmental-variables"></a>環境変数の設定

**Microsoft Windows**  
環境変数を設定するには、Windows コマンド プロンプトで次の形式を使用します。  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS、LinuxおよびUnixベース システム**。Unix ベースのシステムでは、次のコマンドを使用できます。  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>既存の API プロファイル

Azure_sdk ロールアップ gem には、次の 3 つのプロファイルがあります。

1. **V2018_03_01** Azure Stack 用に作成されたプロファイル。 Azure Stack で使用可能なサービスのすべての最新バージョンを使用するには、このプロファイルを使用します。
2. **V2017_03_09**  
  Azure Stack 用に作成されたプロファイル。 このプロファイルは、Azure Stack との互換性に優れたサービスに使用します。
3. **最新**  
  このプロファイルは、すべてのサービスの最新バージョンで構成されます。 すべてのサービスの最新バージョンを使用してください。

Azure Stack および API プロファイルについて詳しくは、「[API プロファイルの概要](azure-stack-version-profiles.md#summary-of-api-profiles)」をご覧ください。

## <a name="azure-ruby-sdk-api-profile-usage"></a>Azure Ruby SDK API プロファイルの使用

次のコードを使用して、プロファイル クライアントをインスタンス化できます。 このパラメーターは、Azure Stack またはまたはその他のプライベート クラウドにのみ必要です。 グローバル Azure では、既定でこれらの設定が使用されます。

```Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
  ENV['AZURE_TENANT_ID'],
  ENV['AZURE_CLIENT_ID'],
  ENV['AZURE_CLIENT_SECRET'],
  active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
  credentials: credentials,
  subscription_id: subscription_id,
  active_directory_settings: active_directory_settings,
  base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2018_03_01::Mgmt::Client.new(options)
```

プロファイル クライアントは、個別のリソース プロバイダー (コンピュート、ストレージ、ネットワークなど) にアクセスするために使用できます。

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2018_03_01::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-environment-setting-functions"></a>Azure Stack 環境設定関数の定義

サービス プリンシパルを Azure Stack 環境に対して認証するには、`get_active_directory_settings()` を使用してエンドポイントを定義します。 このメソッドでは、環境変数を確立する際に設定した、**ARM_Endpoint** 環境変数が使用されます。

```Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
  settings = MsRestAzure::ActiveDirectoryServiceSettings.new
  response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
  status_code = response.code
  response_content = response.body
  unless status_code == "200"
    error_model = JSON.load(response_content)
    fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
  end
  result = JSON.load(response_content)
  settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
  settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
  settings
end
```

## <a name="samples-using-api-profiles"></a>API プロファイルを使用したサンプル

Ruby と Azure Stack API のプロファイルを使用してソリューションを作成する場合は、GitHub で見つかる次のサンプルを参考資料として使用できます。

- [Azure のリソースとリソース グループを Ruby で管理する](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)
- [Ruby を使用して仮想マシンを管理する](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM)
- [Ruby からテンプレートを使用して SSH 対応 VM をデプロイする](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment)

### <a name="sample-resource-manager-and-groups"></a>Resource Manager とグループのサンプル

このサンプルを実行するには、Ruby がインストールされていることを確認してください。 Visual Studio Code を使用している場合は、Ruby SDK 拡張機能もダウンロードしてください。

> [!NOTE]  
> このサンプルのリポジトリは、「[Manage Azure resources and resource groups with Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)」(Rubyを使用して Azure リソースとリソース グループを管理する) で取得できます。

1. リポジトリを複製します。

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. bundle を使用して依存関係をインストールします。

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. PowerShell を使用して Azure サービス プリンシパルを作成し、必要な値を取得します。

   サービス プリンシパルの作成方法については、「[Azure PowerShell を使用して資格情報でのサービス プリンシパルを作成する](../azure-stack-create-service-principals.md)」をご覧ください。

   必要な値は次のとおりです。
   - テナント ID
   - クライアント ID
   - クライアント シークレット
   - サブスクリプション ID
   - Resource Manager エンドポイント

   作成したサービス プリンシパルから取得した情報を使用して、次の環境変数を設定します。

   - export AZURE_TENANT_ID={ご使用のテナント ID}
   - export AZURE_CLIENT_ID={ご使用のクライアント ID}
   - export AZURE_CLIENT_SECRET={ご使用のクライアント シークレット}
   - export AZURE_SUBSCRIPTION_ID={ご使用のサブスクリプション ID}
   - export ARM_ENDPOINT={ご使用の AzureStack Resource Manager URL}

   > [!NOTE]  
   > Windows では、export ではなく set を使用します。

4. location 変数が、たとえば `LOCAL="local"` のように Azure Stack の場所に設定されていることを確認します。

5. Azure Stack またはその他のプライベート クラウドを使用している場合は、次のコード行を追加して、適切な Active Directory エンドポイントをターゲットにします。

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. options 変数内で、Azure Stack と連携するための Active Directory 設定とベース URL を追加します。

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Azure Stack プロファイルをターゲットとするプロファイル クライアントを作成します。

   ```ruby  
   client = Azure::Resources::Profiles::V2018_03_01::Mgmt::Client.new(options)
   ```

8. サービス プリンシパルを Azure Stack に対して認証するには、**get_active_directory_settings()** を使用してエンドポイントを定義します。 このメソッドでは、環境変数を確立する際に設定した、**ARM_Endpoint** 環境変数が使用されます。

   ```ruby  
   def get_active_directory_settings(armEndpoint)
     settings = MsRestAzure::ActiveDirectoryServiceSettings.new
     response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
     status_code = response.code
     response_content = response.body
     unless status_code == "200"
       error_model = JSON.load(response_content)
       fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
     end
     result = JSON.load(response_content)
     settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
     settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
     settings
   end
   ```

9. サンプルを実行します。

   ```ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>次の手順

- [PowerShell for Azure Stack のインストール](azure-stack-powershell-install.md)
- [Azure Stack ユーザーの PowerShell 環境の構成](azure-stack-powershell-configure-user.md)  
