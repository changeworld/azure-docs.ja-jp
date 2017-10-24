---
title: "Azure Active Directory Domain Services: 概要 | Microsoft Docs"
description: "Azure Portal を使用して Azure Active Directory Domain Services を有効にする"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: d86b9e99619dea408e784f32d76ba6296948bdc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal を使用して Azure Active Directory Domain Services を有効にする
この記事では、Azure Portal を使用して、Azure Active Directory Domain Services (Azure AD DS) を有効にする方法について説明します。

**[Azure AD Domain Services の有効化]** ウィザードを起動するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 左側のウィンドウで、**[新規]** をクリックします。
3. **[新規]** ページで、検索バーに「**Domain Services**」と入力します。

    ![ドメイン サービスの検索](./media/getting-started/search-domain-services.png)

4. 検索候補の一覧から **[Azure AD Domain Services]** をクリックして選択します。 **[Azure AD Domain Services]** ページで、**[作成]** をクリックします。

    ![ドメイン サービスの表示](./media/getting-started/domain-services-blade.png)

5. **[Azure AD Domain Services の有効化]** ウィザードが起動します。


## <a name="task-1-configure-basic-settings"></a>タスク 1: 基本設定を構成する
ウィザードの **[基本]** ページで、管理対象ドメインの DNS ドメイン名を指定できます。 管理対象ドメインのデプロイ先となるリソース グループと Azure の場所も選択することができます。

![基本を構成する](./media/getting-started/domain-services-blade-basics.png)

1. 管理対象ドメインの **DNS ドメイン名**を選択します。

   * ディレクトリの既定のドメイン名 (**.onmicrosoft.com** のサフィックスがあるもの) が既定で指定されます。

   * カスタム ドメイン名を入力することもできます。 この例でのカスタム ドメイン名は、*contoso100.com* です。

     > [!WARNING]
     > 指定するドメイン名のプレフィックス (たとえば、ドメイン名 *contoso100.com* の *contoso100* など) は、15 文字以内に収める必要があります。 プレフィックスが 15 文字より長いと、管理対象ドメインを作成することはできません。
     >
     >

2. 管理対象ドメイン用に選択した DNS ドメイン名がまだ仮想ネットワークに存在しないことを確認します。 具体的には、以下のことを確認します。

   * 同じ DNS ドメイン名のドメインが仮想ネットワーク上に既にあるかどうか。

   * 管理対象ドメインを有効にする仮想ネットワークに、オンプレミス ネットワークとの VPN 接続があるかどうか。 このシナリオでは、オンプレミス ネットワークに同じ DNS ドメイン名のドメインがないことを確認します。

   * 仮想ネットワーク上に、その名前の付いたクラウド サービスが既にあるかどうか。

3. **仮想ネットワークの種類**を選びます。 既定では、**Resource Manager** 仮想ネットワークの種類が選択されています。 新しく作成されるすべての管理対象ドメインには、この種類の仮想ネットワークを使うことをお勧めします。

    > [!TIP]
    > **クラシック仮想ネットワークのサポートは廃止される予定です。** 新たにデプロイする場合は、Resource Manager 仮想ネットワークの種類を選択します。 新たなデプロイへのクラシック仮想ネットワークのサポートは、間もなく廃止されます。 クラシック仮想ネットワークに展開されている既存の管理対象ドメインは、引き続きサポートされます。
    >

4. 管理対象ドメインを作成する Azure **サブスクリプション**を選択します。

5. 管理対象ドメインが属する**リソース グループ**を選択します。 **[新規作成]** または **[既存のものを使用]** オプションを選択して、リソース グループを選択できます。

6. 管理対象ドメインを作成する Azure の**場所**を選択します。 ウィザードの **[ネットワーク]** ページに、選択した場所に属する仮想ネットワークのみが表示されます。

7. 完了したら、**[OK]** をクリックして、ウィザードの **[ネットワーク]** ページに移動します。


## <a name="next-step"></a>次のステップ
[タスク 2: ネットワーク設定を構成する](active-directory-ds-getting-started-network.md)
