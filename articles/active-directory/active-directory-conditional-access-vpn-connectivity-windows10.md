---
title: "VPN 接続用の Azure Active Directory の条件付きアクセス(プレビュー) | Microsoft Docs"
description: "VPN 接続用の Azure Active Directory の条件付きアクセスについて説明します。 "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: e9dadb3291ee760e7b05caedfa6b4128be77aa7d
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-conditional-access-for-vpn-connectivity-preview"></a>VPN 接続用の Azure Active Directory の条件付きアクセス(プレビュー)

[Azure Active Directory (Azure AD) の条件付きアクセス](active-directory-conditional-access-azure-portal.md)によって、許可されたユーザーがリソースにアクセスする方法を微調整できます。 仮想プライベート ネットワーク (VPN) 接続用の Azure AD 条件付きアクセスは、VPN 接続の保護に役立ちます。


VPN 接続用の条件付きアクセスを構成するには、次の手順を完了する必要があります。 

1.  VPN サーバーを構成する。
2.  VPN クライアントを構成する。
3.  条件付きアクセス ポリシーを構成する。


## <a name="before-you-begin"></a>開始する前に

このトピックは、次のトピックを熟知していることを前提としています。

- [Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)
- [VPN と条件付きアクセス](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

Microsoft がこの機能をどのように実装しているかの詳細を知るには、「[Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)」(Windows 10 での自動 VPN プロファイルを使用したリモート アクセスの強化) をご覧ください。   


## <a name="prerequisites"></a>前提条件

VPN 接続用の Azure Active Directory 条件付きアクセスを構成するには、VPN サーバーを構成する必要があります。 



## <a name="step-1-configure-your-vpn-server"></a>手順 1: VPN サーバーを構成する 

この手順では、Azure AD での VPN 認証用のルート証明書を構成します。 VPN 接続用の条件付きアクセスを構成するには、以下の操作を行う必要があります。

1. Azure Portal で VPN 証明書を作成する。
2. VPN 証明書をダウンロードする。
2. 証明書を VPN サーバーにデプロイする。

Azure AD では、VPN 接続用に Azure AD で認証するときに、VPN 証明書を使用して Windows 10 クライアントに対して発行された証明書に署名します。 Windows 10 クライアントが要求するトークンは、Windows 10 クライアントがアプリケーション (この場合は VPN サーバー) に提示する証明書です。

![条件付きアクセスの証明書をダウンロード](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

Azure Portal で、2 つの証明書を作成して、一方の証明書の有効期限が近づいているときの移行を管理できます。 証明書を作成するときに、プライマリ証明書にするかどうかを選択できます。プライマリ証明書は、認証時に、接続用の証明書の署名に使用されます。

VPN 証明書を作成するには:

1. [Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインします。

2. 左側のメニューで、**[Azure Active Directory]** をクリックします。 

    ![[Azure Active Directory] を選択します。](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. **[Azure Active Directory]** ページの **[管理]** セクションで、**[条件付きアクセス]** をクリックします。

    ![条件付きアクセスを選択](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. **[条件付きアクセス]** ページの **[管理]** セクションで、**[VPN 接続 (プレビュー)]** をクリックします。

    ![VPN 接続を選択](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. **[VPN 接続]** ページで、**[新しい証明書]** をクリックします。

    ![新しい証明書を選択](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. **[新規]** ページで、次の手順を実行します。

    ![時間とプライマリを選択](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. **[時間の選択]** で **[1 年]** を選択します。

    b. **[プライマリ]** で **[はい]** を選択します。

    c. **Create** をクリックしてください。

7. [VPN 接続] ページで、**[証明書のダウンロード]** をクリックします。


これで、新しく作成した証明書を VPN サーバーにデプロイする準備が整いました。 VPN サーバーで、ダウンロードした証明書を *VPN 認証用の信頼されたルート CA* として追加します。

Windows の RRAS ベースのデプロイでは、次のコマンドを実行して、NPS サーバーで *Enterprise NTauth* ストアにルート証明書を追加します。

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2-configure-your-vpn-client"></a>手順 2: VPN クライアントを構成する 

この手順では、「[VPN と条件付きアクセス](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)」の説明に従って、VPN クライアントの接続プロファイルを構成します。


## <a name="step-3-configure-your-conditional-access-policy"></a>手順 3: 条件付きアクセス ポリシーを構成する

このセクションでは、VPN 接続用の条件付きアクセス ポリシーを構成するための手順を説明します。


1. **[条件付きアクセス]** ページで、ツール バーの上部の **[追加]** をクリックします。

    ![[条件付きアクセス] ページで [追加] を選択](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. **[新規]** ページの **[名前]** ボックスにポリシーの名前を入力します。 たとえば、「**VPN policy**」と入力します。

    ![[条件付きアクセス] ページでポリシーの名前を追加](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. **[割り当て]** セクションで **[ユーザーとグループ]** をクリックします。

    ![ユーザーとグループを選択](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. **[ユーザーとグループ]** ページで、次の手順を実行します。

    ![テスト ユーザーを選択](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. **[ユーザーとグループの選択]** をクリックします。

    b. **[選択]**をクリックします。

    c. **[選択]** ページで、テスト ユーザーを選択し、**[選択]** をクリックします。

    d. **[ユーザーとグループ]** ページで、**[完了]** をクリックします。

7. **[新規]** ページで、次の手順を実行します。

    ![クラウド アプリを選択](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. **[割り当て]** セクションで、**[クラウド アプリ]** をクリックします。

    b. **[クラウド アプリ]** ページで、**[アプリを選択]** をクリックし、**[選択]** をクリックします。

    c. **[選択]** ページで、**[アプリケーション]** ボックスに「**vpn**」と入力します。

    d. **[VPN サーバー]**を選択します。

    e. **[選択]**をクリックします。


13. **[新規]** ページで、**[許可]** ページを開くために、**[コントロール]** セクションで **[許可]** をクリックします。

    ![[許可] を選択](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. **[許可]** ページで、次の手順を実行します。

    ![[多要素認証が必要です] を選択](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. **[多要素認証が必要です]** を選択します。

    b. **[選択]**をクリックします。

15. **[新規]** ページで、**[ポリシーの有効化]** の下の **[オン]** をクリックします。

    ![ポリシーを有効にする](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. **[新規]** ページで **[作成]** をクリックします。



## <a name="next-steps"></a>次のステップ

Microsoft がこの機能をどのように実装しているかの詳細を知るには、「[Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)」(Windows 10 での自動 VPN プロファイルを使用したリモート アクセスの強化) をご覧ください。    

