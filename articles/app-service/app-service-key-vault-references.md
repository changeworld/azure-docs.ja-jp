---
title: Key Vault 参照を使用する
description: Azure Key Vault 参照を使用するように Azure App Service と Azure Functions を設定する方法について説明します。 Key Vault シークレットをアプリケーション コードで使用できるようにします。
author: mattchenderson
ms.topic: article
ms.date: 06/11/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 74341e9f4c2436768a73591878f29e7f392b1f3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006831"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>App Service と Azure Functions の Key Vault 参照を使用する

このトピックでは、コードを変更せず、App Service または Azure Functions アプリケーションの Azure Key Vault のシークレットを使用する方法を紹介します。 [Azure Key Vault](../key-vault/general/overview.md) は、アクセス ポリシーと監査履歴を完全制御する、一元化されたシークレット管理を提供するサービスです。

## <a name="granting-your-app-access-to-key-vault"></a>Key Vault へのアクセス許可をアプリに付与する

Key Vault からシークレットを読み取るには、Key Vault を作成し、それにアクセスする許可をアプリに与える必要があります。

1. [Key Vault クイック スタート](../key-vault/secrets/quick-create-cli.md)に従い、Key Vault を作成してください。

1. アプリケーション用の[マネージド ID](overview-managed-identity.md) を作成します。

    Key Vault 参照では、アプリのシステム割り当て ID が既定で使用されますが、[ユーザー割り当て ID を指定する](#access-vaults-with-a-user-assigned-identity)ことができます。

1. 先に作成したアプリケーション ID に対して、[Key Vault でアクセス ポリシー](../key-vault/general/security-features.md#privileged-access)を作成します。 このポリシーで "Get" シークレット アクセス許可を有効にします。 "承認されているアプリケーション" または `applicationId` 設定を構成しないでください。これは、マネージド ID との互換性がないためです。

### <a name="access-network-restricted-vaults"></a>ネットワーク制限があるコンテナーにアクセスする

コンテナーが[ネットワーク制限](../key-vault/general/overview-vnet-service-endpoints.md)付きで構成されている場合は、アプリケーションがネットワークにアクセスできることを確認する必要もあります。

1. [App Service のネットワーク機能](./networking-features.md)と [Azure Functions のネットワーク オプション](../azure-functions/functions-networking-options.md)のページの説明に従って、アプリケーションの送信ネットワーク機能が構成されていることを確認します。

    プライベート エンドポイントを使用しようとする Linux アプリケーションでは、さらに、すべてのトラフィックを仮想ネットワーク経由でルーティングするようにアプリを明示的に構成する必要があります。 この要件は、今後の更新で削除される予定です。 これを設定するには、次の CLI コマンドを使用します。

    ```azurecli
    az webapp config set --subscription <sub> -g <rg> -n <appname> --generic-configurations '{"vnetRouteAllEnabled": true}'
    ```

2. アプリがアクセスするネットワークまたはサブネットに対するコンテナーの構成アカウントがあることを確認します。

> [!NOTE]
> Windows コンテナーでは、現在のところ、VNet 統合を介した Key Vault の参照はサポートされていません。

### <a name="access-vaults-with-a-user-assigned-identity"></a>ユーザー割り当て ID を使用してコンテナーにアクセスする

一部のアプリでは、作成時の、システム割り当て ID をまだ使用できないときに、シークレットを参照する必要があります。 このような場合は、ユーザー割り当て ID を作成し、コンテナーへのアクセス権を事前に与えることができます。

ユーザー割り当て ID にアクセス許可を付与した後、次の手順のようにします。

1. まだ行っていない場合は、アプリケーションに [ID を割り当てます](./overview-managed-identity.md#add-a-user-assigned-identity)。

1. `keyVaultReferenceIdentity` プロパティにユーザー割り当て ID のリソース ID を設定することにより、この ID を使用して Key Vault 参照操作を行うようアプリを構成します。

    ```azurecli-interactive
    userAssignedIdentityResourceId=$(az identity show -g MyResourceGroupName -n MyUserAssignedIdentityName --query id -o tsv)
    appResourceId=$(az webapp show -g MyResourceGroupName -n MyAppName --query id -o tsv)
    az rest --method PATCH --uri "${appResourceId}?api-version=2021-01-01" --body "{'properties':{'keyVaultReferenceIdentity':'${userAssignedIdentityResourceId}'}}"
    ```

この構成は、アプリのすべての参照に適用されます。

## <a name="reference-syntax"></a>参照構文

Key Vault 参照の形式は `@Microsoft.KeyVault({referenceString})` です。`{referenceString}` は次のいずれかのオプションで置換されます。

> [!div class="mx-tdBreakAll"]
> | 参照文字列                                                            | 説明                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** は、省略可能でバージョン (例: `https://myvault.vault.azure.net/secrets/mysecret/` または `https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`) を含む、Key Vault におけるシークレットのフル データプレーン URI になります。  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** は必須で、Key Vault リソースの名前になります。 **SecretName** は必須で、ターゲット シークレットの名前になります。 **SecretVersion** は省略可能ですが、存在する場合は、使用するシークレットのバージョンを示します。 |

たとえば、完全な参照は次のようになります。

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

あるいは:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>回転

参照でバージョンが指定されていない場合、アプリでは Key Vault に存在する最新バージョンが使用されます。 回転イベントなどにより新しいバージョンが利用できるようになると、アプリは自動的に更新され、24 時間以内に最新バージョンの使用が開始されます。 この遅延は、App Service によって Key Vault 参照の値がキャッシュされ、24 時間ごとに再フェッチされるためです。 アプリの構成を変更すると、参照されているシークレットがすべて直ちに再フェッチされます。

## <a name="source-application-settings-from-key-vault"></a>Key Vault からのソース アプリケーション設定

Key Vault 参照は [[アプリケーション設定]](configure-common.md#configure-app-settings) の値として使用できます。サイト構成ではなく、Key Vault でシークレットを保存できます。格納中のアプリケーション設定は暗号化されて保護されますが、シークレットの管理機能が必要な場合、Key Vault に進む必要があります。

[アプリケーション設定](configure-common.md#add-or-edit)に Key Vault 参照を使用するには、設定の値として参照を設定します。 アプリは通常どおり、そのキーを利用してシークレットを参照できます。 コードに変更を加える必要はありません。

> [!TIP]
> Key Vault 参照を使用するほとんどのアプリケーション設定は、環境ごとに別個の Key Vault を用意する必要があるため、スロット設定としてマークする必要があります。

### <a name="considerations-for-azure-files-mounting"></a>Azure Files のマウントに関する考慮事項

アプリでは、`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` アプリケーション設定を使用して、Azure Files をファイル システムとしてマウントできます。 この設定には、アプリを正しく開始できることを確認するための追加の検証チェックがあります。 プラットフォームでは、Azure Files 内にコンテンツ共有があることを前提としており、`WEBSITE_CONTENTSHARE` 設定で指定されていない場合は既定の名前を想定します。 これらの設定を変更する要求がある場合、プラットフォームでは、このコンテンツ共有が存在するかどうかの検証を試み、存在しない場合は作成を試みます。 コンテンツ共有が見つからないか、作成できない場合、要求はブロックされます。

この設定に対して Key Vault 参照を使用する場合、受信要求の処理中にシークレット自体を解決できないため、この検証チェックは既定で失敗します。 この問題を回避するために、`WEBSITE_SKIP_CONTENTSHARE_VALIDATION` を "1" に設定して検証をスキップできます。 これにより、すべてのチェックがバイパスされ、コンテンツ共有は自動的に作成されなくなります。 事前に作成されていることを確認する必要があります。 

> [!CAUTION]
> 検証をスキップし、接続文字列またはコンテンツ共有が無効である場合、アプリは正常に開始できず、HTTP 500 エラーのみを処理します。

サイト作成の途中で、マネージド ID のアクセス許可が伝達されていないか、仮想ネットワーク統合がセットアップされていないことが原因で、コンテンツ共有のマウント試行が失敗する可能性もあります。 これに対応するために、デプロイ テンプレートの後半まで Azure Files のセットアップを先送りすることができます。 詳細については、「[Azure Resource Manager デプロイ](#azure-resource-manager-deployment)」を参照してください。 App Service では、Azure Files がセットアップされるまでは既定のファイル システムを使用し、ファイルはコピーされないため、Azure Files がマウントされる前の一時的な期間中はデプロイ試行が発生しないようにする必要があります。

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager デプロイ

Azure Resource Manager テンプレート経由でリソース デプロイを自動化するとき、この機能を動作させるには、場合によっては特定の順序で依存関係を並べる必要があります。 注意すべきことですが、サイト定義の `siteConfig` プロパティを使用するのではなく、アプリケーション設定を独自のリソースとして定義する必要があります。 これはサイトを最初に定義する必要があるからです。そうすることで、サイトと共にシステム割り当て ID が作成され、アクセス ポリシーで使用できます。

Function App の擬似テンプレートの例は次のようになります。

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(resourceId('Microsoft.Web/sites/', variables('functionAppName')), '2020-12-01', 'Full').identity.tenantId]",
                        "objectId": "[reference(resourceId('Microsoft.Web/sites/', variables('functionAppName')), '2020-12-01', 'Full').identity.principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2019-09-01').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2019-09-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> この例では、ソース管理デプロイはアプリケーション設定に依存します。 アプリ設定は非同期で更新されるため、通常、これは安全ではありません。 しかしながら、`WEBSITE_ENABLE_SYNC_UPDATE_SITE` アプリケーション設定を含めているため、同期で更新されます。 つまり、ソース管理デプロイは、アプリケーションが完全に更新されたときにのみ開始されます。 他のアプリの設定については、「[Azure App Service の環境変数とアプリ設定](reference-app-settings.md)」を参照してください。

## <a name="troubleshooting-key-vault-references"></a>Key Vault 参照のトラブルシューティング

参照が正しく解決されない場合は、代わりに参照値が使用されます。 つまり、アプリケーションの設定では、値の構文が `@Microsoft.KeyVault(...)` である環境変数が作成されます。 この場合に、アプリケーションで特定の構造のシークレットが想定されていると、エラーがスローされることがあります。

最も一般的な原因は、[Key Vault アクセス ポリシー](#granting-your-app-access-to-key-vault)が正しく構成されていないことです。 ただし、シークレットが既に存在していないか、参照自体の構文エラーが原因である可能性もあります。

構文が正しい場合は、ポータルで現在の解決状態を確認して、エラーの他の原因を確認できます。 [アプリケーションの設定] に移動し、該当する参照の [編集] を選択します。 設定の構成の下には、エラーなどの状態情報が表示されます。 表示されない場合は、参照構文が無効であることを意味します。

組み込みの検出機能のいずれかを使用して、追加情報を取得することもできます。

### <a name="using-the-detector-for-app-service"></a>App Service の検出機能の使用

1. ポータルで、アプリに移動します。
2. **[Diagnose and solve prolems]\(問題の診断と解決\)** を選択します。
3. **[Availability and Performance]\(可用性とパフォーマンス\)** を選択し、 **[Web app down]\(Web アプリのダウン\)** を選択します。
4. **[Key Vault Application Settings Diagnostics]\(Key Vault のアプリケーション設定の診断\)** を見つけて、 **[詳細情報]** をクリックします。


### <a name="using-the-detector-for-azure-functions"></a>Azure Functions の検出機能の使用

1. ポータルで、アプリに移動します。
2. **[プラットフォーム機能]** に移動します。
3. **[Diagnose and solve prolems]\(問題の診断と解決\)** を選択します。
4. **[Availability and Performance]\(可用性とパフォーマンス\)** を選択し、 **[Function app down or reporting errors]\(関数アプリのダウンまたはエラーの報告\)** を選択します。
5. **[Key Vault Application Settings Diagnostics]\(Key Vault のアプリケーション設定の診断\)** をクリックします。
