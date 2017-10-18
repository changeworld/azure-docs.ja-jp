---
title: "Azure Active Directory Domain Services: Windows Server VM の管理対象ドメインへの参加 | Microsoft Docs"
description: "Windows Server 仮想マシンの Azure AD ドメイン サービスへの参加"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: maheshu
ms.openlocfilehash: ce50c678247226b629490a2bd8ba2935ed229f06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Windows Server 仮想マシンの管理対象ドメインへの参加
> [!div class="op_single_selector"]
> * [Azure Portal - Windows](active-directory-ds-admin-guide-join-windows-vm-portal.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
>
>

<br>

この記事では、Azure ポータルを使用して Windows Server 仮想マシンをデプロイする方法を示します。 その後、仮想マシンを Azure AD Domain Services の管理対象ドメインに参加させる方法を示します。

## <a name="step-1-create-the-windows-server-virtual-machine"></a>ステップ 1: Windows Server 仮想マシンを作成する
Azure AD Domain Services を有効にした仮想ネットワークに参加する Windows 仮想マシンを作成するには、次の手順を実行します。

1. Azure Portal ([http://portal.azure.com](http://portal.azure.com)) にログインします。
2. Azure Portal の左上にある **[新規]** ボタンをクリックします。
3. **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。

    ![イメージの選択](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. ウィザードの **[基本]** ページで、仮想マシンの基本的な設定を構成します。

    ![VM の基本設定を構成する](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > ここで入力するユーザー名とパスワードは、仮想マシンにログインするために使用するローカルの Administrator アカウントです。 仮想マシンをパスワードのブルート フォース攻撃から保護するために、強力なパスワードを選択してください。 ここではドメイン ユーザー アカウントの資格情報を入力しないでください。
    >

5. 仮想マシンの **[サイズ]** を選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。

    ![VM のサイズを選択する](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. ウィザードの **[設定]** ページで、Azure AD Domain Services の管理対象ドメインがその中にデプロイされる仮想ネットワークを選択します。 管理対象ドメインのデプロイ先とは別のサブネットを選択します。 その他の設定は既定値のままにして、**[OK]** をクリックします。

    ![仮想マシンの仮想ネットワークの選択](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **適切な仮想ネットワークとサブネットを選択してください。**
    > 管理対象ドメインがデプロイされている仮想ネットワークまたは仮想ネットワーク ピアリングを使用して接続されている仮想ネットワークを選択します。 別の仮想ネットワークを選択した場合、仮想ネットワークを管理対象ドメインに参加させることができなくなります。
    > 管理対象ドメインは専用サブネットにデプロイすることをお勧めします。 そのため、管理対象ドメインを有効にしたサブネットは選択しないでください。

7. **[購入]** ページで設定を確認し、**[OK]** をクリックして仮想マシンをデプロイします。
8. VM のデプロイは、Azure ポータルのダッシュボードにピン留めされます。

    ![完了](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
9. デプロイが完了したら、**[概要]** ページで VM に関する情報を確認できます。


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>ステップ 2: ローカル管理者アカウントを使用して Windows Server 仮想マシンに接続する
次に、新たに作成された Windows Server 仮想マシンをドメインに参加させるため、その仮想マシンに接続します。 仮想マシンの作成時に指定したローカル管理者の資格情報を使用します。

仮想マシンに接続するには、次の手順を実行します。

1. **[概要]** ページの **[接続]** ボタンをクリックします。 リモート デスクトップ プロトコル (.rdp) ファイルが作成されてダウンロードされます。

    ![Windows 仮想マシンに接続する](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. VM に接続するには、ダウンロードした RDP ファイルを開きます。 メッセージが表示されたら、**[接続]** をクリックします。
3. ログイン プロンプトで、仮想マシンの作成時に指定した **ローカル管理者の資格情報**を入力します。 たとえば、この例では "localhost\mahesh" を使用しています。
4. サインイン処理中に証明書の警告が表示される場合があります。 [はい] または [続行] をクリックして接続処理を続行します。

この時点で、ローカル管理者の資格情報を使用して、新しく作成した Windows 仮想マシンにログインしている必要があります。 次のステップでは、仮想マシンをドメインに参加させます。


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>ステップ 3: Windows Server 仮想マシンを AAD-DS 管理対象ドメインに参加させる
Windows Server 仮想マシンを AAD DS の管理対象ドメインに参加させるには、次の手順を実行します。

1. 手順 2. で示したように、Windows Server に接続します。 スタート画面で、 **[サーバー マネージャー]**を開きます。
2. [サーバー マネージャー] ウィンドウの左側のウィンドウで **[ローカル サーバー]** をクリックします。

    ![仮想マシンでのサーバー マネージャーの起動](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. **[プロパティ]** セクションで **[ワークグループ]** をクリックします。 **[システムのプロパティ]** のプロパティ ページで、**[変更]** をクリックしてドメインに参加します。

    ![システムのプロパティ ページ](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. **[ドメイン]** テキスト ボックスで、Azure AD ドメイン サービス管理対象ドメインのドメイン名を指定し、**[OK]** をクリックします。

    ![参加するドメインの指定](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. ドメインに参加するために、資格情報を入力するように求められます。 **AAD DC 管理者グループに属しているユーザーの資格情報を指定** します。 このグループのメンバーだけが、マシンを管理対象のドメインに参加させる権限を有します。

    ![ドメインへの参加の資格情報を指定する](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. 次のいずれかの方法で、資格情報を指定できます。

   * **UPN 形式: (推奨)** Azure AD で構成されたユーザー アカウントの UPN サフィックスを指定します。 この例では、ユーザー 'bob' の UPN サフィックスは 'bob@domainservicespreview.onmicrosoft.com' です。
   * **SAMAccountName 形式:** SAMAccountName 形式でアカウント名を指定できます。 この例では、ユーザー 'bob' は、「CONTOSO100\bob」を入力する必要があります。

     > [!TIP]
     > **UPN 形式を使用して資格情報を指定することをお勧めします。**
     > ユーザーの UPN プレフィックスが最大文字数を超えていると (例: "joehasareallylongname")、SAMAccountName が自動生成される場合があります。 複数のユーザーが Azure AD テナントで同じ UPN プレフィックス (例: "bob") を使用していると、サービスにより SAMAccountName 形式が自動生成される場合があります。 このような場合は、UPN 形式を使用するとドメインに確実にログインすることができます。
     >

7. ドメイン参加が成功すると、ドメインへの歓迎を通知する次のメッセージが表示されます。 ドメイン参加操作を完了するには、仮想マシンを再起動します。

    ![ドメインへようこそ](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>ドメイン参加のトラブルシューティング
### <a name="connectivity-issues"></a>接続に関する問題
仮想マシンでドメインを検索できない場合は、次のトラブルシューティング手順を参照してください。

* ドメイン サービスを有効にしているのと同じ仮想ネットワークに仮想マシンが接続されていることを確認します。 そうでない場合、仮想マシンがドメインに接続できず、そのため、ドメインに参加することできません。
* 仮想マシンが仮想ネットワークに存在することを確認します。その仮想マシンは Domain Services を有効にした仮想ネットワークに接続されています。
* 管理対象ドメインのドメイン名を使用して、ドメインの ping を試行してください (例: "ping contoso100.com")。 これを実行できない場合は、Azure AD Domain Services を有効にしたページに表示されているドメインの IP アドレスに対して ping を試行してください (例: "ping 10.0.0.4")。 ドメインではなく IP アドレスを ping できた場合は、DNS が正しく構成されていない場合があります。 ドメインの IP アドレスが仮想ネットワークの DNS サーバーとして構成されているかどうかを確認します。
* 仮想マシンのDNS リゾルバー キャッシュのフラッシュを試行します ('ipconfig/flushdns')。

ドメインに参加するために資格情報の入力を求めるダイアログ ボックスが表示される場合、接続の問題はありません。

### <a name="credentials-related-issues"></a>資格情報に関連した問題
資格情報で問題があり、ドメインに参加することができない場合は、次の手順を参照してください。

* UPN 形式を使用して資格情報を指定します。 複数のユーザーがテナントで同じ UPN プレフィックスを使用していたり、UPN プレフィックスが最大文字数を超えている場合、アカウントの SAMAccountName は自動生成される可能性があります。 そのため、アカウントの SAMAccountName 形式が想定した形式やオンプレミス ドメインで使用する形式と異なる可能性があります。
* "AAD DC 管理者" グループに属するユーザー アカウントの資格情報を使用して、コンピューターを管理対象ドメインに参加させるようにします。
* ファースト ステップ ガイドで説明されている手順に従って [パスワード同期が有効になっている](active-directory-ds-getting-started-password-sync.md) ことを確認します。
* サインインするためには、Azure AD で構成されているユーザーの UPN (例: 'bob@domainservicespreview.onmicrosoft.com') を使用します。
* ファースト ステップ ガイドで指定されているとおり、パスワード同期が完了するまで十分な時間待機します。

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](active-directory-ds-getting-started.md)
* [Azure AD ドメイン サービスで管理されているドメインの管理](active-directory-ds-admin-guide-administer-domain.md)
