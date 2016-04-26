<properties
	pageTitle="Azure AD Connect Sync によるパスワード同期の導入 | Microsoft Azure"
	description="パスワード同期の働きとパスワード同期を有効にする方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/15/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync によるパスワード同期の導入

パスワード同期を利用すれば、オンプレミス Active Directory パスワードを利用し、Azure Active Directory にもサインインできます。

このトピックでは、お使いの環境でパスワード同期を有効するために、また、パスワード同期の問題を解消するために必要な情報を提供します。


## パスワード同期とは

パスワード同期は、オンプレミス Active Directory からクラウド ベースの Azure Active Directory (Azure AD) にユーザー パスワードを同期するための機能です。この機能を使用すると、オンプレミス ネットワークにサインインするときと同じパスワードを使用し、Azure Active Directory サービス (Office 365、Microsoft Intune、CRM Online など) にサインインできます。この機能を利用するには、Azure Active Directory Connect 同期サービス (Azure AD Connect 同期) をインストールする必要があります。


> [AZURE.NOTE] FIPS とパスワード同期に対して構成されている Active Directory ドメイン サービスの詳細については、「[Azure AD Connect Sync: パスワード同期の実装](#password-synchronization-and-fips)」を参照してください。

## パスワード同期のしくみ

パスワード同期は、Azure AD Connect Sync によって実装されるディレクトリ同期の拡張機能です。この機能が動作するためには、オンプレミス AD と Azure Active Directory の間にディレクトリ同期が構成されている必要があります。

Active Directory ドメイン サービスは、実際のユーザー パスワードをハッシュ値表現の形式で格納します。パスワード ハッシュを使用できない操作:

- オンプレミス ネットワークにサインインする

- プレーン テキスト バージョンのパスワードに戻す

パスワードを同期するには、Azure AD Connect Sync でオンプレミス Active Directory からパスワード ハッシュを抽出します。Azure Active Directory 認証サービスに同期される前に、パスワード ハッシュには、追加のセキュリティ処理が行われます。パスワード同期処理の実際のデータ フローは、表示名や電子メール アドレスなどのユーザー データの同期と似ています。

パスワードは次のように同期されます

- ユーザーごとに

- 時系列順に

- 他の属性に対する標準のディレクトリ同期ウィンドウよりも頻繁に

パスワードが同期されると、既存のクラウド パスワードが上書きされます。

パスワード同期機能を初めて有効にすると、範囲内のすべてのユーザーの初回同期が実行されます。同期するユーザー パスワードのサブセットを明示的に定義することはできません。

オンプレミス パスワードを変更すると、更新されたパスワードは、多くの場合、ほんの数分で同期されます。パスワードの同期が失敗すると、パスワード同期機能は自動的に再試行します。パスワード同期の試行中にエラーが発生した場合、イベント ビューアーにエラーが記録されます。

パスワードの同期によって、現在ログイン中のユーザーが影響を受けることはありません。クラウド サービスにログインしている間、パスワード変更が同期された場合、クラウド サービス セッションがその影響をすぐに受けることはありません。しかしながら、クラウド サービスで認証が再び要求されると、すぐに新しいパスワードを指定する必要があります。

> [AZURE.NOTE] パスワード同期は、Active Directory でオブジェクトの種類がユーザーであるオブジェクトのみがサポートされます。オブジェクトの種類が INetOrgPerson であるオブジェクトはサポートされません。

### Azure AD Domain Services のパスワード同期のしくみ

Azure AD でこのサービスを有効にする場合、シングル サインオンを利用するには、パスワード同期オプションが必要です。このサービスを有効にすると、パスワード同期の動作が変わり、パスワード ハッシュも、そのままオンプレミスの Active Directory から Azure AD Domain Services に対して同期されます。この機能は ADMT (Active Directory 移行ツール) と似ており、Azure AD Domain Services はオンプレミス AD で使用できるすべての方法を使用してユーザーを認証できるようになります。

### セキュリティに関する考慮事項

パスワードを同期するとき、ユーザーのプレーン テキスト形式のパスワードは、パスワード同期機能にも、Azure AD や関連するどのサービスにも公開されません。

また、オンプレミス Active Directory でパスワードを可逆的に暗号化された形式で保存する必要はありません。Active Directory パスワード ハッシュのダイジェストが、オンプレミスの AD と Azure Active Directory の間の伝送に使用されます。パスワード ハッシュのダイジェストを使用して、オンプレミス環境のリソースにアクセスすることはできません。

### パスワード ポリシーの考慮事項

パスワード同期を有効にすることによって影響を受ける 2 種類のパスワード ポリシーがあります。

1. パスワードの複雑性のポリシー
2. パスワードの有効期限のポリシー

**パスワードの複雑性のポリシー**

パスワード同期を有効にすると、オンプレミス Active Directory で構成されているパスワードの複雑性のポリシーにより、同期されるユーザーに対してクラウドで定義されている複雑性のポリシーが上書きされます。つまり、ユーザーのオンプレミス Active Directory 環境において有効なパスワードは、Azure AD サービスのアクセスに使用できます。

> [AZURE.NOTE] クラウド内で直接作成されたユーザーのパスワードには、引き続きクラウドで定義されているパスワード ポリシーが適用されます。

**パスワードの有効期限のポリシー**

ユーザーがパスワード同期のスコープ内にいる場合、クラウド アカウントのパスワードは "*期限なし*" に設定されます。つまり、オンプレミス環境で期限切れになった同期パスワードを利用し、引き続きクラウド サービスにログインできます。

クラウドのパスワードは、次にオンプレミス環境でパスワードを変更したときに更新されます。

### 同期されたパスワードの上書き

管理者は PowerShell を使用してパスワードを手動でリセットできます。

この場合、新しいパスワードによって同期されたパスワードは上書きされ、クラウドで定義されているすべてのパスワード ポリシーが新しいパスワードに適用されます。

オンプレミスのパスワードを再び変更した場合、新しいパスワードはクラウドに同期され、手動で更新したパスワードを上書きします。


## パスワード同期の有効化

パスワード同期は、次の 2 とおりの方法で有効にできます。

- Azure AD Connect のインストール時に簡単設定を使用すると、既定でパスワード同期が有効になります。

- Azure AD Connect のインストール時にカスタム設定を使用すると、ユーザー サインイン ページでパスワード同期が有効になります。

<br> ![パスワード同期の有効化](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png) <br>

**AD FS とのフェデレーション**の使用を選択する場合、AD FS インフラストラクチャで障害が発生した際のバックアップとして、パスワード同期を有効にすることができます。Azure AD Domain Services を使用する予定がある場合にも、有効にすることができます。

### パスワード同期と FIPS

FIPS (Federal Information Processing Standard) に従ってサーバーがロックされた場合、MD5 は無効になります。パスワード同期のために MD5 を有効にするには、C:\\Program Files\\Azure AD Sync\\Bin の miiserver.exe.config に enforceFIPSPolicy キーを追加します。

```
<configuration>
    <runtime>
        <enforceFIPSPolicy enabled="false"/>
    </runtime>
</configuration>
```

config ファイルの末尾に configuration/runtime ノードがあります。

セキュリティと FIPS の詳細については、[AAD パスワード同期、暗号化、FIPS コンプライアンス](http://blogs.technet.com/b/ad/archive/2014/06/28/aad-password-sync-encryption-and-and-fips-compliance.aspx)に関するページを参照してください。


## パスワード同期のトラブルシューティング

**パスワード同期のトラブルシューティングを行うには、次の手順を実行します。**

1. **Synchronization Service Manager** を開きます。

2. **[コネクタ]** をクリックします。

3. ユーザーが存在する Active Directory コネクタを選択します。

4. **[コネクタ スペースの検索]** を選択します。

5. 探しているユーザーを特定します。

6. **[系列]** タブを選択し、少なくとも 1 つの同期規則の **[パスワード同期]** に **[True]** と表示されていることを確認します。既定の構成では、**[In from AD - User AccountEnabled]** という名前の同期規則です。

    ![Lineage information about a user](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)

7. また、メタバースを経由し、Azure AD Connector スペースまで[ユーザーをフォローする](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system)必要があります。コネクタ スペース オブジェクトには送信規則を与える必要があります。**パスワード同期**を **True** に設定します。既定の構成では、**[Out to AAD - User Join]** という名前の同期規則です。

    ![Connector space properties of a user](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)

8. オブジェクトのパスワード同期の詳細を確認するには、**[ログ]** をクリックします。<br> 過去 1 週間分、ユーザーのパスワード同期状態の履歴を表示するページが生成されます。

    ![Object log details](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)

状態列には次の値が入ります。

| 状態 | 説明 |
| ---- | ----- |
| 成功 | パスワードが正常に同期されました。 |
| FilteredByTarget | パスワードは **[ユーザーは次回ログオン時にパスワードの変更が必要]** に設定されています。パスワードは同期されていません。 |
| NoTargetConnection | メタバースまたは Azure AD コネクタ スペースなオブジェクトがありません。 |
| SourceConnectorNotPresent | オンプレミスの Active Directory コネクタ スペースにオブジェクトがありません。 |
| TargetNotExportedToDirectory | Azure AD コネクタ スペースのオブジェクトはまだエクスポートされていません。 |
| MigratedCheckDetailsForMoreInfo | ログ エントリはビルド 1.0.9125.0 より前に作成されており、従来の状態で表示されます。 |


## すべてのパスワードの完全同期の開始

通常、すべてのパスワードの完全同期を強制する必要はありません。<br> ただし、必要に応じて、次のスクリプトを使用してすべてのパスワードの完全同期を開始できます。

    $adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
    $aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
    Import-Module adsync
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter “Microsoft.Synchronize.ForceFullPasswordSync”, String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true




## 次のステップ

* [Azure AD Connect Sync: 同期オプションのカスタマイズ](active-directory-aadconnectsync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0420_2016-->