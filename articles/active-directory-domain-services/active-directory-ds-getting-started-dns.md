---
title: "Azure Active Directory Domain Services: Azure 仮想ネットワークの DNS 設定を更新する | Microsoft Docs"
description: "Azure Active Directory ドメイン サービスの概要"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: dacd689576dce65bbf1a975409ea7d7f2c3ada90
ms.contentlocale: ja-jp
ms.lasthandoff: 06/14/2017


---
# <a name="update-dns-settings-for-the-azure-virtual-network"></a>Azure 仮想ネットワークの DNS 設定を更新する
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>タスク 4: Azure 仮想ネットワークの DNS 設定を更新する
前の構成タスクでは、ディレクトリに対して Azure Active Directory Domain Services を有効にしました。 次のタスクでは、仮想ネットワーク内のコンピューターがこれらのサービスに接続してそれを使用できるようにします。 この記事では、仮想ネットワーク上で Azure Active Directory Domain Services を利用できる 2 つの IP アドレスを指すように、仮想ネットワークの DNS サーバー設定を更新します。

> [!NOTE]
> ディレクトリに対して Azure Active Directory Domain Services を有効にしたら、ディレクトリの **[構成]** タブに表示される Azure Active Directory Domain Services の IP アドレスをメモしておきます。
>
>

Azure Active Directory Domain Services を有効にした仮想ネットワークの DNS サーバー設定を更新するには、次の手順に従います。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)に移動します。
2. 左側のウィンドウで、**[ネットワーク]** を選択します。  
    **[ネットワーク]** ウィンドウが開きます。

    ![仮想ネットワーク ウィンドウ](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. **[Virtual Networks]** タブで、Azure Active Directory Domain Services を有効にした仮想ネットワークを選択してプロパティを表示します。
4. [ **構成** ] タブをクリックします。

    ![仮想ネットワーク ウィンドウ](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. **[DNS サーバー]** セクションで、ディレクトリの **[構成]** タブの **[ドメイン サービス]** セクションに表示されていた 2 つの IP アドレスを両方とも入力します。
6. この仮想ネットワークの DNS サーバー設定を保存するには、ウィンドウ下部にある作業ウィンドウで **[保存]** をクリックします。

   ![仮想ネットワークの DNS サーバー設定を更新する](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> ネットワーク内の仮想マシンは、再起動時に新しい DNS 設定のみを取得します。 すぐに更新後の DNS 設定を取得する必要がある場合は、ポータル、PowerShell、または CLI のいずれかの再起動をトリガーします。
>
>

## <a name="next-steps"></a>次のステップ
タスク 5: [Azure Active Directory Domain Services とのパスワード同期を有効にする](active-directory-ds-getting-started-password-sync.md)

