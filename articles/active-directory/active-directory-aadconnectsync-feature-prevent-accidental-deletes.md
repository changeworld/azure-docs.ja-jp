<properties
   pageTitle="Azure AD Connect sync: 誤って削除されないように保護する | Microsoft Azure"
   description="このトピックでは、Azure AD Connect の「誤って削除されないように保護する」機能について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/13/2015"
   ms.author="andkjell"/>

# 誤って削除されないように保護する
このトピックでは、Azure AD Connect の「誤って削除されないように保護する」機能について説明します。

Azure AD Connect のインストール中は、誤った削除操作を防止する機能が既定で有効になり、500 個を超える削除を行うエクスポートを許可しないように構成されます。この機能は、構成を誤って変更することと、たくさんのユーザーに影響を与える可能性があるオンプレミス ディレクトリの変更を防ぐように設計されています。

500 オブジェクトという既定値は PowerShell と `Enable-ADSyncExportDeletionThreshold` で変更できます。組織の規模に合わせてこの値を構成する必要があります。同期スケジューラは 3 時間おきに実行されるので、この値は 3 時間以内に確認される削除数になります。

この機能が有効なときに、Azure AD にエクスポートするようにステージングされた削除の数が多すぎる場合は、エクスポートは続行されず、次のような電子メールが送信されます。

![電子メールを誤って削除する](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

これを予想していなかった場合は、調査し、修正のアクションを実行します。削除されようとしているオブジェクトを表示するには、次の操作を行います。

1. [スタート] メニューから **[同期サービス]** を起動します。
2. **[コネクタ]** に進みます。
3. 種類が「**Azure Active Directory**」のコネクタを選択します。
4. 右にある **[アクション]** で、**[コネクタの検索領域]** を選択します。
5. ポップアップ ウィンドウの **[範囲]** で、**[切断時刻]** を選択し、過去の時間を選択します。**[検索]** をクリックします。削除されようとしているすべてのオブジェクトが表示されます。各項目をクリックすると、そのオブジェクトに関する追加情報を取得できます。あるいは、**[列設定]** をクリックし、グリッドに表示する属性を追加できます。

![コネクタ スペースの検索](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

すべての削除が望まれる場合、次のように操作します。

1. この保護を一時的に無効にし、これらの削除を行うには、PowerShell コマンドレットの `Disable-ADSyncExportDeletionThreshold` を実行します。
2. Azure Active Directory Connector が選択されている状態で、**[実行]** アクションを選択し、**[エクスポート]** を選択します。
3. 保護を再度有効にするには、PowerShell コマンドレットの `Enable-ADSyncExportDeletionThreshold` を実行します。

## 次のステップ
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=Oct15_HO3-->