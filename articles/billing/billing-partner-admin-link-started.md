---
title: Azure アカウントをパートナー ID にリンクする | Microsoft Docs
description: パートナー ID を顧客のリソースを管理するために使用するユーザー アカウントにリンクすることで、Azure の顧客とのエンゲージメントを追跡します。
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f729a0228c6b0c2f514ab7170299b0271ed28ad2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="link-partner-id-to-your-azure-accounts"></a>貴社のパートナー ID を Azure アカウントにリンクする 
パートナーとして、貴社のパートナー ID を顧客のリソースを管理するために使用されるアカウントにリンクすることで、顧客エンゲージメントに対する貴社の影響を追跡できます。

この機能は、パブリック プレビューで使用できます。 

## <a name="get-access-from-your-customer"></a>顧客からアクセス権を取得する 
パートナー ID をリンクする前に、次のオプションのいずれかを使用することで、顧客の Azure リソースに対するアクセス権を顧客から与えてもらう必要があります。

- **ゲスト ユーザー:** 顧客は、貴社をゲスト ユーザーとして追加し、RBAC ロールを割り当てることができます。 詳細については、[別のディレクトリからのゲスト ユーザーの追加](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)に関する記事を参照してください。

- **ディレクトリ アカウント:** 貴社の顧客は、貴社の組織から顧客のディレクトリ内に新しいユーザーを作成し、RBAC ロールを割り当てることができます。 

- **サービス プリンシパル:** 貴社の顧客は、貴社の組織から顧客のディレクトリ内にアプリまたはスクリプトを追加し、RBAC ロールを割り当てることができます。 アプリまたはスクリプトの ID は、サービス プリンシパルと呼ばれます。

## <a name="link-partner-id"></a>パートナー ID をリンクする
顧客のリソースにアクセスできるようになったら、PowerShell または CLI を使用して、貴社の Microsoft Partner Network ID (MPN ID) を貴社のユーザー ID またはサービス プリンシパルにリンクします。 顧客のテナントのそれぞれに対してパートナー ID をリンクする必要があります。 

### <a name="use-powershell-to-link-new-partner-id"></a>PowerShell を使用して新しいパートナー ID をリンクする

1. [AzurePartnerRP](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner/0.1.0-preview) PowerShell モジュールをインストールします。

2. ユーザー アカウントまたはサービス プリンシパルのいずれかで顧客のテナントにサインインします。詳細については、「[Azure PowerShell でのログイン](https://docs.microsoft.com/en-us/powershell/azure/authenticate-azureps?view=azurermps-5.2.0)」を参照してください。
 
   ```azurepowershell-interactive
    C:\> Login-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. 新しいパートナー ID をリンクします。 パートナー ID は、貴社の組織の [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) ID です。

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>リンク済みのパートナー ID を取得する
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>リンク済みのパートナー ID を更新する
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>リンク済みのパートナー ID を削除する
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>CLI を使用して新しいパートナー ID をリンクする
1.  CLI 拡張機能をインストールします。

    ```azure-cli
    C:\ az extension add --name managementpartner
    ``` 

2.  ユーザー アカウントまたはサービス プリンシパルで顧客のテナントにサインインします。 詳しくは、「[Azure CLI 2.0 を使用してログインする](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)」をご覧ください。

    ```azure-cli
    C:\ az login --tenant <tenant>
    ``` 


3.  新しいパートナー ID をリンクします。 パートナー ID は、貴社の組織の [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) ID です。

     ```azure-cli
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>リンク済みのパートナー ID を取得する
```azure-cli
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>リンク済みのパートナー ID を更新する
```azure-cli
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>リンク済みのパートナー ID を削除する
```azure-cli
C:\ az managementpartner delete --partner-id 12345
``` 


## <a name="frequently-asked-questions"></a>よく寄せられる質問

**誰がパートナー ID をリンクできますか。**

顧客のリソースを管理しているパートナー組織の任意のユーザーが、パートナー ID をアカウントにリンクできます。
  

**パートナー ID をリンクした後でそれを変更できますか。**

はい、リンク済みのパートナー ID は、変更、追加、または削除できます。

**ユーザーのアカウントが複数の顧客のテナントに存在する場合はどうなりますか。**

パートナー ID とアカウント間のリンクは、各顧客テナントに対して行われます。  顧客のテナントのそれぞれでパートナー ID をリンクする必要があります。

**他のパートナーまたは顧客がパートナー ID へのリンクを編集または削除できますか。**

リンクはアカウント レベルで関連付けられます。 パートナー ID へのリンクは、貴社のみが編集または削除できます。 顧客と他のパートナーは、パートナー ID へのリンクを変更できません。 


