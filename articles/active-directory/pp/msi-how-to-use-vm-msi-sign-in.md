---
title: "Azure VM の管理対象サービス ID を使用してサインインする方法"
description: "スクリプト クライアント サインインおよびリソース アクセスに Azure VM の MSI サービス プリンシパルを使用する方法の例と詳しい手順を説明します。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/05/2018
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c5f71d27a9e07cc6d6a260b809e91aaa2a50270c
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2018
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>VM ユーザーが割り当てられた Managed Service Identity (MSI) を使用したサインイン

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)] この記事は、ユーザーが割り当てられた MSI のサービス プリンシパルを使用してサインインするための CLI スクリプトの例を示し、エラー処理などの重要なトピックに関するガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

この記事の Azure CLI の例を使用する場合は、[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) の最新バージョンをインストールする必要があります。 

> [!IMPORTANT]
> - この記事のすべてのサンプル スクリプトでは、コマンドライン クライアントが、MSI 対応の仮想マシンで実行されていることを前提としています。 お使いの VM にリモート接続するには、Azure ポータルで VM への "接続" 機能を使用します。 VM で MSI を有効にする方法の詳細については、「[Azure CLI を使用して、VM 管理対象サービス ID (MSI) を構成する](msi-qs-configure-cli-windows-vm.md)」、または関連する記事 (PowerShell、ポータル、テンプレート、または Azure SDK を使用) のいずれかを参照してください。 
> - サインインおよびリソースへのアクセス中のエラーを回避するには、VM 上の Azure Resource Manager の操作を許可するために、MSI に適切なスコープ (VM 以上) の "読み取り" アクセス以上のアクセス権が付与されている必要があります。 詳細については、「[Azure CLI を使用してリソースに管理対象サービス ID (MSI) アクセスを割り当てる](msi-howto-assign-access-cli.md)」を参照してください。

## <a name="overview"></a>概要

MSI は、VM 上で [MSI を有効化したときに作成された](msi-overview.md#how-does-it-work)、[サービス プリンシパル](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object)を提供します。 サービス プリンシパルに Azure のリソースへのアクセス権を付与し、スクリプトまたはコマンドライン クライアントがサインインおよびリソースにアクセスするための ID として使用できます。 従来では、独自の ID でセキュリティで保護されたリソースにアクセスするには、スクリプト クライアントは以下を実行する必要がありました。  

   - Azure AD で機密/Web クライアント アプリケーションとして登録し合意されている
   - (多くの場合、スクリプトに埋め込まれている) アプリの資格情報を使用して、そのサービス プリンシパルを使ってサインインする。

MSI を使用すると、スクリプト クライアントはこのいずれも実行する必要がなくなり、MSI のサービス プリンシパルを使ってサインインできます。 

## <a name="azure-cli"></a>Azure CLI

次のスクリプトは、以下を行う方法を示しています。

1. ユーザーが割り当てられた MSI サービス プリンシパルを使って Azure AD にサインインします。  
2. Azure Resource Manager を呼び出し、VM の Azure リージョンの場所を取得します。 CLI がトークンの取得や使用を自動的に管理します。 `<VM NAME>` は VM 名に、`<MSI ID>` はユーザーが割り当てられた MSI リソース ID に必ず置き換えてください。 MSI リソース ID は、ユーザーが割り当てられた MSI の作成時に、`id` プロパティに返されます (`az identity create` コマンドの例については、「[Azure CLI を使用して、VM のユーザーによって割り当てられたマネージ サービスの ID (MSI) を構成する](msi-qs-configure-cli-windows-vm.md)」を参照してください。)。

    ```azurecli
    az login --msi –u <MSI ID>
   
    vmLocation=$(az resource list -n <VM NAME> --query [*].location --out tsv)
    echo The VM region location is $vmLocation
    ```

    応答の例:
   
    ```bash
    user@vmLinux:~$ az login --msi -u /subscriptions/80c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName
    [
      {
        "environmentName": "AzureCloud",
        "id": "90c696ff-5efa-4909-a64d-z1b616f423bl",
        "isDefault": true,
        "name": "MSIResource-/subscriptions/90c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName@50342",
        "state": "Enabled",
        "tenantId": "933a8f0e-ec41-4e69-8ad8-971zc4b533ll",
        "user": {
          "name": "userAssignedIdentity",
          "type": "servicePrincipal"
        }
      }
    ]  
    user@vmLinux:~$ vmLocation=$(az resource list -n vmLinux --query [*].location --out tsv)
    user@vmLinux:~$ echo The VM region location is $vmLocation
    The VM region location is westcentralus
    ```

## <a name="resource-ids-for-azure-services"></a>Azure サービスのリソース ID

Azure AD をサポートし、MSI でテスト済みのリソースの一覧と、そのリソース ID については、「[Azure AD 認証をサポートしている Azure サービス](msi-overview.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。

## <a name="error-handling-guidance"></a>エラー処理に関するガイダンス 

次の応答は、MSI が正しく構成されていない可能性があることを示しています。

- CLI: *MSI: 'http://localhost:50342/oauth2/token' からトークンを取得できませんでした。'HTTPConnectionPool (host='localhost', port=50342) エラーが発生しました* 

これらのエラーが発生した場合、[Azure ポータル](https://portal.azure.com)で Azure VM に戻り、

- **[構成]** ページに移動して、[管理対象サービス ID] が [はい] に設定されていることを確認してください。
- **[拡張]** ページに移動して、MSI 拡張機能が正常に展開されていることを確認します。

いずれかが正しくない場合は、リソースに MSI を再割り当てするか、デプロイ エラーのトラブルシューティングを行う必要があります。 VM の構成でサポートが必要な場合、「[Azure CLI を使用して、VM 管理対象サービス ID (MSI) を構成する](msi-qs-configure-cli-windows-vm.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- Azure VM で MSI を有効にするには、「[Azure CLI を使用して、VM 管理対象サービス ID (MSI) を構成する](msi-qs-configure-cli-windows-vm.md)」を参照してください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。








