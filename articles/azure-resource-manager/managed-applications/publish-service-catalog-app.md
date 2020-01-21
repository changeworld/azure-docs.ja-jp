---
title: サービス カタログ マネージド アプリを発行する
description: 組織のメンバーを対象とする Azure マネージド アプリケーションを作成する方法について説明します。
author: tfitzmac
ms.topic: tutorial
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: e756617a700d258078e84a3fa11c8aceb6f4dd88
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903266"
---
# <a name="create-and-publish-a-managed-application-definition"></a>マネージド アプリケーション定義を作成して発行する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

組織のメンバーを対象とする Azure [マネージド アプリケーション](overview.md)を作成し、発行することができます。 たとえば、IT 部門が、組織の基準を満たすマネージ アプリケーションを発行できます。 これらのマネージド アプリケーションは、Azure Marketplace ではなく、サービス カタログを利用して入手できます。

対象の Azure サービス カタログにマネージド アプリケーションを発行するには、次の操作を行う必要があります。

* マネージド アプリケーションでデプロイするリソースを定義するテンプレートを作成する。
* マネージド アプリケーションをデプロイするときに、ポータルのユーザー インターフェイス要素を定義する。
* 必要なテンプレート ファイルを含む .zip パッケージを作成する。
* どのユーザー、グループ、またはアプリケーションがユーザーのサブスクリプションのリソース グループにアクセスする必要があるかを決める。
* .zip パッケージを指定して ID アクセスを要求するマネージド アプリケーション定義を作成する。

この記事では、マネージ アプリケーションには、ストレージ アカウントのみ存在します。 これは、マネージ アプリケーションを公開する手順を説明するためです。 詳細な例については、[Azure マネージド アプリケーションのサンプル プロジェクト](sample-projects.md)に関する記事を参照してください。

この記事の PowerShell の例では、Azure PowerShell 6.2 以降が必要です。 必要に応じて、[バージョンを更新](/powershell/azure/install-Az-ps)してください。

## <a name="create-the-resource-template"></a>リソース テンプレートの作成

各マネージ アプリケーション定義には、**mainTemplate.json** というファイルが含まれています。 この中で、デプロイする Azure リソースを定義します。 テンプレートは、通常の Resource Manager テンプレートと違いはありません。

**mainTemplate.json** というファイルを作成します。 名前の大文字と小文字は区別されます。

次の JSON をファイルに追加します。 このファイルで、ストレージ アカウントを作成するパラメーターを定義し、ストレージ アカウントのプロパティを指定します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

mainTemplate.json ファイルを保存します。

## <a name="defining-your-create-experience-using-createuidefinitionjson"></a>CreateUiDefinition.json を使用した作成エクスペリエンスの定義

発行元として、**createUiDefinition.json** ファイルを使用して作成エクスペリエンスを定義します。これにより、マネージド アプリケーションを作成するユーザーのインターフェイスが生成されます。 ドロップダウン、テキスト ボックス、パスワード ボックスなどの[コントロール要素](create-uidefinition-elements.md)を使用して、各パラメーターの入力をユーザーがどのように提供するかを定義します。

**createUiDefinition.json** というファイルを作成します (この名前の大文字と小文字は区別されます)

次のスターター JSON をファイルに追加し、保存します。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

詳細については、[CreateUiDefinition の概要](create-uidefinition-overview.md)に関するページを参照してください。

## <a name="package-the-files"></a>ファイルのパッケージ化

app.zip という .zip ファイルに 2 つのファイルに追加します。 2 つのファイルは .zip ファイルのルートに配置する必要があります。 このファイルをフォルダーに配置すると、マネージ アプリケーション定義を作成するときに、必要なファイルが存在しないことを示すエラーが発生します。 

パッケージは、想定される実行場所からアクセスできる場所にアップロードしてください。 

```powershell
New-AzResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>マネージド アプリケーション定義の作成

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Azure Active Directory ユーザー グループまたはアプリケーションの作成

次に、顧客に代わってリソースを管理するためのユーザー グループまたはアプリケーションを選択します。 このユーザー グループまたはアプリケーションには、割り当てられているロールに従って、マネージド リソース グループに対するアクセス許可が付与されています。 そのロールには、ロールベースのアクセス制御 (RBAC) の組み込みロール (所有者、共同作成者など) をどれでも使用できます。 リソースを管理するためのアクセス許可は、個々のユーザーに付与することもできますが、ユーザー グループに割り当てるのが一般的です。 新しい Active Directory ユーザー グループの作成する場合は、「[Azure Active Directory でグループを作成し、メンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

リソースの管理に使用するユーザー グループのオブジェクト ID が必要です。 

```powershell
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>ロール定義 ID の取得

次に、ユーザー、ユーザー グループ、アプリケーションへのアクセス権を付与する RBAC の組み込みのロールのロール定義 ID が必要となります。 通常、所有者、共同作成者、閲覧者のいずれかのロールを使います。 次のコマンドは、所有者ロールのロール定義 ID を取得する方法を示しています。

```powershell
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>マネージド アプリケーション定義の作成

マネージ アプリケーション定義を保存するリソース グループがまだない場合は、ここで作成します。

```powershell
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

次に、マネージド アプリケーション定義のリソースを作成します。

```powershell
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>マネージド アプリケーション定義用に独自のストレージを使用する
管理アプリケーション定義は、作成時に指定したストレージ アカウント内に保存することを選択できます。これにより、規制のニーズに合わせて場所とアクセスを完全に管理できます。

> [!NOTE]
> 独自のストレージの使用は、マネージド アプリケーション定義の ARM テンプレートまたは REST API デプロイでのみサポートされます。

### <a name="select-your-storage-account"></a>使うストレージ アカウントを選ぶ
サービス カタログで使用するマネージド アプリケーション定義を含めるための[ストレージ アカウントを作成](../../storage/common/storage-account-create.md)する必要があります。

ストレージ アカウントのリソース ID をコピーします。 これは、後で定義をデプロイするときに使用します。

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>ストレージ アカウントに "アプライアンス リソースプロバイダー" のロールの割り当てを設定する
マネージド アプリケーション定義をストレージ アカウントにデプロイするには、ストレージ アカウントのコンテナーに定義ファイルを書き込むことができるように、事前に**アプライアンス リソースプロバイダー** ロールに共同作成者のアクセス許可を付与する必要があります。

1. [Azure portal](https://portal.azure.com) のストレージ アカウントに移動します。
1. **[アクセス制御 (IAM)]** を選択して、ストレージ アカウントのアクセス制御設定を表示します。 **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。
1. **[ロールの割り当ての追加]** ウィンドウで、 **[共同作成者]** ロールを選択します。 
1. **[アクセスの割り当て先]** フィールドで **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選択します。
1. **[選択]** で、**アプライアンス リソースプロバイダー** ロールを検索して選択します。
1. ロールの割り当てを保存します。

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>ARM テンプレートを使用してマネージド アプリケーション定義をデプロイする 

パッケージ化されたマネージド アプリケーションをサービス カタログの新しいマネージド アプリケーション定義としてデプロイし、その定義ファイルを独自のストレージ アカウントに格納して保持するには、次の ARM テンプレートを使用します。
   
```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "applicationName": {
            "type": "string",
            "metadata": {
                "description": "Managed Application name"
            }
        },
        "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
        "definitionStorageResourceID": {
            "type": "string",
            "metadata": {
                "description": "Storage account resource ID for where you're storing your definition"
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located."
            }
        }
    },
    "variables": {
        "lockLevel": "None",
        "description": "Sample Managed application definition",
        "displayName": "Sample Managed application definition",
        "managedApplicationDefinitionName": "[parameters('applicationName')]",
        "packageFileUri": "[parameters('_artifactsLocation')]",
        "defLocation": "[parameters('definitionStorageResourceID')]",
        "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
        "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/applicationDefinitions",
            "apiVersion": "2019-07-01",
            "name": "[variables('managedApplicationDefinitionName')]",
            "location": "[parameters('location')]",
            "properties": {
                "lockLevel": "[variables('lockLevel')]",
                "description": "[variables('description')]",
                "displayName": "[variables('displayName')]",
                "packageFileUri": "[variables('packageFileUri')]",
                "storageAccountId": "[variables('defLocation')]"
            }
        }
    ],
    "outputs": {}
}
```

applicationDefintion のプロパティに **storageAccountId** という名前の新しいプロパティが追加されています。その値として、対象の定義を格納するストレージ アカウントの ID を指定します。

アプリケーション定義ファイルが、指定されたストレージ アカウントの **applicationdefinitions** という名前のコンテナーに保存されることを確認できます。

> [!NOTE]
> セキュリティを強化するために、マネージド アプリケーション定義を作成し、[暗号化が有効になっている Azure ストレージ アカウント BLOB に保存できます](../../storage/common/storage-service-encryption.md)。 定義の内容は、ストレージ アカウントの暗号化オプションを使用して暗号化されます。 そのファイルに対するアクセス許可を持つユーザーのみが、サービス カタログ内の定義を確認できます。

### <a name="make-sure-users-can-see-your-definition"></a>ユーザーが定義を確認できるようにする

自身がアクセスできるマネージ アプリケーション定義に、組織の他のユーザーもアクセスできることを確認する必要があります。 定義に対して閲覧者以上のロールをユーザーに付与してください。 ユーザーはこのレベルのアクセスをサブスクリプションまたはリソース グループから継承している場合があります。 定義にアクセスできるユーザーを確認し、ユーザーやグループを追加する方法については、「[ロールベースのアクセス制御を使用して Azure サブスクリプション リソースへのアクセスを管理する](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションを Microsoft Azure Marketplace に発行するには、「[Marketplace の Azure マネージド アプリケーション](publish-marketplace-app.md)」を参照してください。
* マネージ アプリケーション インスタンスをデプロイするには、[Azure portal を通したサービス カタログ アプリのデプロイ](deploy-service-catalog-quickstart.md)に関するページを参照してください。
