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
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: abb27292d4b5533fe6f3d66d6921fea8c82f18dd
ms.lasthandoff: 04/12/2017


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
> 仮想ネットワークの DNS サーバー設定を更新した後、ネットワーク上の仮想マシンによって更新済みの DNS 構成が取得されるまでには、しばらく時間がかかる場合があります。 仮想マシンからドメインに接続できない場合は、仮想マシン上の DNS キャッシュをフラッシュ ("ipconfig /flushdns") します。 このコマンドにより、仮想マシンの DNS 設定が強制的に更新されます。
>
>

## <a name="next-steps"></a>次のステップ
タスク 5: [Azure Active Directory Domain Services とのパスワード同期を有効にする](active-directory-ds-getting-started-password-sync.md)

