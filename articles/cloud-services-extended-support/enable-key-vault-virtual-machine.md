---
title: Key Vault VM 拡張機能を Azure Cloud Services (延長サポート) に適用する
description: Cloud Services (延長サポート) の Key Vault VM 拡張機能を有効にする
ms.topic: how-to
ms.service: cloud-services-extended-support
author: shisriva
ms.author: shisriva
ms.reviewer: gachandw
ms.date: 05/12/2021
ms.custom: ''
ms.openlocfilehash: 27a25cbff54acda871530daca455045e5519c936
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104904"
---
# <a name="apply-the-key-vault-vm-extension-to-azure-cloud-services-extended-support"></a>Key Vault VM 拡張機能を Azure Cloud Services (延長サポート) に適用する

## <a name="what-is-the-key-vault-vm-extension"></a>Key Vault VM 拡張機能とは
Key Vault VM 拡張機能を使用すると、Azure キー コンテナーに保存されている証明書が自動更新されます。 この拡張機能は、具体的には、キー コンテナーに保存されている観察対象の証明書の一覧を監視し、変更が検出されると対応する証明書を取得してインストールします。 詳細については、[Windows 用の Key Vault VM 拡張機能](../virtual-machines/extensions/key-vault-windows.md)に関する記事を参照してください。

## <a name="whats-new-in-the-key-vault-vm-extension"></a>Key Vault VM 拡張機能の新機能
Key Vault VM 拡張機能は Azure Cloud Services (延長サポート) プラットフォームでサポートされたため、証明書をエンド ツー エンドで管理できるようになりました。 拡張機能を使用すると、事前に定義されたポーリング間隔で、構成済みの Key Vault から証明書をプルしてインストールし、サービスで使用できるようになります。 

## <a name="how-can-i-leverage-the-key-vault-vm-extension"></a>Key Vault VM 拡張機能の使用方法
次のチュートリアルでは、まずコンテナーにブートストラップ証明書を作成し、コンテナーで拡張機能を認証できるようにするトークンを AAD から取得することで、PaaSV1 サービスに Key Vault VM 拡張機能をインストールする方法について説明します。 認証プロセスが設定され、拡張機能がインストールされると、すべての最新の証明書が定期的なポーリング間隔で自動的にプルされます。 


## <a name="prerequisites"></a>前提条件 
Azure Key Vault VM 拡張機能を使用するには、Azure Active Directory テナントが必要です。 新しい Active Directory テナントの設定方法の詳細については、[AAD テナントの設定](../active-directory/develop/quickstart-create-new-tenant.md)に関する記事を参照してください

## <a name="enable-the-azure-key-vault-vm-extension"></a>Azure Key Vault VM 拡張機能を有効にする

1. コンテナーで[証明書を生成し](../key-vault/certificates/create-certificate-signing-request.md)、その証明書の .cer をダウンロードします。

2. [Azure portal](https://portal.azure.com) で、 **[アプリの登録]** に移動します。
    
    :::image type="content" source="media/app-registration-0.jpg" alt-text="ポータルで [アプリの登録] を選択する方法を示すスクリーンショット。":::
    

3. [アプリの登録] ページで、ページの左上隅にある **[新規登録]** を選択します
    
    :::image type="content" source="media/app-registration-1.png" alt-text="Azure portal の [アプリの登録] を示すスクリーンショット。":::

4. 次のページでフォームに入力すると、アプリの作成を完了できます。

5. 証明書の .cer を Azure Active Directory アプリ ポータルにアップロードします。

    - 必要に応じて、[Key Vault の Event Grid 通知機能](https://azure.microsoft.com/updates/azure-key-vault-event-grid-integration-is-now-available/)を利用して証明書をアップロードすることもできます。  

6. Key Vault に Azure Active Directory アプリ シークレットの一覧表示および取得アクセス許可を付与します。
    - RBAC プレビューを使用している場合は、作成した Azure AAD アプリの名前を検索し、それを Key Vault Secrets User (プレビュー) ロールに割り当てます。
    - コンテナー アクセス ポリシーを使用している場合は、作成した AAD アプリに **シークレットの取得** アクセス許可を割り当てます。 詳細については、[アクセス ポリシーの割り当て](../key-vault/general/assign-access-policy-portal.md)に関する記事を参照してください

7. 次に示すように ARM テンプレートを使用して、最初の手順で作成した証明書の最初のバージョンをインストールし、Key Vault VM 拡張機能をインストールします。

    ```json
        {
       "osProfile":{
          "secrets":[
             {
                "sourceVault":{
                   "id":"[parameters('sourceVaultValue')]"
                },
                "vaultCertificates":[
                   {
                      "certificateUrl":"[parameters('bootstrpCertificateUrlValue')]"
                   }
                ]
             }
          ]
       }{
          "name":"KVVMExtensionForPaaS",
          "properties":{
             "type":"KeyVaultForPaaS",
             "autoUpgradeMinorVersion":true,
             "typeHandlerVersion":"1.0",
             "publisher":"Microsoft.Azure.KeyVault",
             "settings":{
                "secretsManagementSettings":{
                   "pollingIntervalInS":"3600",
                   "certificateStoreName":"My",
                   "certificateStoreLocation":"LocalMachine",
                   "linkOnRenewal":false,
                   "requireInitialSync":false, 
                   "observedCertificates":"[parameters('keyVaultObservedCertificates']"
                },
                "authenticationSettings":{
                   "clientId":"Your AAD app ID",
                   "clientCertificateSubjectName":"Your boot strap certificate subject name [Do not include the 'CN=' in the subject name]"
                }
             }
          }
       }
    ```
    場合によっては、次のように、ServiceDefinition.csdef に、ブートストラップ証明書の証明書ストアを指定する必要があります。
    
    ```xml
        <Certificates>
                 <Certificate name="bootstrapcert" storeLocation="LocalMachine" storeName="My" />
        </Certificates> 
    ```

## <a name="next-steps"></a>次の手順
[Cloud Services (延長サポート) での監視を有効にする](enable-alerts.md)ことによって、デプロイをさらに向上させる