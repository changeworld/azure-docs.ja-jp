---
title: "Azure Active Directory Domain Services: パスワード同期を有効にする | Microsoft Docs"
description: "Azure Active Directory ドメイン サービスの概要"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 6ed35ca1b83f5f7221824d99817800c1e42e68c1
ms.lasthandoff: 04/12/2017


---
# <a name="enable-password-synchronization-with-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services とのパスワード同期を有効にする
前のタスクでは、Azure Active Directory (Azure AD) テナントに対して Azure Active Directory Domain Services (AD DS) を有効にしました。 次のタスクでは、Azure Active Directory Domain Services と同期するために、NT LAN Manager (NTLM) および Kerberos 認証に必要な資格情報ハッシュを有効にします。 資格情報の同期が設定されると、ユーザーは自社の資格情報を使用して、管理対象ドメインにサインインできます。

タスクの手順は、組織で所有する Azure AD テナントが、クラウド専用のテナントか、それとも Azure AD Connect を使用してオンプレミスのディレクトリと同期するように設定されているテナントかによって異なります。

> [!div class="op_single_selector"]
> * [クラウド専用 Azure AD テナント](active-directory-ds-getting-started-password-sync.md)
> * [同期された Azure AD テナント](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

## <a name="task-5-enable-password-synchronization-with-azure-active-directory-domain-services-for-a-cloud-only-azure-ad-tenant"></a>タスク 5: Azure Active Directory Domain Services とのパスワード同期を有効にする (クラウド専用 Azure AD テナントの場合)
Azure Active Directory Domain Services では、管理対象ドメインでユーザーを認証するために、NTLM および Kerberos 認証に適した形式の資格情報ハッシュが必要です。 テナントに対して Azure Active Directory Domain Services を有効にしない限り、Azure AD で NTLM または Kerberos 認証に必要な形式の資格情報ハッシュが生成または保存されることはありません。 明らかなセキュリティ上の理由から、Azure AD は資格情報をクリア テキスト形式でも保存しません。 そのため、Azure AD には、これらの NTLM または Kerberos 資格情報ハッシュをユーザーの既存の資格情報に基づいて生成する方法がありません。

> [!NOTE]
> 組織でクラウド専用 Azure AD テナントを所有している場合、Azure Active Directory Domain Services を使用する必要があるユーザーは、自分のパスワードを変更しなければなりません。
>
>

このパスワード変更プロセスにより、Azure Active Directory Domain Services での Kerberos および NTLM 認証に必要な資格情報ハッシュが Azure AD 内に生成されます。 パスワードの変更を促す方法として、Azure Active Directory Domain Services を使用する必要があるテナント内の全ユーザーのパスワードを有効期限切れにすることも、それらのユーザーにパスワードを変更するように指示することもできます。

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>NTLM と Kerberos の資格情報ハッシュの生成を有効にする (クラウド専用 Azure AD テナント)
ユーザーがパスワードを変更できるように、以下の手順をユーザーに伝える必要があります。

1. 組織の [Azure AD アクセス パネル](http://myapps.microsoft.com)のページに移動します。
2. アクセス パネル ウィンドウで、**[プロファイル]** タブを選択します。
3. **[パスワードの変更]** タイルをクリックします。

    ![Azure AD アクセス パネルの [パスワードの変更] タイル](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > アクセス パネル ウィンドウに **[パスワードの変更]** オプションが表示されない場合は、組織で [Azure AD でのパスワード管理](../active-directory/active-directory-passwords-getting-started.md)を構成済みであることを確認します。
   >
   >
4. **[パスワードの変更]** ページで既存の (古い) パスワードを入力し、新しいパスワードを入力して、それを確認します。 

    ![Azure AD Domain Services 用の仮想ネットワークを作成します。](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. **[送信]**をクリックします。

パスワードを変更すると、数分後に Azure Active Directory Domain Services で新しいパスワードを使用できるようになります。 また、さらに数分 (通常は約 20 分) 後には、新しく変更したパスワードを使用して、管理対象ドメインに参加しているコンピューターにサインインできるようになります。

## <a name="next-steps"></a>次のステップ
* [自分のパスワードを更新する方法](../active-directory/active-directory-passwords-update-your-own-password.md#reset-your-password)
* [Azure AD でのパスワード管理の概要](../active-directory/active-directory-passwords-getting-started.md)
* [Azure Active Directory Domain Services とのパスワード同期を有効にする (同期された Azure AD テナントの場合)](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Azure Active Directory Domain Services で管理されているドメインを管理する](active-directory-ds-admin-guide-administer-domain.md)
* [Azure Active Directory Domain Services で管理されているドメインに Windows 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)
* [Azure Active Directory Domain Services で管理されているドメインに Red Hat Enterprise Linux 仮想マシンを参加させる](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

