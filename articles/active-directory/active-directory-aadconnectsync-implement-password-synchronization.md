<properties
	pageTitle="Azure AD Connect Sync によるパスワード同期の導入 | Microsoft Azure"
	description="パスワード同期の働きとパスワード同期を有効にする方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/20/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync によるパスワード同期の導入

このトピックでは、オンプレミス Active Directory (AD) からクラウド ベースの Azure Active Directory (Azure AD) にユーザー パスワードを同期するために必要な情報を示します。


## パスワード同期とは

パスワードを忘れたために作業ができなくなる確率は、覚えておかなければならないパスワードの数と関係があります。覚えておかなければならないパスワードの数が多いほど、パスワードを忘れる確率は高まります。パスワードのリセットおよびその他のパスワード関連の問題についての質問や電話への対応に、最も多くのヘルプデスク リソースが費やされています。

パスワード同期は、オンプレミス Active Directory からクラウド ベースの Azure Active Directory (Azure AD) にユーザー パスワードを同期するための機能です。この機能を使用すると、オンプレミス Active Directory にサインインするときと同じパスワードを使って、Azure Active Directory サービス (Office 365、Microsoft Intune、CRM Online、Azure AD ドメイン サービスなど) にサインインできます。

![What is Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

パスワード同期によって、覚えておく必要があるパスワードの数を減らして 1 つだけにすると、次のような利点があります。

- ユーザーの生産性向上
- ヘルプデスク関連コストの削減

また、[**AD FS とのフェデレーション**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)の使用を選択する場合、AD FS インフラストラクチャで障害が発生した際のバックアップとして、パスワード同期を有効にすることができます。

パスワード同期は、Azure AD Connect Sync によって実装されるディレクトリ同期の拡張機能です。環境でパスワード同期を使用するには、以下のことを行う必要があります。

- Azure AD Connect のインストール

- オンプレミス AD と Azure Active Directory 間のディレクトリ同期の構成

- パスワード同期の有効化


詳細については、「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。



> [AZURE.NOTE] FIPS とパスワード同期に対して構成されている Active Directory ドメイン サービスの詳細については、「[Azure AD Connect Sync: パスワード同期の実装](#password-synchronization-and-fips)」を参照してください。

## パスワード同期のしくみ

Active Directory ドメイン サービスは、実際のユーザー パスワードをハッシュ値表現の形式で格納します。ハッシュ値は、一方向の数学関数 ("*ハッシュ アルゴリズム*") の結果として求められます。一方向の関数の結果をパスワードのプレーン テキスト バージョンに戻す方法はありません。パスワードのハッシュを使用してオンプレミスのネットワークにサインインすることはできません。

パスワードを同期するには、Azure AD Connect Sync でオンプレミス Active Directory からパスワード ハッシュを抽出します。Azure Active Directory 認証サービスに同期される前に、パスワード ハッシュには、追加のセキュリティ処理が行われます。パスワードは、ユーザーごとに、時間順に同期されます。

パスワード同期処理の実際のデータ フローは、表示名や電子メール アドレスなどのユーザー データの同期と似ています。ただし、パスワードは、他の属性に対する標準のディレクトリ同期ウィンドウよりも頻繁に同期されます。パスワードの同期プロセスは 2 分間隔で実行されます。このプロセスの頻度を変更することはできません。パスワードを同期すると、既存のクラウド パスワードが上書きされます。

パスワード同期機能を初めて有効にすると、スコープ内のすべてのユーザーの初回同期が実行されます。同期するユーザー パスワードのサブセットを明示的に定義することはできません。

オンプレミス パスワードを変更すると、更新されたパスワードは、多くの場合、ほんの数分で同期されます。パスワードの同期が失敗すると、パスワード同期機能は自動的に再試行します。パスワード同期の試行中にエラーが発生した場合、イベント ビューアーにエラーが記録されます。

パスワードの同期によって、現在ログイン中のユーザーが影響を受けることはありません。クラウド サービスにログインしている間、パスワード変更が同期された場合、現在のクラウド サービス セッションがその影響をすぐに受けることはありません。しかしながら、クラウド サービスで認証が再び要求された場合は、新しいパスワードを指定する必要があります。

> [AZURE.NOTE] パスワード同期は、Active Directory でオブジェクトの種類がユーザーであるオブジェクトのみがサポートされます。オブジェクトの種類が INetOrgPerson であるオブジェクトはサポートされません。

### Azure AD Domain Services のパスワード同期のしくみ

パスワード同期機能を使用して、オンプレミス パスワードを [Azure AD ドメイン サービス](../active-directory-domain-services/active-directory-ds-overview.md)に同期することもできます。このシナリオでは、Azure AD ドメイン サービスはオンプレミス AD で使用できるすべての方法を使用してユーザーを認証できるようになります。このシナリオのエクスペリエンスは、オンプレミス環境で Active Directory 移行ツール (ADMT) を使用する場合に似ています。


### セキュリティに関する考慮事項

パスワードを同期するとき、ユーザーのプレーン テキスト形式のパスワードは、パスワード同期機能にも、Azure AD や関連するどのサービスにも公開されません。

また、オンプレミス Active Directory でパスワードを可逆的に暗号化された形式で保存する必要はありません。Active Directory パスワード ハッシュのダイジェストが、オンプレミスの AD と Azure Active Directory の間の伝送に使用されます。パスワード ハッシュのダイジェストを使用して、オンプレミス環境のリソースにアクセスすることはできません。

### パスワード ポリシーの考慮事項

パスワード同期を有効にすることによって影響を受ける 2 種類のパスワード ポリシーがあります。

1. パスワードの複雑性のポリシー
2. パスワードの有効期限のポリシー

**パスワードの複雑性のポリシー**

パスワード同期を有効にすると、オンプレミス Active Directory でのパスワードの複雑性ポリシーによって、同期済みユーザーに対するクラウドでの複雑性ポリシーが上書きされます。オンプレミス Active Directory の有効なパスワードすべてを Azure AD サービスへのアクセスに使用することができます。

> [AZURE.NOTE] クラウド内で直接作成されたユーザーのパスワードには、引き続きクラウドで定義されているパスワード ポリシーが適用されます。

**パスワードの有効期限のポリシー**

ユーザーがパスワード同期のスコープ内にいる場合、クラウド アカウントのパスワードは "*期限なし*" に設定されます。オンプレミス環境で期限切れになった同期パスワードを利用し、引き続きクラウド サービスにサインインできます。クラウドのパスワードは、次にオンプレミス環境でパスワードを変更したときに更新されます。

### 同期されたパスワードの上書き

管理者は Windows PowerShell を使用してパスワードを手動でリセットできます。

この場合、新しいパスワードによって同期されたパスワードは上書きされ、クラウドで定義されているすべてのパスワード ポリシーが新しいパスワードに適用されます。

オンプレミスのパスワードを再び変更した場合、新しいパスワードはクラウドに同期され、手動で更新したパスワードを上書きします。


## パスワード同期の有効化

**簡単設定**を使用して Azure AD Connect をインストールすると、パスワード同期が自動的に有効になります。詳細については、「[簡単設定を使用した Azure AD Connect の開始](active-directory-aadconnect-get-started-express.md)」を参照してください。

Azure AD Connect のインストール時にカスタム設定を使用すると、ユーザー サインイン ページでパスワード同期が有効になります。詳細については、「[Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」を参照してください。


![パスワード同期の有効化](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)


### パスワード同期と FIPS

Federal Information Processing Standard (FIPS) に従ってサーバーがロックされた場合、MD5 は無効になります。


**パスワード同期で MD5 を有効にするには、次の手順を実行します。**

    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>

1. **%programfiles%\\Azure AD Sync\\Bin** に移動します。
2. **miiserver.exe.config** を開きます。
2. **configuration/runtime** ノード (ファイル末尾) に移動します。
3. 次のノードを追加します: **<enforceFIPSPolicy enabled="false"/>**
4. 変更を保存します。

セキュリティと FIPS の詳細については、[AAD パスワード同期、暗号化、FIPS コンプライアンス](http://blogs.technet.com/b/ad/archive/2014/06/28/aad-password-sync-encryption-and-and-fips-compliance.aspx)に関するページを参照してください。


## パスワード同期のトラブルシューティング

オブジェクトの現在の状態を確認すると、パスワード同期に関連した問題を簡単に解決できます。


**パスワード同期のトラブルシューティングを行うには、次の手順を実行します。**

1. **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)** を起動します。

2. **[コネクタ]** をクリックします。

3. ユーザーが存在する **Active Directory コネクタ**を選択します。

4. **[コネクタ スペースの検索]** を選択します。

5. 探しているユーザーを特定します。

6. **[系列]** タブを選択し、少なくとも 1 つの同期規則の **[パスワード同期]** に **[True]** と表示されていることを確認します。既定の構成では、**[In from AD - User AccountEnabled]** という名前の同期規則です。

    ![Lineage information about a user](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)

7. また、メタバースを経由し、Azure AD コネクタ スペースまで[ユーザーをフォローする](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system)必要があります。コネクタ スペース オブジェクトには送信規則を与える必要があります。**パスワード同期**を **True** に設定します。既定の構成では、**[Out to AAD - User Join]** という名前の同期規則です。

    ![Connector space properties of a user](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)

8. 過去 1 週間のオブジェクトのパスワード同期の詳細を確認するには、**[ログ]** をクリックします。

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

通常、すべてのパスワードの完全同期をトリガーする必要はありません。ただし、必要に応じて、次のスクリプトを使用してすべてのパスワードの完全同期を開始できます。

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

<!---HONumber=AcomDC_0727_2016-->