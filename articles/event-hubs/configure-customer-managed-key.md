---
title: Azure Event Hubs 保存データの暗号化用に独自のキーを構成する
description: この記事では、Azure Event Hubs の保存データを暗号化するために独自のキーを構成する方法について説明します。
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: ed230cc1e0a377a580e0f23feeac74f6e0b5489d
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515708"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest"></a>Azure Event Hubs の保存データを暗号化するためにカスタマー マネージド キーを構成する
Azure Event Hubs では、Azure Storage Service Encryption (Azure SSE) による保存データの暗号化が提供されます。 Event Hubs サービスには、データを格納するために Azure Storage が使用されます。 Azure Storage に格納されているすべてのデータは、Microsoft マネージド キーを使用して暗号化されます。 独自のキー (Bring Your Own Key (BYOK) またはカスタマーマネージド キーとも呼ばれます) を使用する場合、データは引き続き Microsoft マネージド キーを使用して暗号化されますが、さらに Microsoft マネージド キーはカスタマー マネージド キーを使用して暗号化されます。 この機能を使用して、Microsoft マネージド キーの暗号化に使用されるカスタマー マネージド キーへの作成、ローテーション、無効化、およびアクセスの取り消しを実行できます。 BYOK 機能の有効化は、名前空間での 1 回限りのセットアップ プロセスです。

> [!IMPORTANT]
> - BYOK 機能は、Event Hubs の **Premium** レベルおよび **Dedicated** レベルのサービスでサポートされます。
> - 暗号化は、新規または空の名前空間に対してのみ有効にすることができます。 名前空間にイベント ハブが含まれている場合、暗号化操作は失敗します。

キーの管理およびキーの使用状況の監査には、Azure Key Vault を使用できます。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../key-vault/general/overview.md)

この記事では、Azure portal を使って、カスタマー マネージド キーでキー コンテナーを構成する方法について説明します。 Azure portal を使ってキー コンテナーを作成する方法を学習するには、「[クイック スタート: Azure portal を使用して Azure Key Vault を作成する](../key-vault/general/quick-create-portal.md)」を参照します。

## <a name="enable-customer-managed-keys-azure-portal"></a>カスタマー マネージド キーを有効にする (Azure portal)
Azure portal でカスタマー マネージド キーを有効にするには、次の手順のようにします。 Dedicated レベルを使用している場合は、まず Event Hubs Dedicated クラスターに移動します。

1. BYOK を有効にする名前空間を選択します。
1. Event Hubs 名前空間の **[設定]** ページで、 **[暗号化]** を選択します。 
1. 次の図に示すように、**カスタマー マネージド キーによる保存中の暗号化** を選択します。 

    ![カスタマー マネージド キーの有効化](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>キーを使用したキー コンテナーの設定
カスタマー マネージド キーを有効にした後、カスタマー マネージド キーを Azure Event Hubs 名前空間に関連付ける必要があります。 Event Hubs では Azure Key Vault のみがサポートされています。 前のセクションで **カスタマー マネージド キーによる暗号化** のオプションを有効にした場合は、キーが Azure Key Vault にインポートされている必要があります。 また、キーに対して **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** が構成されている必要があります。 これらの設定は、[PowerShell](../key-vault/general/key-vault-recovery.md) または [CLI](../key-vault/general/key-vault-recovery.md) を使用して構成できます。

1. 新しいキー コンテナーを作成するには、Azure Key Vault の[クイック スタート](../key-vault/general/overview.md)に従ってください。 既存のキーをインポートする方法については、「[キー、シークレット、証明書について](../key-vault/general/about-keys-secrets-certificates.md)」を参照してください。

    > [!IMPORTANT]
    > Azure Event Hubs でカスタマー マネージド キーを使うには、キー コンテナーに 2 つの必須プロパティが構成されている必要があります。 これらは次のとおりです。 **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** です。 Azure portal で新しいキー コンテナーを作成すると、これらのプロパティは既定で有効になります。 ただし、既存のキー コンテナーでこれらのプロパティを有効にする必要がある場合は、PowerShell または Azure CLI を使う必要があります。
1. コンテナーの作成時、論理的な削除と消去保護の両方をオンにするには、[az keyvault create](/cli/azure/keyvault#az_keyvault_create) コマンドを使用します。

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 既存のコンテナー (論理的な削除が既に有効になっている) に消去保護を追加するには、[az keyvault update](/cli/azure/keyvault#az_keyvault_update) コマンドを使用します。

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 次の手順に従ってキーを作成します。
    1. 新しいキーを作成するには、 **[設定]** の下にある **[キー]** メニューから **[生成/インポート]** を選択します。
        
        ![[Generate/Import]\(生成/インポート\) ボタンの選択](./media/configure-customer-managed-key/select-generate-import.png)
    1. **[オプション]** を **[生成]** に設定し、キーの名前を付けます。

        ![キーの作成](./media/configure-customer-managed-key/create-key.png) 
    1. 次に、ドロップダウン リストからこのキーを選択して、暗号化用の Event Hubs 名前空間に関連付けることができます。 

        ![Key Vault からのキーの選択](./media/configure-customer-managed-key/select-key-from-key-vault.png)

        > [!NOTE]
        > 冗長性を確保するために、最大 3 つのキーを追加できます。 いずれかのキーの有効期限が切れた場合、またはアクセスできない場合は、他のキーが暗号化に使用されます。
    1. キーの詳細を入力し、 **[選択]** をクリックします。 これにより、Microsoft マネージド キーを自分のキー (カスタマー マネージド キー) で暗号化できます。 

## <a name="managed-identities"></a>マネージド ID
1 つの Event Hubs 名前空間に割り当て可能なマネージド ID は 2 種類あります。

- **システム割り当て**: Event Hubs 名前空間でマネージド ID を直接有効にできます。 システム割り当てマネージド ID を有効にすると、その Event Hubs 名前空間のライフサイクルに関連付けられている Azure AD 内に ID が作成されます。 そのため、名前空間が削除されると、Azure によってその ID が自動的に削除されます。 その ID を使用して Azure AD にトークンを要求できるのは、必然的に、その Azure リソース (名前空間) のみとなります。
- **ユーザー割り当て:** スタンドアロンの Azure リソースとしてマネージド ID を自分で作成することもできます。これがユーザー割り当て ID と呼ばれます。 ユーザー割り当てマネージド ID を作成して、それを 1 つ以上の Event Hubs 名前空間に割り当てることができます。 ユーザー割り当てマネージド ID の場合、ID は、それを使用するリソースとは別に管理されます。 これらは、名前空間のライフサイクルに関連付けられていません。 ユーザー割り当て ID は、不要になったときに明示的に削除できます。    

    詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。


## <a name="encrypt-using-system-assigned-identities-template"></a>システム割り当て ID を使用して暗号化する (テンプレート)
このセクションでは、**Azure Resource Manager テンプレート** を使用して次のタスクを実行する方法を示します。 

1. マネージド サービス ID がある **Event Hubs 名前空間** を作成する。
2. **キー コンテナー** を作成し、サービス ID にキー コンテナーへのアクセス権を付与する。 
3. キー コンテナー情報 (キー/値) を使用して、Event Hubs 名前空間を更新する。 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>マネージド サービス ID がある Event Hubs クラスターと名前空間を作成する。
このセクションでは、Azure Resource Manager テンプレートと PowerShell を使用して、マネージド サービス ID がある Azure Event Hubs 名前空間を作成する方法について説明します。 

1. マネージド サービス ID がある Azure Event Hubs 名前空間を作成するための Azure Resource Manager テンプレートを作成します。 ファイルに次の名前を付けます。**CreateEventHubClusterAndNamespace.json** 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. 次の名前のテンプレート パラメーター ファイルを作成します。**CreateEventHubClusterAndNamespaceParams.json** 

    > [!NOTE]
    > 次の値を置き換えます。 
    > - `<EventHubsClusterName>` - お使いの Event Hubs クラスターの名前    
    > - `<EventHubsNamespaceName>` - お使いの Event Hubs 名前空間の名前
    > - `<Location>` - お使いの Event Hubs 名前空間の場所

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. 次の PowerShell コマンドを実行して、Event Hubs 名前空間を作成するテンプレートをデプロイします。 次に、後で使用するために Event Hubs 名前空間の ID を取得します。 コマンドを実行する前に、`{MyRG}` をリソース グループの名前に置き換えます。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Event Hubs 名前空間の ID にキー コンテナーへのアクセス権を付与する

1. 次のコマンドを実行して、**消去保護** と **論理的な削除** が有効なキー コンテナーを作成します。 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (または)    
    
    次のコマンドを実行して、**既存のキー コンテナー** を更新します。 コマンドを実行する前に、リソース グループとキー コンテナー名の値を指定します。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. キー コンテナーのアクセス ポリシーを設定して、Event Hubs 名前空間のマネージド ID で、キー コンテナー内のキー値にアクセスできるようにします。 前のセクションの Event Hubs 名前空間の ID を使用します。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Event Hubs 名前空間のデータをキー コンテナーのユーザーが管理するキーを使用して暗号化する
ここまで、次の手順を実行しました。 

1. マネージド ID がある Premium 名前空間を作成しました。
2. キー コンテナーを作成し、マネージド ID にキー コンテナーへのアクセス権を付与しました。 

この手順では、キー コンテナー情報を使用して、Event Hubs 名前空間を更新します。 

1. 次の内容の **CreateEventHubClusterAndNamespace.json** という名前の JSON ファイルを作成します。 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. 次のテンプレート パラメーター ファイルを作成します。**UpdateEventHubClusterAndNamespaceParams.json** 

    > [!NOTE]
    > 次の値を置き換えます。 
    > - `<EventHubsClusterName>` - お使いの Event Hubs クラスターの名前        
    > - `<EventHubsNamespaceName>` - お使いの Event Hubs 名前空間の名前
    > - `<Location>` - お使いの Event Hubs 名前空間の場所
    > - `<KeyVaultName>` - お使いのキー コンテナーの名前
    > - `<KeyName>` - キー コンテナー内のキーの名前

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. 次の PowerShell コマンドを実行して、Resource Manager テンプレートをデプロイします。 コマンドを実行する前に、`{MyRG}` をお使いのリソース グループの名前に置き換えます。 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="encrypt-using-user-assigned-identities-template"></a>ユーザー割り当て ID を使用して暗号化する (テンプレート)

1. **ユーザー割り当て ID** を作成します。
1. **キー コンテナー** を作成し、アクセス ポリシーによってユーザー割り当て ID へのアクセスを付与します。
1. マネージド ユーザー ID とキー コンテナーの情報を使用して、**Event Hubs 名前空間** を作成します。

### <a name="create-a-user-assigned-identity"></a>ユーザー割り当て ID を作成する
記事「[ユーザー割り当てマネージド ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)」の手順に従ってユーザー割り当て ID を作成します。 ユーザー割り当て ID は、[CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)、[PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)、[Azure Resource Manager テンプレート](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)、[REST](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md) を使用して作成することもできます。 

> [!NOTE]
> 1 つの名前空間には、ユーザー ID を **4 つ** まで割り当てることができます。 これらの関連付けは、名前空間が削除されたとき、またはテンプレート内で `identity -> type` が `None` に渡されたときに削除されます。 

### <a name="create-a-key-vault-and-grant-access-to-user-assigned-identity"></a>キー コンテナーを作成し、ユーザー割り当て ID へのアクセスを付与する 

1. 次のコマンドを実行して、消去保護と論理的な削除が有効なキー コンテナーを作成します。

    ```azurepowershell-interactive
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName} -Location "{location}" -EnableSoftDelete -EnablePurgeProtection           
    ```
    
    (または)

    次のコマンドを実行して、既存のキー コンテナーを更新します。 コマンドを実行する前に、リソース グループとキー コンテナー名の値を指定します。

    ```azurepowershell-interactive
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force            
    ```
2. 次の PowerShell コマンドを使用して、ユーザー ID の **サービス プリンシパル ID** を取得します。 この例では `ud1` が、暗号化に使用されるユーザー割り当て ID です。

    ```azurepowershell-interactive
    $servicePrincipal=Get-AzADServicePrincipal -SearchString "ud1"    
    ```
3. アクセス ポリシーを割り当てることで、ユーザー割り当て ID に、キー コンテナーへのアクセスを付与します。     

    ```azurepowershell-interactive
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $servicePrincipal.Id -PermissionsToKeys get,wrapKey,unwrapKey,list    
    ```

    > [!NOTE]
    > キーは **3 つ** まで追加できますが、暗号化に使用されるユーザー ID は、すべてのキーで同じである必要があります。 現時点では、1 つの暗号化 ID だけがサポートされています。 

### <a name="create-an-event-hubs-namespace-with-user-identity-and-key-vault-information"></a>ユーザー ID とキー コンテナーの情報を使用して、Event Hubs 名前空間を作成します。
このセクションでは、Azure Resource Manager テンプレートを使用して以下のタスクを実行する方法を示す例を紹介します。 

- ユーザー マネージド ID を Event Hubs 名前空間に割り当てます。

    ```json
                "identity": {
                    "type": "UserAssigned",
                    "userAssignedIdentities": {
                        "[parameters('identity').userAssignedIdentity]": {}
                    }
                },
    ```    
- キー コンテナーのキーと、キーにアクセスするためのユーザー マネージド ID を指定して、名前空間での暗号化を有効にします。 

    ```json
                    "encryption":{
                       "keySource":"Microsoft.KeyVault",
                       "keyVaultProperties":[
                            {
                                "keyName": "[parameters('keyName')]",
                                "keyVaultUri": "[parameters('keyVaultUri')]",
                                "identity": {
                                    "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
                                }
                            }
                       ]
                    }
    ```
   

1. 以下の内容の、**CreateEventHubsNamespaceWithUserIdentityAndEncryption.json** という名前の JSON ファイルを作成します。

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
        "clusterName":{
            "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             },
         "identity": {
            "type": "Object",
            "defaultValue": {
                "userAssignedIdentity": ""
            },
            "metadata": {
                "description": "user-assigned identity."
            }
         }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2021-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[parameters('identity').userAssignedIdentity]": {}
                }
            },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                        {
                            "keyName": "[parameters('keyName')]",
                            "keyVaultUri": "[parameters('keyVaultUri')]",
                            "identity": {
                                "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
                            }
                        }
                   ]
                }
             }
          }
       ]
    }        
    ```  
1. テンプレート パラメーター ファイル **CreateEventHubsNamespaceWithUserIdentityAndEncryptionParams.json** を作成します。

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "identity": {
            "value": {
                "userAssignedIdentity": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER MANAGED IDENTITY NAME>"
            }
         }
       }
    }
    ```

    パラメーター ファイル内で、プレースホルダーを適切な値に置き換えます。
    
    | プレースホルダー | value | 
    | ----------- | ----- | 
    | `<EventHubsNamespaceName>` | Event Hubs 名前空間の名前。 | 
    | `<Location>` | 名前空間を作成する場所。 | 
    | `<KeyVaultName>` | キー コンテナーの名前。 | 
    | `<KeyName>` | キー コンテナー内のキーの名前。 | 
    | `<AZURE SUBSCRIPTION ID>` | Azure のサブスクリプション ID。 |
    | `<RESOURCE GROUP NAME>` | ユーザー マネージド ID のリソース グループ。 | 
    | `<USER MANAGED IDENTITY NAME>` | ユーザー マネージド ID の名前。 | 

3. 次の PowerShell コマンドを実行して、Resource Manager テンプレートをデプロイします。 コマンドを実行する前に、`{MyRG}` をお使いのリソース グループの名前に置き換えます。

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name CreateEventHubsNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./ CreateEventHubsNamespaceWithUserIdentityAndEncryption.json -TemplateParameterFile ./ CreateEventHubsNamespaceWithUserIdentityAndEncryptionParams.json        
    ```


## <a name="use-both-user-assigned-and-system-assigned-identities"></a>ユーザー割り当て ID とシステム割り当て ID の両方を使用する
名前空間は、システム割り当て ID とユーザー割り当て ID の両方を同時に持つことができます。 この場合 `type` プロパティは、次の例に示すように、`SystemAssigned`, `UserAssigned` になります。 

```json
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userIdentity1>" : {}
    }
}
```

このシナリオでは、保存データを暗号化するために、システム割り当て ID またはユーザー割り当て ID のいずれかを選択できます。  

Resource Manager テンプレートでは、`identity` 属性を指定しない場合、システム マネージド ID が使用されます。 次にスニペットの例を示します。 

```json
"properties":{
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]"
         }
      ]
   }
}
```

暗号化のためにユーザー マネージド ID を使用することについては、次の例を参照してください。 `identity` 属性が、ユーザー マネージド ID に設定されていることに注目してください。 

```json
"properties":{
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]",
            "identity": {
                "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
            }
         }
      ]
   }
}
```

## <a name="enable-infrastructure-or-double-encryption-of-data"></a>データのインフラストラクチャの (または二重) 暗号化を有効にする
データがセキュリティで保護されていることについて、より高いレベルの保証が必要な場合は、インフラストラクチャ レベルの暗号化 (二重暗号化とも呼ばれる) を有効にできます。 

インフラストラクチャの暗号化が有効な場合、Event Hubs 名前空間アカウント内のデータは、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用して、2 回 (サービス レベルで 1 回とインフラストラクチャ レベルで 1 回) 暗号化されます。 Event Hubs データのインフラストラクチャ暗号化を使用すると、暗号化アルゴリズムまたはキーのいずれかが侵害される可能性があるシナリオから保護されます。

インフラストラクチャ暗号化を有効にするには、下に示すように、上記の **CreateEventHubClusterAndNamespace.json** で `requireInfrastructureEncryption` プロパティを使用して Azure Resource Manager テンプレートを更新します。 

```json
"properties":{
   "isAutoInflateEnabled":false,
   "maximumThroughputUnits":0,
   "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "requireInfrastructureEncryption":true,
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]"
         }
      ]
   }
}
```

## <a name="rotate-revoke-and-cache-encryption-keys"></a>暗号化キーのローテーション、取り消し、キャッシュ

### <a name="rotate-your-encryption-keys"></a>暗号化キーのローテーション
Azure Key Vault のローテーション メカニズムを使用して、キー コンテナー内のキーをローテーションできます。 アクティブ化と有効期限の日付を設定してキー ローテーションを自動化することもできます。 Event Hubs サービスによって新しいキーのバージョンが検出され、自動的に使用が開始されます。

### <a name="revoke-access-to-keys"></a>キーへのアクセスの取り消し
暗号化キーへのアクセスを取り消しても、Event Hubs からデータが消去されることはありません。 ただし、Event Hubs 名前空間からデータへのアクセスはできません。 暗号化キーは、アクセス ポリシーを使用して、またはキーを削除することで取り消すことができます。 アクセス ポリシーと、キー コンテナーのセキュリティ保護の詳細については、「[キー コンテナーへのアクセスをセキュリティで保護する](../key-vault/general/security-features.md)」を参照してください。

暗号化キーを取り消すと、暗号化した名前空間で Event Hubs サービスが機能しなくなります。 キーへのアクセスを有効にするか、削除キーを復元すると、Event Hubs サービスによってキーが選択され、暗号化した Event Hubs 名前空間からデータにアクセスできるようになります。

### <a name="caching-of-keys"></a>キーのキャッシュ
Event Hubs インスタンス (イベント ハブ) により、5 分ごとに一覧の暗号化キーがポーリングされます。 それらは、次のポーリング (5 分後) までキャッシュされて使用されます。 少なくとも 1 つのキーが使用可能である限り、イベント ハブにアクセスできます。 ポーリング時に一覧のすべてのキーにアクセスできない場合は、すべてのイベント ハブが使用できなくなります。 

詳細を次に示します。 

- Event Hubs サービスは、5 分ごとに名前空間のレコードに記載されているすべてのカスタマー マネージド キーをポーリングします。
    - キーがローテーションされている場合、このレコードは新しいキーで更新されます。
    - キーが取り消されている場合、そのキーはレコードから削除されます。
    - すべてのキーが取り消されている場合は、名前空間の暗号化状態が **[取り消し済み]** に設定されます。 Event Hubs 名前空間からデータにはアクセスできません。

## <a name="considerations-when-using-geo-disaster-recovery"></a>geo ディザスター リカバリー使用時の考慮事項

> [!IMPORTANT]
> BYOK 暗号化を使用している名前空間で Geo-DR を有効にするには、ペアリング対象のセカンダリ名前空間に、有効なシステム割り当てまたはユーザー割り当てマネージド ID が存在する必要があります。 

### <a name="geo-disaster-recovery---encryption-with-system-assigned-identities"></a>geo ディザスター リカバリー - システム割り当て ID を使用した暗号化
カスタマー マネージド キーを使用した Microsoft マネージド キーの暗号化を有効にするために、指定した Azure キー コンテナー上のシステム割り当てマネージド ID に対して[アクセス ポリシー](../key-vault/general/secure-your-key-vault.md)が設定されます。 これにより、Azure Event Hubs 名前空間から Azure キー コンテナーへのアクセスが制御されます。

これにより:

- Event Hubs 名前空間に対して [geo ディザスター リカバリー](event-hubs-geo-dr.md)が既に有効になっているときに、カスタマー マネージド キーを有効化する場合は、以下を行います。
    - ペアリングを解除します。
    - プライマリとセカンダリの両方の名前空間について、キー コンテナーに、システム割り当てマネージド ID 用の[アクセス ポリシーを設定](../key-vault/general/assign-access-policy-portal.md)します。
    - プライマリ名前空間に暗号化を設定します。
    - プライマリとセカンダリの名前空間を再びペアリングします。
- カスタマー マネージド キーが既に設定されている Event Hubs 名前空間で Geo DR を有効にしようとしている場合は、次の手順に従います。 
    - キー コンテナーに対して、セカンダリ名前空間のマネージド ID を対象とする[アクセス ポリシー を設定](../key-vault/general/assign-access-policy-portal.md)します。
    - プライマリとセカンダリの名前空間をペアリングします。

### <a name="geo-disaster-recovery---encryption-with-user-assigned-identities"></a>geo ディザスター リカバリー - ユーザー割り当て ID を使用した暗号化
以下に、推奨事項をいくつか示します。 

1.  マネージド ID を作成し、マネージド ID に Key Vault のアクセス許可を割り当てます。 
2.  ID をユーザー割り当て ID として追加し、両方の名前空間で、その ID を使用した暗号化を有効にします。 
3.  名前空間をペアにします。 

ユーザー割り当て ID を使用して Geo DR と暗号化を有効にするための条件:

1.  セカンダリ名前空間を、暗号化が有効になっているプライマリ名前空間とペアにする必要がある場合は、ユーザー割り当て ID を使用してセカンダリ名前空間で既に暗号化が有効になっている必要があります。 
2.  セカンダリに、名前空間に関連付けられているユーザー割り当て ID がある場合でも、既にペアになっているプライマリで暗号化を有効にすることはできません。

## <a name="set-up-diagnostic-logs"></a>診断ログの設定 
BYOK が有効な名前空間の診断ログを設定すると、操作に関する必要な情報が得られます。 これらのログを有効にして、後からイベント ハブにストリーミングしたり、ログ分析を使用して分析したり、ストレージにストリーミングしてカスタマイズした分析を実行したりできます。 診断ログの詳細については、「[Azure 診断ログの概要](../azure-monitor/essentials/platform-logs-overview.md)」を参照してください。 スキーマについては、「[データ参照を監視する](monitor-event-hubs-reference.md#customer-managed-key-user-logs-schema)」を参照してください。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。
- [Event Hubs の概要](event-hubs-about.md)
- [Key Vault の概要](../key-vault/general/overview.md)