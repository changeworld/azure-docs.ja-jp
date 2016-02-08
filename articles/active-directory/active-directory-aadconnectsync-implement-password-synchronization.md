<properties
	pageTitle="Azure AD Connect Sync: パスワード同期の実装 | Microsoft Azure"
	description="パスワード同期機能のしくみを理解するために必要な情報と環境でパスワード同期を有効にする方法について説明します。"
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
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync: パスワード同期の実装

パスワード同期により、ユーザーはオンプレミス Active Directory にサインインするときと同じパスワードを使用して Azure Active Directory にサインインできるようになります。

このトピックでは、パスワード同期機能のしくみを理解するために必要な情報と環境でパスワード同期を有効にする方法について説明します。

## パスワード同期とは

パスワード同期は Azure Active Directory Connect 同期サービス (Azure AD Connect Sync) の機能であり、オンプレミス Active Directory と Azure Active Directory (Azure AD) のユーザー パスワードを同期します。この機能を使用すると、ユーザーは、オンプレミス ネットワークにログインするときと同じパスワードを使用して、Azure Active Directory のサービス (Office 365、Microsoft Intune、CRM Online など) にログインできます。

> [AZURE.NOTE] FIPS とパスワード同期に対して構成されている Active Directory ドメイン サービスの詳細については、「[Azure AD Connect Sync: パスワード同期の実装](#password-synchronization-and-fips)」を参照してください。

### パスワード同期の可用性

Azure Active Directory のユーザーはすべてパスワード同期を実行できます。パスワード同期とフェデレーション認証などの他の機能との互換性については、以下をご覧ください。

### パスワード同期のしくみ

パスワード同期は、Azure AD Connect Sync によって実装されるディレクトリ同期の拡張機能です。そのため、この機能が動作するためには、オンプレミスと Azure Active Directory の間にディレクトリ同期が構成されている必要があります。

Active Directory ドメイン サービスは、実際のユーザー パスワードをハッシュ値表現の形式で格納します。パスワードのハッシュを使用してオンプレミスのネットワークにサインインすることはできません。また、設計上、ユーザーのプレーン テキスト パスワードにアクセスするように元に戻すことはできません。パスワードを同期するには、Azure AD Connect Sync でオンプレミス Active Directory のパスワード ハッシュを抽出します。Azure Active Directory 認証サービスに同期される前に、パスワード ハッシュには、追加のセキュリティ処理が行われます。パスワード同期処理の実際のデータ フローは、表示名や電子メール アドレスなどのユーザー データの同期と似ています。

パスワードは、他の属性に対する標準のディレクトリ同期ウィンドウよりも頻繁に同期されます。パスワードは、ユーザーごとに同期され、通常は時間順に同期されます。ユーザーのパスワードがオンプレミス AD からクラウドに対して同期されるとき、既存のクラウド パスワードは上書きされます。

パスワード同期機能を初めて有効にすると、パスワードの初期同期を実行し、スコープ内のすべてのユーザーのパスワードをオンプレミス Active Directory から Azure Active Directory に同期します。パスワードをクラウドに同期するユーザーのセットを明示的に定義することはできません。その後は、オンプレミス ユーザーがパスワードを変更すると、パスワード同期機能は、ほとんどの場合は数分以内に、それを検出して変更されたパスワードを同期します。パスワードの同期が失敗すると、パスワード同期機能は自動的に再試行します。パスワード同期の試行中にエラーが発生した場合、イベント ビューアーにエラーが表示されます。

パスワードの同期によって、現在ログイン中のユーザーが影響を受けることはありません。クラウド サービスにログインしているユーザーがオンプレミスのパスワードを変更した場合でも、クラウド サービスのセッションは中断せずに続けられます。ただし、クラウド サービスでユーザーの再認証が必要になったときは、すぐに新しいパスワードを提供する必要があります。この時点で、ユーザーは新しいパスワード、つまりその前にオンプレミス Active Directory からクラウドに同期されたパスワードを提供する必要があります。

> [AZURE.NOTE] パスワード同期は、Active Directory でオブジェクトの種類がユーザーであるオブジェクトのみがサポートされます。オブジェクトの種類が INetOrgPerson であるオブジェクトはサポートされません。

### Azure AD Domain Services のパスワード同期のしくみ

Azure AD でこのサービスを有効にする場合、シングル サインオンを利用するには、パスワード同期オプションが必要です。このサービスを有効にすると、パスワード同期の動作が変わり、パスワード ハッシュも、そのままオンプレミスの Active Directory から Azure AD Domain Services に対して同期されます。この機能は、ADMT (Active Directory 移行ツール) と似ており、Azure AD Domain Services はオンプレミス AD で使用できるすべての方法を使用してユーザーを認証できるようになります。

### セキュリティに関する考慮事項

パスワードを同期するとき、ユーザーのプレーン テキスト形式のパスワードは、パスワード同期機能にも、Azure AD や関連するどのサービスにも公開されません。

また、オンプレミス Active Directory でパスワードを可逆的に暗号化された形式で保存する必要はありません。Active Directory パスワード ハッシュのダイジェストが、オンプレミスの AD と Azure Active Directory の間の伝送に使用されます。パスワード ハッシュのダイジェストを使用して、ユーザーのオンプレミス環境のリソースにアクセスすることはできません。

### パスワード ポリシーの考慮事項

パスワード同期を有効にすることによって影響を受ける 2 種類のパスワード ポリシーがあります。

1. パスワードの複雑性のポリシー
2. パスワードの有効期限のポリシー

**パスワードの複雑性のポリシー**

パスワード同期を有効にすると、オンプレミス Active Directory で構成されているパスワードの複雑性のポリシーにより、同期されるユーザーに対してクラウドで定義されている複雑性のポリシーが上書きされます。つまり、ユーザーのオンプレミス Active Directory 環境において有効なパスワードは、Azure AD サービスのアクセスに使用できます。

> [AZURE.NOTE] クラウド内で直接作成されたユーザーのパスワードには、引き続きクラウドで定義されているパスワード ポリシーが適用されます。

**パスワードの有効期限のポリシー**

ユーザーがパスワード同期のスコープ内にいる場合、クラウド アカウントのパスワードは "*期限なし*" に設定されます。つまり、オンプレミス環境でユーザーのパスワードの期限が切れても、クラウド サービスにはその期限切れパスワードを使用して引き続きログインできる可能性があります。

クラウドのパスワードは、次にユーザーがオンプレミス環境でパスワードを変更したときに更新されます。

### 同期されたパスワードの上書き

管理者は、Azure Active Directory PowerShell を使用して手動でユーザーのパスワードをリセットできます。

この場合、新しいパスワードによってユーザーの同期されたパスワードは上書きされ、クラウドで定義されているすべてのパスワード ポリシーが新しいパスワードに適用されます。

ユーザーがオンプレミスのパスワードを再び変更した場合、新しいパスワードはクラウドに同期され、手動で更新したパスワードを上書きします。

## パスワード同期の準備


### パスワード同期の有効化

Azure AD Connect のインストール時に簡単設定を使用すると、既定でパスワード同期が有効になります。

Azure AD Connect のインストール時にカスタム設定を使用すると、ユーザー サインイン ページでパスワード同期が有効になります。![usersignin](./media/active-directory-aadsync-implement-password-synchronization/usersignin.png)

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

## パスワード同期の管理

### パスワード同期のトラブルシューティング

**Synchronization Service Manager** を起動し、**[コネクタ]** を開き、ユーザーが属する Active Directory Connector を選択し、**[コネクタ スペースの検索]** を選択し、対象のユーザーを検索します。

![csuser](./media/active-directory-aadsync-implement-password-synchronization/cspasswordsync.png)

ユーザーについて、**[系列]** タブを選択し、少なくとも 1 つの同期規則の **[パスワード同期]** に **[True]** と表示されていることを確認します。既定の構成では、**[In from AD - User AccountEnabled]** という同期規則です。

オブジェクトのパスワード同期の詳細を確認するには、このページの下部にある **[ログ...]** をクリックします。過去 1 週間分、ユーザーのパスワード同期状態の履歴が表示されます。

![オブジェクト ログ](./media/active-directory-aadsync-implement-password-synchronization/csobjectlog.png)

状態列には次のような値が表示されます。ここには、問題と、パスワードが同期されない理由も表示されます。

| 状態 | 説明 |
| ---- | ----- |
| 成功 | パスワードが正常に同期されました。 |
| FilteredByTarget | パスワードは **[ユーザーは次回ログオン時にパスワードの変更が必要]** に設定されています。パスワードは同期されていません。 |
| NoTargetConnection | メタバースまたは Azure AD コネクタ スペースなオブジェクトがありません。 |
| SourceConnectorNotPresent | オンプレミスの Active Directory コネクタ スペースにオブジェクトがありません。 |
| TargetNotExportedToDirectory | Azure AD コネクタ スペースのオブジェクトはまだエクスポートされていません。 |
| MigratedCheckDetailsForMoreInfo | ログ エントリはビルド 1.0.9125.0 より前に作成されており、従来の状態で表示されます。 |


### すべてのパスワードの完全同期の開始
すべてのパスワードを強制的に完全同期する必要はありませんが、何らかの理由で必要な場合は、PowerShell を利用できます。

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




## その他のリソース

* [Azure AD Connect Sync: 同期オプションのカスタマイズ](active-directory-aadconnectsync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0128_2016-->