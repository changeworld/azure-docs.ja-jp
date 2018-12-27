---
title: 新しい Azure Stack テナント アカウントを Azure Active Directory に追加する | Microsoft Docs
description: Microsoft Azure Stack Development Kit のデプロイを行った後、少なくとも 1 つのテナント ユーザー アカウントを作成して、テナント ポータルを表示できるようにする必要があります。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: fa8c8da34a687edd1bd92c1d516183ee5d3e1bd0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240122"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>新しい Azure Stack テナント アカウントをAzure Active Directory に追加する

[Azure Stack Development Kit をデプロイ](azure-stack-run-powershell-script.md)した後は、テナント ポータルを操作し、オファーとプランをテストするためにテナント ユーザー アカウントが必要になります。 テナント アカウントを作成するには、[Azure Portal](#create-an-azure-stack-tenant-account-using-the-azure-portal) または [PowerShell](#create-an-azure-stack-tenant-account-using-powershell) を使用します。

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Azure ポータルを使用して Azure Stack テナント アカウントを作成する

Azure ポータルを使用するには Azure サブスクリプションが必要です。

1. [Azure](https://portal.azure.com) にサインインします。
2. 左側のナビゲーション バーで **Active Directory** を選択し、Azure Stack で使用するディレクトリに切り替えるか、新しいディレクトリを作成します。
3. **[Azure Active Directory]** > **[ユーザー]** > **[新しいユーザー]** を選択します。

    ![[ユーザー] - [すべてのユーザー] ページ ([新しいユーザー] が強調表示されている)](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. **[ユーザー]** ページで、必要な情報を入力します。

    ![新しいユーザーの追加 (ユーザー情報が入力された [ユーザー] ページ)](media/azure-stack-add-new-user-aad/new-user-user.png)

    - **[名前] (必須)。** 新しいユーザーの氏名です。 たとえば、Mary Parker などです。
    - **[ユーザー名] (必須)。** 新しいユーザーのユーザー名です。 たとえば、「 mary@contoso.com 」のように入力します。
        ユーザー名のドメイン部分には、既定の初期ドメイン名の <_yourdomainname_>.onmicrosoft.com、またはカスタム ドメイン名 (contoso.com など) のいずれかを使用する必要があります。 カスタム ドメイン名の作成方法の詳細については、[Azure Active Directory にカスタム ドメイン名を追加する方法](../active-directory/fundamentals/add-custom-domain.md)に関するページを参照してください。
    - **[プロファイル]。** オプションで、ユーザーに関する詳細情報を追加することができます。 後でユーザー情報を追加することもできます。 ユーザー情報の追加方法については、「[ユーザー プロファイル情報の追加または変更方法](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)」を参照してください。
    - **ディレクトリ ロール。**  **[ユーザー]** を選択します。

5. **[パスワードの表示]** チェックボックスを選択し、**[パスワード]** ボックスに表示される自動生成されたパスワードをコピーします。 このパスワードは、最初のサインイン プロセスで必要になります。

6. **作成**を選択します。

    ユーザーが作成され、Azure AD テナントに追加されます。

7. 新しいアカウントで Microsoft Azure portal にサインインします。 パスワードの変更を求められたら、変更します。
8. `https://portal.local.azurestack.external` に新しいアカウントでサインインして、テナント ポータルを表示します。

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>PowerShell を使用して Azure Stack テナント アカウントを作成する

Azure サブスクリプションがない場合は、Azure Portal を使用してテナント ユーザー アカウントを追加できません。 その場合は、代わりに Windows PowerShell 用 Azure Active Directory モジュールを利用できます。

> [!NOTE]
> Microsoft アカウント (Live ID) を使用して Azure Stack Development Kit をデプロイしている場合は、AAD PowerShell を使用してテナント アカウントを作成できません。 
> 
> 

1. [IT プロフェッショナル 用 Microsoft Online Services サインイン アシスタント RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950) をインストールします。
2. [Windows PowerShell 用 Azure Active Directory Module (64 ビット版)](https://go.microsoft.com/fwlink/p/?linkid=236297) をインストールして開きます。
3. 次のコマンドレットを実行します。

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. 新しいアカウントで Microsoft Azure にサインインします。 パスワードの変更を求められたら、変更します。
2. `https://portal.local.azurestack.external` に新しいアカウントでサインインして、テナント ポータルを表示します。

