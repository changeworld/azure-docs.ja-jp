---
title: Azure Data Box の注文方法のチュートリアル | Microsoft Docs
description: このチュートリアルでは、オンプレミスのデータを Azure にインポートするハイブリッド ソリューション Azure Data Box とその注文方法について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 489182db34366a910a26ad358d9ef60ae4244530
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123468566"
---
# <a name="tutorial-order-azure-data-box"></a>チュートリアル:Azure Data Box を注文する

Azure Data Box は、迅速かつ簡単な信頼性の高い方法でオンプレミス データを Azure にインポートできるハイブリッド ソリューションです。 お客様は、Microsoft 提供の 80 TB (使用可能容量) ストレージ デバイスにデータを転送した後、そのデバイスを Microsoft に返送します。 その後、このデータは Azure にアップロードされます。

このチュートリアルでは、Azure Data Box を注文する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。  

> [!div class="checklist"]
>
> * Data Box を展開するための前提条件
> * Data Box を注文する
> * 注文を追跡する
> * 注文をキャンセルする

> [!NOTE]
> Data Box の注文と出荷に関するよくあるご質問の回答については、「[Data Box の FAQ](data-box-faq.yml)」を参照してください。

## <a name="prerequisites"></a>前提条件

# <a name="portal"></a>[ポータル](#tab/portal)

デバイスをデプロイする前に、Data Box サービスとデバイスに関する次の構成の前提条件を完了してください。

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

次の 2 つの方法のいずれかで Azure にサインインし、Azure CLI コマンドを実行できます。

* CLI をインストールして、CLI コマンドをローカルで実行できます。
* Azure portal 内から、Azure Cloud Shell で CLI コマンドを実行できます。

このチュートリアルでは Windows PowerShell から Azure CLI を使用しますが、どちらの方法を使用してもかまいません。

### <a name="for-azure-cli"></a>Azure CLI では

開始する前に次の点を確認します。

#### <a name="install-the-cli-locally"></a>CLI をローカルにインストールする

* [Azure CLI](/cli/azure/install-azure-cli) (バージョン 2.0.67 以降) をインストールします。 [MSI を使用してインストール](https://aka.ms/installazurecliwindows)することもできます。

**Azure へのサインイン**

Windows PowerShell コマンド ウィンドウを開き、[az login](/cli/azure/reference-index#az_login) コマンドを使用して Azure にサインインします。

```azurecli
PS C:\Windows> az login
```

サインインに成功すると、次の出力が表示されます。

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**Azure Data Box CLI 拡張機能をインストールする**

Azure Data Box CLI コマンドを使用する前に、拡張機能をインストールしておく必要があります。 Azure CLI 拡張機能を使用すると、コア CLI の一部としてまだ出荷されていない実験用コマンドおよびプレリリース コマンドにアクセスできます。 拡張機能の詳細については、「[Azure CLI で拡張機能を使用する](/cli/azure/azure-cli-extensions-overview)」を参照してください。

Azure Data Box の拡張機能をインストールするには、`az extension add --name databox` コマンドを実行します。

```azurecli

    PS C:\Windows> az extension add --name databox
```

拡張機能が正常にインストールされた場合、次の出力が表示されます。

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell を使用する

CLI コマンドは、Azure でホストされる対話型のシェル環境、[Azure Cloud Shell](https://shell.azure.com/) をブラウザーから使用して実行できます。 Azure サービスに関して、Azure Cloud Shell は Bash または Windows PowerShell をサポートします。 Azure CLI は、アカウントで使用できるように事前にインストールおよび構成されています。 Azure portal の右上のセクションのメニューで [Cloud Shell] ボタンを選択します。

![Cloud Shell メニューの選択](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

このボタンによって対話型のシェルが起動されます。このハウツー記事で説明する手順は、これを使って実行できます。

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Azure PowerShell では

開始する前に次の点を確認します。

* Windows PowerShell 6.2.4 以降をインストールします。
* Azure PowerShell (AZ) モジュールをインストールします。
* Azure Data Box (Az.DataBox) モジュールをインストールします。
* Azure にサインインします。

#### <a name="install-azure-powershell-and-modules-locally"></a>Azure PowerShell とモジュールをローカルにインストールする

**Windows PowerShell をインストールまたはアップグレードする**

Windows PowerShell バージョン 6.2.4 以降がインストールされている必要があります。 インストールされている PowerShell のバージョンを確認するには、`$PSVersionTable` を実行します。

次の出力が表示されます。

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

6\.2.4 よりも前のバージョンがインストールされている場合は、Windows PowerShell のバージョンをアップグレードする必要があります。 Windows PowerShell の最新バージョンをインストールするには、[Azure PowerShell のインストール](/powershell/scripting/install/installing-powershell)に関するページを参照してください。

**Azure PowerShell および Data Box モジュールをインストールする**

Azure PowerShell を使用して Azure Data Box を注文するには、Azure PowerShell モジュールをインストールする必要があります。 Azure PowerShell モジュールをインストールするには、次の手順を実行します。

1. [Azure PowerShell Az モジュール](/powershell/azure/new-azureps-module-az)をインストールします。
2. 次に、`Install-Module -Name Az.DataBox` コマンドを使用して Az.DataBox をインストールします。

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Azure へのサインイン

Windows PowerShell コマンド ウィンドウを開き、[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) コマンドを使用して Azure にサインインします。

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

サインインに成功すると、次の出力が表示されます。

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Windows PowerShell を使用して Azure にサインインする方法の詳細については、「[Azure PowerShell を使用してサインインする](/powershell/azure/authenticate-azureps)」を参照してください。

---

## <a name="order-data-box"></a>Data Box を注文する

# <a name="portal"></a>[ポータル](#tab/portal)

[!INCLUDE [order-data-box-via-portal](../../includes/data-box-order-portal.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

デバイスを注文するには、Azure CLI を使用して次の手順を実行します。

1. Data Box の注文の設定を書き留めます。 たとえば、個人情報や勤務先情報、サブスクリプション名、デバイス情報、発送情報があります。 CLI コマンドを実行して Data Box の注文を作成するときに、これらの設定をパラメーターとして使用する必要があります。 `az databox job create` に使用するパラメーターの設定を次の表に示します。

   | 設定 (パラメーター) | 説明 |  値の例 |
   |---|---|---|
   |resource-group| 既存のグループを使用するか、新しいグループを作成します。 リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。 | "myresourcegroup"|
   |name| 作成する注文の名前。 | "mydataboxorder"|
   |contact-name| 配送先住所に関連付けられている名前。 | "Gus Poland"|
   |phone| 注文の配送先となる個人または企業の電話番号。| "14255551234"
   |location| デバイスの配送元となる最寄りの Azure リージョン。| "US West"|
   |sku| 注文する特定の Data Box デバイス。 有効な値は次のとおりです。"DataBox"、"DataBoxDisk"、"DataBoxHeavy"| "DataBox" |
   |email-list| 注文に関連付けられたメール アドレス。| "gusp@contoso.com" |
   |street-address1| 注文の配送先住所の番地。 | "15700 NE 39th St" |
   |street-address2| 住所 2 の情報 (部屋番号、建物番号など)。 | "Building 123" |
   |city| デバイスの配送先となる市区町村。 | "Redmond" |
   |state-or-province| デバイスの配送先となる都道府県。| "WA" |
   |country| デバイスの配送先となる国。 | "米国" |
   |postal-code| 配送先住所に関連付けられている郵便番号。| "98052"|
   |company-name| 勤務先の会社の名前。| "Contoso, LTD" |
   |ストレージ アカウント| インポートするデータがある Azure Storage アカウント。| "mystorageaccount"|
   |debug| 詳細ログにデバッグ情報を追加します。  | --debug |
   |help| このコマンドのヘルプ情報を表示します。 | --help -h |
   |only-show-errors| エラーのみを表示し、警告は抑制します。 | --only-show-errors |
   |output -o| 出力形式を設定します。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。 既定値は json です。 | --output "json" |
   |query| JMESPath クエリ文字列。 詳細については、「[JMESPath](http://jmespath.org/)」を参照してください。 | --query &lt;文字列&gt;|
   |verbose| 詳細ログを含めます。 | --verbose |

2. 任意のコマンド プロンプトまたはターミナルで、[az data box job create](/cli/azure/databox/job#az_databox_job_create) を実行して Azure Data Box の注文を作成します。

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   コマンドの使用例を次に示します。

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   このコマンドを実行したときの出力を次に示します。

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. すべての Azure CLI コマンドでは、出力形式を変更しない限り、json が既定の形式として使用されます。 出力形式は、グローバル パラメーター `--output <output-format>` を使用して変更できます。 形式を "table" に変更すると、出力の読みやすさが改善されます。

   次に示すのは、先ほど実行したコマンドと同じですが、形式を少し変更しています。

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   このコマンドを実行したときの出力を次に示します。

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

デバイスを注文するには、Azure PowerShell を使用して次の手順を実行します。

1. インポート注文を作成する前に、ストレージ アカウントを取得し、ストレージ アカウント オブジェクトを変数に保存する必要があります。

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Data Box の注文の設定を書き留めます。 たとえば、個人情報や勤務先情報、サブスクリプション名、デバイス情報、発送情報があります。 PowerShell コマンドを実行して Data Box の注文を作成するときに、これらの設定をパラメーターとして使用する必要があります。 [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) に使用するパラメーターの設定を、次の表に示します。

    | 設定 (パラメーター) | 説明 |  値の例 |
    |---|---|---|
    |ResourceGroupName [必須]| 既存のリソース グループを使用します。 リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。 | "myresourcegroup"|
    |Name [必須]| 作成する注文の名前。 | "mydataboxorder"|
    |ContactName [必須]| 配送先住所に関連付けられている名前。 | "Gus Poland"|
    |PhoneNumber [必須]| 注文の配送先となる個人または企業の電話番号。| "14255551234"
    |Location [必須]| デバイスの配送元となる最寄りの Azure リージョン。| "WestUS"|
    |DataBoxType [必須]| 注文する特定の Data Box デバイス。 有効な値は次のとおりです。"DataBox"、"DataBoxDisk"、"DataBoxHeavy"| "DataBox" |
    |EmailId [必須]| 注文に関連付けられたメール アドレス。| "gusp@contoso.com" |
    |StreetAddress1 [必須]| 注文の配送先住所の番地。 | "15700 NE 39th St" |
    |StreetAddress2| 住所 2 の情報 (部屋番号、建物番号など)。 | "Building 123" |
    |StreetAddress3| 3 つ目の住所情報。 | |
    |City [必須]| デバイスの配送先となる市区町村。 | "Redmond" |
    |StateOrProvinceCode [必須]| デバイスの配送先となる都道府県。| "WA" |
    |CountryCode [必須]| デバイスの配送先となる国。 | "米国" |
    |PostalCode [必須]| 配送先住所に関連付けられている郵便番号。| "98052"|
    |CompanyName| 勤務先の会社の名前。| "Contoso, LTD" |
    |StorageAccountResourceId [必須]| データのインポート元の Azure Storage アカウント ID。| &lt;AzstorageAccount&gt;.id |

3. 任意のコマンド プロンプトまたはターミナルで、[New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) を使用して Azure Data Box の注文を作成します。

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   このコマンドを実行したときの出力を次に示します。

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>注文を追跡する

# <a name="portal"></a>[ポータル](#tab/portal)

注文後、Azure portal から注文の状態を追跡できます。 Data Box の注文に移動し、 **[概要]** に移動して状態を表示します。 ポータルでは、注文は、 **[注文済み]** 状態で表示されます。

デバイスが使用できない場合は、通知されます。 デバイスが使用可能な場合は、Microsoft が配送用のデバイスを特定し、配送の準備をします。 デバイスの準備中に、次のアクションが実行されます。

* デバイスに関連付けられているストレージ アカウントごとに SMB 共有が作成されます。
* 各共有で、ユーザー名やパスワードなどのアクセス資格情報が生成されます。
* デバイスのロック解除に役立つデバイス パスワードも生成されます。
* Data Box は、任意の時点でのデバイスへの不正アクセスを防ぐためにロックされます。

デバイスの準備が完了すると、ポータルでは、注文が **[処理済み]** 状態で表示されます。

![処理済みの Data Box の注文](media/data-box-overview/data-box-order-status-processed.png)

次に、Microsoft は、デバイスを準備して地域の運送業者を通じて発送します。 デバイスが出荷されると、お客様に追跡番号が送信されます。 ポータルには、**出荷済み** 状態の注文が表示されます。

![発送済みの Data Box の注文](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>1 つの注文を追跡する

Azure Data Box の既存の 1 つの注文に関する追跡情報を取得するには、[`az databox job show`](/cli/azure/databox/job#az_databox_job_show) を実行します。 このコマンドでは、注文に関する情報が表示されます。名前、リソース グループ、追跡情報、サブスクリプション ID、連絡先情報、出荷タイプ、デバイス SKU などが表示されますが、これらに限定されません。

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   `az databox job show` に使用するパラメーターの情報を次の表に示します。

   | パラメーター | 説明 |  値の例 |
   |---|---|---|
   |resource-group [必須]| 注文に関連付けられているリソース グループの名前。 リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。 | "myresourcegroup"|
   |name [必須]| 表示する注文の名前。 | "mydataboxorder"|
   |debug| 詳細ログにデバッグ情報を追加します。 | --debug |
   |help| このコマンドのヘルプ情報を表示します。 | --help -h |
   |only-show-errors| エラーのみを表示し、警告は抑制します。 | --only-show-errors |
   |output -o| 出力形式を設定します。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。 既定値は json です。 | --output "json" |
   |query| JMESPath クエリ文字列。 詳細については、「[JMESPath](http://jmespath.org/)」を参照してください。 | --query &lt;文字列&gt;|
   |verbose| 詳細ログを含めます。 | --verbose |

   次に示すのは、出力形式を "table" に設定したコマンドの例です。

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   このコマンドを実行したときの出力を次に示します。

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> 注文の一覧表示はサブスクリプション レベルでサポートされます。そのため、リソース グループは (必要なパラメーターではなく) オプション パラメーターとなります。

### <a name="list-all-orders"></a>すべての注文を一覧表示する

複数のデバイスを注文した場合は、[`az databox job list`](/cli/azure/databox/job#az_databox_job_list) を実行することで、Azure Data Box のすべての注文を表示できます。 このコマンドでは、特定のリソース グループに属しているすべての注文が一覧表示されます。 出力には、注文の名前、出荷状態、Azure リージョン、配送の種類、注文の状態も表示されます。 この一覧には、キャンセルされた注文も含まれます。
それぞれの注文のタイム スタンプも表示されます。

```azurecli
az databox job list --resource-group <resource-group>
```

`az databox job list` に使用するパラメーターの情報を次の表に示します。

   | パラメーター | 説明 |  値の例 |
   |---|---|---|
   |resource-group [必須]| 注文が含まれているリソース グループの名前。 リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。 | "myresourcegroup"|
   |debug| 詳細ログにデバッグ情報を追加します。 | --debug |
   |help| このコマンドのヘルプ情報を表示します。 | --help -h |
   |only-show-errors| エラーのみを表示し、警告は抑制します。 | --only-show-errors |
   |output -o| 出力形式を設定します。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。 既定値は json です。 | --output "json" |
   |query| JMESPath クエリ文字列。 詳細については、「[JMESPath](http://jmespath.org/)」を参照してください。 | --query &lt;文字列&gt;|
   |verbose| 詳細ログを含めます。 | --verbose |

   次に示すのは、出力形式を "table" に設定したコマンドの例です。

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   このコマンドを実行したときの出力を次に示します。

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>1 つの注文を追跡する

Azure Data Box の既存の注文 1 つに関する追跡情報を取得するには、[Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob) を実行します。 このコマンドでは、注文に関する情報が表示されます。名前、リソース グループ、追跡情報、サブスクリプション ID、連絡先情報、出荷タイプ、デバイス SKU などが表示されますが、これらに限定されません。

> [!NOTE]
> `Get-AzDataBoxJob` を使用すると、1 つの注文と複数の注文の両方を表示できます。 1 つの注文を表示する場合は、注文名を指定します。

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   `Get-AzDataBoxJob` に使用するパラメーターの情報を次の表に示します。

   | パラメーター | 説明 |  値の例 |
   |---|---|---|
   |ResourceGroup [必須]| 注文に関連付けられているリソース グループの名前。 リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。 | "myresourcegroup"|
   |Name [必須]| 情報を取得する注文の名前。 | "mydataboxorder"|
   |ResourceId| 注文に関連付けられているリソースの ID。 |  |

   コマンドの例と出力を次に示します。

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   このコマンドを実行したときの出力を次に示します。

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>すべての注文を一覧表示する

複数のデバイスを注文した場合は、[`Get-AzDataBoxJob`](/powershell/module/az.databox/Get-AzDataBoxJob) を実行することで、Azure Data Box のすべての注文を表示できます。 このコマンドでは、特定のリソース グループに属しているすべての注文が一覧表示されます。 出力には、注文の名前、出荷状態、Azure リージョン、配送の種類、注文の状態も表示されます。 この一覧には、キャンセルされた注文も含まれます。
それぞれの注文のタイム スタンプも表示されます。

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

次にコマンドの例を示します。

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

このコマンドを実行したときの出力を次に示します。

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>注文をキャンセルする

# <a name="portal"></a>[ポータル](#tab/portal)

この注文をキャンセルするには、Azure portal で **[概要]** に移動し、コマンド バーの **[キャンセル]** を選択します。

注文は、注文した後、注文の状態が [処理済み] とマークされるまでの間の任意の時点でキャンセルできます。

キャンセルされた注文を削除するには、 **[概要]** に移動し、コマンド バーの **[削除]** を選択します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>注文のキャンセル

Azure Data Box の注文をキャンセルするには、[`az databox job cancel`](/cli/azure/databox/job#az_databox_job_cancel) を実行します。 注文のキャンセル理由を指定する必要があります。

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   `az databox job cancel` に使用するパラメーターの情報を次の表に示します。

   | パラメーター | 説明 |  値の例 |
   |---|---|---|
   |resource-group [必須]| 削除する注文に関連付けられているリソース グループの名前。 リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。 | "myresourcegroup"|
   |name [必須]| 削除する注文の名前。 | "mydataboxorder"|
   |reason [必須]| 注文のキャンセル理由。 | "間違った情報を入力したので注文をキャンセルする必要がありました。" |
   |はい| 確認のダイアログを表示しません。 | --yes (-y)| 
   |debug| 詳細ログにデバッグ情報を追加します。 | --debug |
   |help| このコマンドのヘルプ情報を表示します。 | --help -h |
   |only-show-errors| エラーのみを表示し、警告は抑制します。 | --only-show-errors |
   |output -o| 出力形式を設定します。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。 既定値は json です。 | --output "json" |
   |query| JMESPath クエリ文字列。 詳細については、「[JMESPath](http://jmespath.org/)」を参照してください。 | --query &lt;文字列&gt;|
   |verbose| 詳細ログを含めます。 | --verbose |

   コマンドの例と出力を次に示します。

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   このコマンドを実行したときの出力を次に示します。

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>注文を削除する

Azure Data Box の注文をキャンセルした場合は、[`az databox job delete`](/cli/azure/databox/job#az_databox_job_delete) を実行してその注文を削除できます。

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   `az databox job delete` に使用するパラメーターの情報を次の表に示します。

   | パラメーター | 説明 |  値の例 |
   |---|---|---|
   |resource-group [必須]| 削除する注文に関連付けられているリソース グループの名前。 リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。 | "myresourcegroup"|
   |name [必須]| 削除する注文の名前。 | "mydataboxorder"|
   |subscription| Azure サブスクリプションの名前または ID (GUID)。 | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |はい| 確認のダイアログを表示しません。 | --yes (-y)|
   |debug| 詳細ログにデバッグ情報を追加します。 | --debug |
   |help| このコマンドのヘルプ情報を表示します。 | --help -h |
   |only-show-errors| エラーのみを表示し、警告は抑制します。 | --only-show-errors |
   |output -o| 出力形式を設定します。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。 既定値は json です。 | --output "json" |
   |query| JMESPath クエリ文字列。 詳細については、「[JMESPath](http://jmespath.org/)」を参照してください。 | --query &lt;文字列&gt;|
   |verbose| 詳細ログを含めます。 | --verbose |

コマンドの例と出力を次に示します。

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   このコマンドを実行したときの出力を次に示します。

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>注文のキャンセル

Azure Data Box の注文をキャンセルするには、[Stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob) を実行します。 注文のキャンセル理由を指定する必要があります。

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

`Stop-AzDataBoxJob` に使用するパラメーターの情報を次の表に示します。

| パラメーター | 説明 |  値の例 |
|---|---|---|
|ResourceGroup [必須]| キャンセルする注文に関連付けられているリソース グループの名前。 リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。 | "myresourcegroup"|
|Name [必須]| 削除する注文の名前。 | "mydataboxorder"|
|Reason [必須]| 注文のキャンセル理由。 | "間違った情報を入力したので注文をキャンセルする必要がありました。" |
|Force | ユーザーの確認なしに、コマンドレットを強制的に実行します。 | -Force |

コマンドの例と出力を次に示します。

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

このコマンドを実行したときの出力を次に示します。

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>注文を削除する

Azure Data Box の注文をキャンセルした場合は、[`Remove-AzDataBoxJob`](/powershell/module/az.databox/remove-azdataboxjob) を実行してその注文を削除できます。

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

`Remove-AzDataBoxJob` に使用するパラメーターの情報を次の表に示します。

| パラメーター | 説明 |  値の例 |
|---|---|---|
|ResourceGroup [必須]| 削除する注文に関連付けられているリソース グループの名前。 リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。 | "myresourcegroup"|
|Name [必須]| 削除する注文の名前。 | "mydataboxorder"|
|Force | ユーザーの確認なしに、コマンドレットを強制的に実行します。 | -Force |

コマンドの例と出力を次に示します。

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

このコマンドを実行したときの出力を次に示します。

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box に関する次のような事柄について説明しました。

> [!div class="checklist"]
>
> * Data Box を展開するための前提条件
> * Data Box を注文する
> * 注文を追跡する
> * 注文をキャンセルする

次のチュートリアルに進んで、Data Box を設定する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を設定する](./data-box-deploy-set-up.md)
