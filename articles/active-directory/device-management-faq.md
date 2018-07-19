---
title: Azure Active Directory デバイス管理の FAQ | Microsoft Docs
description: Azure Active Directory デバイス管理の FAQ。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 864f790db48d3d4542ed56a4c7272a198df5bd56
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901138"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory デバイス管理の FAQ



**Q: macOS デバイスを登録するにはどうしたらよいですか?**

**A:** macOS デバイスを登録するには: 

1.  [コンプライアンス ポリシーを作成する](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [macOS デバイスの条件付きアクセス ポリシーを定義する](active-directory-conditional-access-azure-portal.md) 

**解説:**

- 条件付きアクセス ポリシーに含まれているユーザーがリソースにアクセスするには、[macOS でサポートされているバージョンの Office](active-directory-conditional-access-technical-reference.md#client-apps-condition) が必要です。 

- 最初のアクセス試行中に、ユーザーは、会社のポータルを使用してデバイスを登録するよう求められます。

---

**Q: 最近、デバイスを登録しました。Azure Portal のユーザー情報にデバイスが表示されないのはなぜですか?**

**A:** ハイブリッド Azure AD 参加済みの Windows 10 デバイスは、[ユーザー デバイス] には表示されません。
Azure portal で [すべてのデバイス] ビューを使用する必要があります。 PowerShell の [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) コマンドレットを使用することもできます。

[ユーザー デバイス] には、次のデバイスだけが表示されます。

- ハイブリッド Azure AD 参加済みではないすべての個人用デバイス 
- Windows 10/Windows Server 2016 以外のすべてのデバイス
- Windows 以外のすべてのデバイス 

--- 

**Q: クライアントのデバイスの登録状態はどうすればわかりますか?**

**A:** Azure portal を使用して、[すべてのデバイス] に移動し、デバイス ID を入力してデバイスを検索することができます。 [結合の種類] 列の値を確認します。

登録デバイスからローカル デバイスの登録状態を確認するには:

- Windows 10 および Windows Server 2016 以降のデバイスの場合は、"dsregcmd.exe/status" を実行します。
- ダウンレベルの OS バージョンの場合は、"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" を実行します。

---

**Q: Azure portal または Windows PowerShell を使用して削除しましたが、デバイスのローカル状態にはまだ登録済みと表示されます。**

**A:** これは仕様です。 デバイスは、クラウドのリソースにアクセスできなくなります。 

再登録する場合は、デバイスで手動操作を実行する必要があります。 

オンプレミスの AD ドメインに参加済みの Windows 10 および Windows Server 2016 の参加状態をクリアするには、次の手順を実行します。

1.  管理者としてコマンド プロンプトを開きます。

2.  「`dsregcmd.exe /debug /leave`」と入力します。

3.  サインアウトしてからサインインして、デバイスを Azure AD に再登録するスケジュール済みタスクをトリガーします。 

オンプレミスの AD ドメインに参加済みのダウンレベルの Windows OS バージョンの場合は、次の手順を実行します。

1.  管理者としてコマンド プロンプトを開きます。
2.  「 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`」と入力します。
3.  「 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`」と入力します。

---
**Q: デバイス上の Azure AD 参加済みデバイスをローカルで参加解除するにはどうすればよいですか?**

**A:** 
- ハイブリッド Azure AD 参加済みデバイスの場合、スケジュールされたタスクによってデバイスが再登録されないように、自動登録をオフにます。 次に、管理者としてコマンド プロンプトを開き、「`dsregcmd.exe /debug /leave`」と入力します。 または、このコマンドを複数のデバイスに対するスクリプトとして実行し、一括で参加を解除することもできます。

- 純粋な Azure AD 参加済みデバイスの場合、Azure AD ユーザーの資格情報ではサインインできなくなるので、オフラインのローカル管理者アカウントを持っている必要があります。 次に、**[設定]** > **[アカウント]** > **[職場または学校にアクセスする]** に移動します。 アカウントを選択し、**[切断]** をクリックします。 画面の指示に従い、入力を求められたらローカル管理者の資格情報を入力します。 デバイスを再起動して参加の解除プロセスを完了します。

---

**Q: ユーザーが Azure AD 参加済みデバイスからプリンターを検索できません。どうすれば Azure AD 参加済みデバイスからの印刷を有効にできますか?**

**A:** Azure AD 参加済みデバイスにプリンターをデプロイする手順については、「[Hybrid cloud print](https://docs.microsoft.com/en-us/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)」 (ハイブリッド クラウド印刷) を参照してください。 ハイブリッド クラウド印刷には、Windows Server がオンプレミスで必要です。 現在、クラウドベースの印刷サービスは利用できません。 

---

**Q: Azure Portal に重複するデバイス エントリが表示されるのはなぜですか?**

**A:**

-   Windows 10 および Windows Server 2016 の場合、同じデバイスの参加を解除し、再度参加させる操作を繰り返した場合に、エントリの重複が発生することがあります。 

-   [職場または学校アカウントを追加] を使用した場合、[職場または学校アカウントを追加] を使用する各 Windows ユーザーは、同じデバイス名で新しいデバイス レコードを作成します。

-   自動登録を使用してオンプレミスの AD ドメインに参加しているダウンレベルの Windows OS バージョンでは、デバイスにログインするドメイン ユーザーごとに、同じデバイス名で新しいデバイス レコードが作成されます。 

-   Azure AD 参加済みコンピューターをワイプして再インストールし、同じ名前で再度参加させると、同じデバイス名で別のレコードとして表示されます。

---

**Q: Azure Portal で無効にしたデバイスからユーザーがリソースに引き続きアクセスできるのはなぜですか?**

**A:** 取り消しが適用されるまで最大 1 時間かかる場合があります。

>[!Note] 
>登録済みデバイスの場合、ユーザーがリソースにアクセスできないように、デバイスのワイプを実行することをお勧めします。 詳細については、「[管理するデバイスを Intune に登録する](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)」をご覧ください。 


---

**Q: ユーザーに "ここからアクセスすることはできません" というメッセージが表示されるのはなぜですか?**

**A:** デバイスの特定の状態を要求する条件付きアクセス規則を構成しており、デバイスが条件を満たしていない場合、ユーザーはブロックされ、このメッセージが表示されます。 このメッセージが表示されないようにするには、条件付きアクセス ポリシー ルールを評価し、デバイスが条件を満たすことができるようにしてください。

---

**Q: 一部のユーザーの Azure AD 参加済みデバイスで MFA のプロンプトが表示されないのはなぜですか?**

**A:** ユーザーが多要素認証を使用してデバイスを Azure AD に参加または登録すると、そのデバイス自体がそのユーザーにとっての信頼された 2 つ目の要素となります。 その後、同じユーザーがデバイスにサインインし、アプリケーションにアクセスすると、Azure AD ではデバイスが 2 つ目の要素として考慮されるので、ユーザーには MFA のプロンプトが表示されずアプリケーションにシームレスにアクセスできます。 そのデバイスにサインインする他のユーザーに対しては、このようには動作しないので、そのデバイスにアクセスするその他のすべてのユーザーには、MFA を必要とするアプリケーションにアクセスする前に MFA のプロンプトが表示されます。

---

**Q: Azure portal の [ユーザー情報] にデバイス レコードが表示され、デバイスに状態が登録済みと表示されます。条件付きアクセスの使用は正しく設定されていますか?**

**A:** deviceID に反映されたデバイスの参加状態が Azure AD での状態と一致しており、条件付きアクセスの評価基準を満たしている必要があります。 詳細については、「[Azure Active Directory Device Registration の基本](active-directory-device-registration.md)」をご覧ください。

---

**Q: Azure AD に参加したばかりのデバイスに対して、"ユーザー名またはパスワードが正しくありません" というメッセージが表示されるのはなぜですか?**

**A:** このシナリオの一般的な理由は次のとおりです。

- ユーザーの資格情報が有効ではなくなっています。

- コンピューターが Azure Active Directory と通信できません。 ネットワーク接続の問題を確認してください。

- Azure AD Join の前提条件が満たされていません。 「[Azure Active Directory Join を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-overview.md)」の手順に従っていることを確認してください。  

- フェデレーション ログインでは、フェデレーション サーバーが WS-Trust のアクティブ エンドポイントをサポートしている必要があります。 

- パス スルー認証を有効にしたため、ログオン時には変更する必要がある一時的なパスワードがユーザーに割り当てられています。

---

**Q: PC を Azure AD に参加させようとしたときに、[申し訳ありません。エラーが発生しました] ダイアログが表示されるのはなぜですか?**

**A:** これは、Intune への Azure Active Directory の登録の設定によるものです。 Azure AD への参加を試みているユーザーに、適切な Intune ライセンスが割り当てられていることを確認してください。 詳細については、「[Windows デバイスの管理をセットアップする](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment)」をご覧ください。  

---

**Q: エラー情報が表示されなかったにも関わらず、PC を参加させることができなかったのはなぜですか?**

**A:** ユーザーがローカルの組み込み管理者アカウントを使用してデバイスにログインしていることが原因と考えられます。 Azure Active Directory Join を使用してセットアップを完了する前に、別のローカル アカウントを作成してください。 

---

**Q: 自動デバイス登録の設定方法はどこで確認できますか。**

**A:** 詳しい手順については、「[Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法](active-directory-conditional-access-automatic-device-registration-setup.md)」をご覧ください。

---

**Q: デバイスの自動登録に関するトラブルシューティング情報はどこで入手できますか?**

**A:** トラブルシューティング情報については、次の記事をご覧ください。

- [Windows 10 および Windows Server 2016 の Azure AD ドメイン参加済みコンピューターの自動登録に関するトラブルシューティング](device-management-troubleshoot-hybrid-join-windows-current.md)

- [Windows ダウンレベル クライアントの Azure AD ドメイン参加済みコンピューターの自動登録に関するトラブルシューティング](device-management-troubleshoot-hybrid-join-windows-legacy.md)
 

---

