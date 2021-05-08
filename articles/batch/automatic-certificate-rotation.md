---
title: Batch プールで証明書の自動ローテーションを有効にする
description: マネージド ID と自動的に更新される証明書を使用して、Batch プールを作成できます。
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: e8bea49b2980deb8f20258ab7ea5619ece8cd2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962304"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Batch プールで証明書の自動ローテーションを有効にする

 自動的に更新される証明書を使用して Batch プールを作成できます。 これを行うには、[Azure Key Vault](../key-vault/general/overview.md) で証明書にアクセスできる[ユーザー割り当てマネージド ID](managed-identity-pools.md)を使用してプールを作成する必要があります。

> [!IMPORTANT]
> ユーザー割り当てマネージド ID を使用する Azure Batch プールに対するサポートは、現在、次のリージョンを対象にパブリック プレビュー段階にあります。米国西部 2、米国中南部、米国東部、US Gov アリゾナ、US Gov バージニア。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="create-a-user-assigned-identity"></a>ユーザー割り当て ID を作成する

まず、[ユーザー割り当てマネージド ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) を Batch アカウントと同じテナントに作成します。 このマネージド ID は、同じリソース グループ内または同じサブスクリプション内に存在する必要はありません。

ユーザー割り当てマネージド ID の **クライアント ID** をメモします。 この値は後で必要になります。

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Azure portal 内のユーザー割り当てマネージド ID のクライアント ID を示すスクリーンショット。":::

## <a name="create-your-certificate"></a>証明書を作成する

次に、証明書を作成して Azure Key Vault に追加する必要があります。 まだキー コンテナーを作成していない場合は、まずそれを行う必要があります。 手順については、「[クイック スタート:Azure portal を使用して Azure Key Vault から証明書の設定と取得を行う](../key-vault/certificates/quick-create-portal.md)」を参照してください。

証明書を作成するときは、 **[有効期間のアクション タイプ]** が自動的に更新されるように設定し、証明書を更新するまでの日数を指定してください。

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Azure portal の [証明書のインポート] ペインのスクリーンショット。":::

証明書が作成されたら、その **シークレット識別子** をメモします。 この値は後で必要になります。

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="証明書のシークレット識別子を示すスクリーンショット。":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Azure Key Vault にアクセス ポリシーを追加する

キー コンテナーで、ユーザー割り当てマネージド ID でシークレットと証明書へのアクセスを許可する Key Vault アクセス ポリシーを割り当てます。 詳細な手順については、「[Azure portal を使用して Key Vault アクセス ポリシーを割り当てる](../key-vault/general/assign-access-policy-portal.md)」を参照してください。

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を使用して Batch プールを作成する

[Batch .NET 管理ライブラリ](/dotnet/api/overview/azure/batch#management-library)を使用して、マネージド ID で Batch プールを作成します。 詳細については、「[Batch プールでマネージド ID を構成する](managed-identity-pools.md)」を参照してください。

次の例では、Batch 管理 REST API を使用してプールを作成します。 `observedCertificates` の証明書の **シークレット識別子** と、`msiClientId` のマネージ ID の **クライアント ID** を使用して、以下のサンプル データを置き換えてください。

REST API URI

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

要求本文

```json
{
    "name": "test2",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "KVExtensions",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>証明書を検証する

証明書が正常にデプロイされたことを確認するには、コンピューティング ノードにログインします。 次のような出力が表示されます。

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>次のステップ

- [Azure リソース用マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) 詳細について説明します。
- [ユーザー マネージド ID でカスタマー マネージド キー](batch-customer-managed-key.md)を使用する方法について説明します。
