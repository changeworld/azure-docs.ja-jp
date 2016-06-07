<properties
	pageTitle="Azure AD Connect 同期サービスの機能と構成 | Microsoft Azure"
	description="Azure AD Connect 同期サービスのサービス側の機能について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="andkjell;markvi"/>

# Azure AD Connect 同期サービスの機能

Azure AD Connect の同期機能には 2 つのコンポーネントがあります。

- **Azure AD Connect Sync** という名前のオンプレミスのコンポーネント: **同期エンジン**とも呼ばれます。
- Azure AD 内にあるサービス: **Azure AD Connect 同期サービス**とも呼ばれます。

このトピックでは、**Azure AD Connect 同期サービス**の各種機能のしくみと、Windows PowerShell を使用してそれらを構成する方法について説明します。

これらの設定は、[Windows PowerShell 用 Azure Active Directory モジュール](http://aka.ms/aadposh)で構成されています。これらの設定を構成するには、このモジュールを Azure AD Connect とは別にダウンロードしてインストールする必要があります。ここで紹介するコマンドレットは、[2016 年 3 月のリリース (ビルド 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1) で導入されたものです。このトピックに記載されているコマンドレットがないか、同じ結果が生成されない場合は、最新バージョンが実行されていることを確認してください。

Azure AD ディレクトリ内の構成を確認するには、`Get-MsolDirSyncFeatures` を実行します。 ![Get-MsolDirSyncFeatures result](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

これらの設定の多くは、Azure AD Connect でのみ変更できます。

以下の設定は、`Set-MsolDirSyncFeature` で設定できます。

DirSyncFeature | コメント
--- | ---
 [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) | エクスポート時に、別のオブジェクトとの重複がある場合、オブジェクト全体が失敗するのではなく、属性を検疫できます。
[EnableSoftMatchOnUpn](#userprincipalname-soft-match) | プライマリ SMTP アドレスに加えて userPrincipalName でオブジェクトを結合できます。
[SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) | 同期エンジンに管理対象ユーザー/ライセンス ユーザー (非フェデレーション ユーザー) の userPrincipalName 属性の更新を許可します。

いったん機能を有効にすると、後でもう一度無効にすることはできません。

以下の設定は、Azure AD Connect によって構成されており、`Set-MsolDirSyncFeature` で変更することはできません。

DirSyncFeature | コメント
--- | ---
DeviceWriteback | [Azure AD Connect: デバイスの書き戻しの有効化](active-directory-aadconnect-feature-device-writeback.md)
DirectoryExtensions | [Azure AD Connect 同期: ディレクトリ拡張機能](active-directory-aadconnectsync-feature-directory-extensions.md)
PasswordSync | [Azure AD Connect Sync によるパスワード同期の導入](active-directory-aadconnectsync-implement-password-synchronization.md)
UnifiedGroupWriteback | [プレビュー: グループの書き戻し](active-directory-aadconnect-feature-preview.md#group-writeback)
UserWriteback | 現在、サポートされていません。

## 重複属性の回復性
UPN や proxyAddress が重複している場合、そのオブジェクトのプロビジョニングが失敗する代わりに、重複している属性を "検疫" し、必要に応じて一時的な値を割り当てます。競合が解決されると、一時的な UPN は適切な値に自動的に修正されます。この動作は、UPN と proxyAddress に個別に有効にできます。詳細については、「[ID 同期と重複属性の回復性](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)」を参照してください。

## UserPrincipalName のあいまい一致
この機能を有効にすると、[プライマリ SMTP アドレス](https://support.microsoft.com/kb/2641663)だけでなく、UPN にもあいまい一致が適用されます。プライマリ SMTP アドレスでは、あいまい一致が常に有効になっています。あいまい一致は、Azure AD 内の既存のクラウド ユーザーをオンプレミスのユーザーと照合するために使用されます。

この機能は、Exchange Online を使用していない環境で、オンプレミスの AD アカウントを、クラウドで作成された既存のアカウントと照合する必要がある場合に特に役立ちます。このような状況では、通常、クラウドで SMTP 属性を設定する理由がありません。

新たに作成した Azure AD ディレクトリでは、この機能は既定で有効になっています。この機能が有効になっているかどうかを確認するには、次のコマンドレットを実行します。
```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

この機能が Azure AD ディレクトリに対して有効になっていない場合は、次のコマンドレットを実行して有効にすることができます。
```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## userPrincipalName の更新の同期
これまで、オンプレミスから同期サービスを使用して UserPrincipalName 属性を更新することは、次の 2 つの条件が両方とも当てはまらない限り、できませんでした。

- ユーザーが管理対象ユーザー (非フェデレーション ユーザー) である。
- ユーザーにライセンスが割り当てられていない。

詳細については、「[Office 365、Azure、Intune などの組織アカウントのユーザー名が、オンプレミスの UPN または代替ログイン ID と一致しない](https://support.microsoft.com/kb/2523192)」を参照してください。

この機能を有効にすると、userPrincipalName がオンプレミスで変更されたときに、パスワード同期を使用している場合は、同期エンジンによって userPrincipalName が更新されます。フェデレーションを使用している場合は、この機能は動作しません。

新たに作成した Azure AD ディレクトリでは、この機能は既定で有効になっています。この機能が有効になっているかどうかを確認するには、次のコマンドレットを実行します。
```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

この機能が Azure AD ディレクトリに対して有効になっていない場合は、次のコマンドレットを実行して有効にすることができます。
```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

この機能を有効にすると、既存の userPrincipalName の値はそのまま維持されます。次に serPrincipalName 属性をオンプレミスで変更したときに、ユーザーに関する通常の差分同期によって UPN が更新されます。

## 機能の変更点
これらの設定は、今後すべての Azure AD ディレクトリで有効になる予定です。

## 関連項目

- [Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md)

- [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0525_2016-->