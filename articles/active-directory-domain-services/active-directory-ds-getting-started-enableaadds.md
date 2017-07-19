---
title: "Azure Active Directory Domain Services: Azure Active Directory Domain Services を有効にする | Microsoft Docs"
description: "Azure クラシック ポータルを使って Azure Active Directory Domain Services を有効にする"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c659da59-f4b5-4edd-b702-1727a8ccb36f
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: ed72325ca9db99405c6173eb882a92f80cd77f47
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017


---
<a id="enable-azure-active-directory-domain-services-using-the-azure-classic-portal" class="xliff"></a>

# Azure クラシック ポータルを使って Azure Active Directory Domain Services を有効にする

<a id="task-3-enable-azure-active-directory-domain-services" class="xliff"></a>

## タスク 3: Azure Active Directory Domain Services を有効にする
このタスクでは、以下の手順に従い、ディレクトリに対して Azure Active Directory Domain Services (Azure AD DS) を有効にします。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)に移動します。
2. 左側のウィンドウで、**[Active Directory]** を選択します。
3. Azure AD DS を有効にする Azure Active Directory (Azure AD) テナント (ディレクトリ) を選択します。

    ![Select Azure AD Directory](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. **[preview directory (ディレクトリのプレビュー)]** ページで、**[構成]** タブをクリックします。

    ![ディレクトリの [構成] タブ](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. **[ドメイン サービス]** で、**[このディレクトリのドメイン サービスを有効にします]** オプションを **[はい]** に変更します。  
    ページ上に追加の Azure Active Directory Domain Services 構成オプションが表示されます。

    ![Domain Services の有効化](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

   > [!NOTE]
   > テナントに対して Azure Active Directory Domain Services を有効にすると、ユーザーを認証するために必要な Kerberos と NTLM の資格情報のハッシュが Azure AD によって生成され、保存されます。
   >
   >
6. **[ドメイン サービスの DNS ドメイン名]**を指定します。

   * ディレクトリの既定のドメイン名 (**.onmicrosoft.com** のサフィックスがあるもの) が既定で選択されます。

   * 一覧には、Azure AD ディレクトリに対して構成されたすべてのドメインが含まれます。つまり、**[ドメイン]** タブで構成するドメインのうち、確認済みのドメインと未確認のドメインが、両方ともこの一覧に含まれます。

   * カスタム ドメイン名を入力することもできます。 この例でのカスタム ドメイン名は、*contoso100.com* です。

     > [!WARNING]
     > 指定するドメイン名のプレフィックス (たとえば、ドメイン名 *contoso100.com* の *contoso100* など) は、15 文字以内に収める必要があります。 プレフィックスが 15 文字を超える Azure Active Directory Domain Services ドメインは作成できません。
     >
     >
7. 管理対象ドメイン用に選択した DNS ドメイン名がまだ仮想ネットワークに存在しないことを確認します。 特に、以下の点を確認してください。

   * 同じ DNS ドメイン名のドメインが仮想ネットワーク上に既にあるかどうか。

   * 選択した仮想ネットワークにオンプレミス ネットワークとの VPN 接続があり、オンプレミス ネットワーク上に同じ DNS ドメイン名のドメインがあるかどうか。

   * 仮想ネットワーク上に、その名前の付いたクラウド サービスが既にあるかどうか。
8. Azure Active Directory Domain Services を利用できるようにする仮想ネットワークを選択します。 作成した仮想ネットワークと専用サブネットを、**[ドメイン サービスをこの仮想ネットワークに接続します]** ボックスの一覧から選択します。 また、以下の点についても検討してください。

   * 指定した仮想ネットワークが Azure Active Directory Domain Services でサポートされている Azure リージョンに属していることを確認します。 Azure Active Directory Domain Services を利用できる Azure リージョンを確認するには、[リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services/)に関するページを参照してください。

   * Azure Active Directory Domain Services がサポートされていないリージョンに属している仮想ネットワークはドロップダウン リストに表示されません。

   * Azure Active Directory Domain Services には仮想ネットワーク内の専用サブネットを使用します。 ゲートウェイ サブネットは選択 "*しない*" でください。 [ネットワークに関する考慮事項](active-directory-ds-networking.md)を参照してください。

   * 同様に、Azure Resource Manager を使用して作成された仮想ネットワークは、ドロップダウン リストに表示されません。 現在、Resource Manager ベースの仮想ネットワークは Azure Active Directory Domain Services でサポートされていません。
9. Azure Active Directory Domain Services を有効にするには、ページ下部の作業ウィンドウで **[保存]** をクリックします。
    * ディレクトリで Azure Active Directory Domain Services が有効になっている間、ページには *[保留中]* という状態が表示されます。

        ![[Domain Services を有効にします] ウィンドウ](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

        > [!NOTE]
        > Azure Active Directory Domain Services により、管理対象ドメインに高可用性が提供されます。 Azure Active Directory Domain Services を有効にすると、仮想ネットワーク上でドメイン サービスを利用できる IP アドレスが 1 つずつ表示されます。 2 つ目の IP アドレスは、1 つ目のすぐ後に、サービスによってドメインの高可用性が有効になると表示されます。 ドメインに対する高可用性が構成され、アクティブになると、**[構成]** タブの **[domain services]** セクションに 2 つの IP アドレスが表示されます。
        >
        >
    * 約 20 ～ 30 分後に、仮想ネットワーク上でドメイン サービスを利用できる 1 つ目の IP アドレスが、**[構成]** ページの **[IP アドレス]** フィールドに表示されます。

        ![1 つ目のプロビジョニング済み IP が表示されている Domain Services ウィンドウ](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
    * ドメインで高可用性が利用できるようになると、2 つの IP アドレスがページに表示されます。 管理対象ドメインには、選択した仮想ネットワーク上の、この 2 つの IP アドレスでアクセスできます。

10. 仮想ネットワークの DNS 設定を更新できるように、2 つの IP アドレスをメモしておきます。 DNS 設定を更新することで、ドメインへの参加などの操作のために、仮想ネットワーク上の仮想マシンからドメインに接続できるようになります。

    ![両方のプロビジョニング済み IP が表示されている Domain Services ウィンドウ](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> Azure AD テナントのサイズ (ユーザー数やグループ数など) によっては、管理対象ドメインへの同期に時間がかかる場合があります。 この同期処理は、バック グラウンドで発生します。 数万のオブジェクトがある大きなテナントの場合、すべてのユーザー、グループ メンバーシップ、資格情報が同期されるまでに 1 ～ 2 日かかることがあります。
>
>

<a id="next-step" class="xliff"></a>

## 次のステップ
[タスク 4: Azure 仮想ネットワークの DNS 設定を更新する](active-directory-ds-getting-started-update-dns.md)

