---
title: Azure VM の管理対象サービス ID を使用してサインインする方法
description: スクリプト クライアント サインインおよびリソース アクセスに Azure VM の MSI サービス プリンシパルを使用する方法の例と詳しい手順を説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 205938bbf615face0768028717a333c13c1fafa1
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590315"
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Azure VM の管理対象サービス ID (MSI) を使用してサインインする方法 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
この記事は、MSI のサービス プリンシパルを使用してサインインするための PowerShell および CLI スクリプトの例を示し、エラー処理などの重要なトピックに関するガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

この記事に含まれている Azure PowerShell または Azure CLI の例を使用するには、最新バージョンの [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) または [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) をインストールする必要があります。 

> [!IMPORTANT]
> - この記事のすべてのサンプル スクリプトでは、コマンドライン クライアントが、MSI 対応の仮想マシンで実行されていることを前提としています。 お使いの VM にリモート接続するには、Azure ポータルで VM への "接続" 機能を使用します。 VM で MSI を有効にする方法の詳細については、「[Azure Portal を使用して、VM 管理対象サービス ID (MSI) を構成する](qs-configure-portal-windows-vm.md)」、または関連する記事 (PowerShell、CLI、テンプレート、または Azure SDK を使用) のいずれかを参照してください。 
> - リソースへのアクセス中のエラーを回避するには、VM 上の Azure Resource Manager の操作を許可するために、VM の MSI に適切なスコープ (VM 以上) の "読み取り" アクセス以上のアクセス権が付与されている必要があります。 詳細については、「[Azure Portal を使用して、リソースに管理対象サービス ID (MSI) アクセスを割り当てる](howto-assign-access-portal.md)」を参照してください。

## <a name="overview"></a>概要

MSI は、VM 上で [MSI を有効化したときに作成された](overview.md#how-does-it-work)、[サービス プリンシパル オブジェクト](../develop/developer-glossary.md#service-principal-object)を提供します。 サービス プリンシパルに Azure のリソースへのアクセス権を付与し、スクリプトまたはコマンドライン クライアントがサインインおよびリソースにアクセスするための ID として使用できます。 従来では、独自の ID でセキュリティで保護されたリソースにアクセスするには、スクリプト クライアントは以下を実行する必要がありました。  

   - Azure AD で機密/Web クライアント アプリケーションとして登録し合意されている
   - (多くの場合、スクリプトに埋め込まれている) アプリの資格情報を使用して、そのサービス プリンシパルを使ってサインインする。

MSI を使用すると、スクリプト クライアントはこのいずれも実行する必要がなくなり、MSI のサービス プリンシパルを使ってサインインできます。 

## <a name="azure-cli"></a>Azure CLI

次のスクリプトは、以下を行う方法を示しています。

1. VM の MSI サービス プリンシパルを使って Azure AD にサインインする  
2. Azure Resource Manager を呼び出して VM のサービス プリンシパルの ID を取得します。 CLI がトークンの取得や使用を自動的に管理します。 `<VM-NAME>` を必ずお使いの仮想マシン名に置き換えてください。  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

次のスクリプトは、以下を行う方法を示しています。

1. VM の MSI サービス プリンシパルを使って Azure AD にサインインする  
2. VM に関する情報を取得するには、Azure Resource Manager コマンドレットを呼び出します。 PowerShell が自動的にトークンの使用を管理します。  

   ```azurepowershell
   Add-AzureRmAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Azure サービスのリソース ID

Azure AD をサポートし、MSI でテスト済みのリソースの一覧と、そのリソース ID については、「[Azure AD 認証をサポートしている Azure サービス](services-support-msi.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。

## <a name="error-handling-guidance"></a>エラー処理に関するガイダンス 

次のような応答が返ってきた場合は、VM の MSI が正しく構成されていない可能性があります。

- PowerShell: *Invoke-WebrRequest: リモート サーバーに接続できません*
- CLI: *MSI: 'http://localhost:50342/oauth2/token' からトークンを取得できませんでした。'HTTPConnectionPool (host='localhost', port=50342) エラーが発生しました* 

これらのエラーが発生した場合、[Azure ポータル](https://portal.azure.com)で Azure VM に戻り、

- 
  **[構成]** ページに移動して、[管理対象サービス ID] が [はい] に設定されていることを確認してください。
- **[拡張]** ページに移動して、MSI 拡張機能が正常に展開されていることを確認します。

いずれかが正しくない場合は、リソース上で MSI を再デプロイするか、デプロイ エラーのトラブルシューティングを行う必要があります。 VM の構成についてサポートが必要な場合は、「[Azure Portal を使用して、VM 管理対象サービス ID (MSI) を構成する](qs-configure-portal-windows-vm.md)」をご覧ください。

## <a name="related-content"></a>関連コンテンツ

- Azure VM で MSI を有効にするには、「[PowerShell を使用して、VM 管理対象サービス ID (MSI) を構成する](qs-configure-powershell-windows-vm.md)」または「[Azure CLI を使用して、VM 管理対象サービス ID (MSI) を構成する](qs-configure-cli-windows-vm.md)」を参照してください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。








