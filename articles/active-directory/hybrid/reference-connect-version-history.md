---
title: Azure AD Connect:バージョンのリリース履歴 | Microsoft Docs
description: この記事では、Azure AD Connect と Azure AD Sync のすべてのリリースの一覧を示します。
author: billmath
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 09/21/2021
ms.subservice: hybrid
ms.author: rodejo
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: daaf76866b42196808d88d56725d4edbd54543d5
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064668"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect:バージョンのリリース履歴
Azure Active Directory (Azure AD) チームは、Azure AD Connect を定期的に更新し、新機能を追加しています。 すべての追加機能がすべてのユーザーに適用されるわけではありません。

この記事は、リリースされたバージョンを追跡し、最新バージョンで変更された点を確認するためのものです。

次の表に関連トピックの一覧を示します。

トピック |  詳細
--------- | --------- |
Azure AD Connect からのアップグレード手順 | Azure AD Connect の [以前のバージョンから最新バージョンにアップグレード](how-to-upgrade-previous-version.md) するさまざまな方法を説明しています。
必要なアクセス許可 | 更新プログラムの適用に必要なアクセス許可については、[アカウントとアクセス許可](reference-connect-accounts-permissions.md#upgrade)に関するページを参照してください。


>[!IMPORTANT]
> **2022 年 8 月 31 日に、Azure Active Directory (Azure AD) Connect の 1.x バージョンはすべて廃止されます。これは、サポートされなくなる SQL Server 2012 コンポーネントが含まれているためです。** その日付までに最新バージョンの Azure AD Connect (2.x バージョン) にアップグレードするか、[Azure AD クラウド同期を評価して切り替えてください](../cloud-sync/what-is-cloud-sync.md)。
> 
> 最適なサポートエクスペリエンスを得るには、必ず最新バージョンの Azure AD Connect を実行していることを確認します。 
> 
> 廃止されたバージョンの Azure AD Connect を実行すると、予期せず動作しなくなる可能性があり、最新のセキュリティ修正、パフォーマンスの向上、トラブルシューティングと診断ツール、およびサービスの機能強化が受けられない場合があります。 さらに、サポートが必要な場合も、組織が必要とするレベルのサービスを受けられないおそれがあります。
> 
> 詳細について、V2.0 の変更点について、およびこの変化による影響について詳しくは、[Azure Active Directory Connect V2.0](whatis-azure-ad-connect-v2.md) に関する記事を参照してください。
>
> Azure AD Connect を最新バージョンにアップグレードする方法の詳細については、[この記事](./how-to-upgrade-previous-version.md)を参照してください。
>
> 廃止されたバージョンのバージョン履歴情報については、[Azure AD Connect バージョンのリリース履歴アーカイブ](reference-connect-version-history-archive.md)に関する記事を参照してください。

>[!NOTE]
>Azure AD Connect の新バージョンのリリースは、サービスの操作の機能性を確保するためのいくつかの品質管理手順が必要なプロセスであり、このプロセスを進めている間、新しいリリースのバージョン番号とリリースの状態が、最新の状況を反映するように更新されます。
Azure AD Connect のすべてのリリースが自動アップグレードに対応しているわけではありません。 リリースの状態により、リリースが自動アップグレードに対応しているか、ダウンロードにのみ対応しているかが分かります。 Azure AD Connect サーバーに対して自動アップグレードが有効になっている場合、そのサーバーは、自動アップグレードのためにリリースされた Azure AD Connect の最新バージョンに自動的にアップグレードされます。 Azure AD Connect のすべての構成が自動アップグレードの対象となっているわけではないので注意してください。 

>自動アップグレードの用途を明確にすると、これは、すべての重要な更新プログラムとクリティカルな修正プログラムをユーザーにプッシュすることを意図しています。 必ずしも最新バージョンとは限りません。その理由は、すべてのバージョンにおいて、重大なセキュリティ問題 (多くの例の 1 つ) に対する修正プログラムが必要になったり、含まれたりするわけではないからです。 重大な問題は、一般的には自動アップグレードを通じて提供される新しいバージョンによって対処されます。 そのような問題がない場合は、自動アップグレードを使用して更新プログラムがプッシュアウトされることはありません。一般には、最新の自動アップグレードのバージョンを使用していれば問題ありません。
ただし、すべての最新機能と更新プログラムを希望する場合、それがあるかどうかを確認するための最良の方法は、このページを確認し、必要に応じてそれらをインストールすることです。 

>詳細については、[自動アップグレード](how-to-connect-install-automatic-upgrade.md)に関するこのリンクを参照してください。


## <a name="download-links"></a>ダウンロード リンク
 - Windows Server 2016 以降を使用している場合は、Azure AD Connect V2.0 を使用する必要があります。 Azure AD Connect 2.0 の最新バージョンは、[こちらのリンク](https://www.microsoft.com/en-us/download/details.aspx?id=47594)からダウンロードできます。
 - 以前のバージョンの Windows Server を使用している場合は、Azure AD Connect V1.6 を使用する必要があります。 Azure AD Connect V1 の最新バージョンは、[このリンク](https://www.microsoft.com/download/details.aspx?id=103336)を使用してダウンロードできます。 
 - 今後、V1 バージョンには重要な変更のみを適用する予定であるため、V1 リリースでは V2 の機能や修正プログラムの一部が見つかからない場合があります。そのため、できるだけ早く V2 バージョンにアップグレードする必要があります。

## <a name="16160"></a>1.6.16.0
>[!NOTE] 
>これは Azure AD Connect の修正プログラムのリリースです。 このバージョンは、古いバージョンの Windows Server を実行していて、この時点でサーバーを Windows Server 2016 以降にアップグレードできないお客様が使用することを目的としています。 このバージョンを使用して Azure AD Connect V2.0 サーバーを更新することはできません。 このリリースは、Windows Server 2016 以降ではサポートされていません。 このリリースには SQL Server 2012 コンポーネントが含まれていますが、2022 年 8 月 31 日に廃止される予定です。 その日より前に、サーバー OS と AADConnect のバージョンをアップグレードする必要があります。
>このバージョンのダウンロードが可能になると、対象となるテナントの自動アップグレードが開始されます。自動アップグレードが完了するまで、数週間かかります。

### <a name="release-status"></a>リリースの状態
2021 年 10 月 13 日: ダウンロードと自動アップグレード向けにリリース済み。

### <a name="bug-fixes"></a>バグの修正
- 以前の Windows OS バージョン 2008 または 2008 R2 で実行されている AADConnect サーバーの自動アップグレード プロセスでアップグレードが失敗するバグを修正しました。 これらのバージョンの Windows Server はサポートされなくなりました。 このリリースでは、Windows Server 2012 以降を実行しているコンピューターの自動アップグレードのみが試行されます。
- 特定の条件下で、アクセス違反例外が原因で miisserver がクラッシュする問題を修正しました。

## <a name="20280"></a>2.0.28.0

>[!NOTE] 
> これは Azure AD Connect のメンテナンス更新プログラムのリリースです。 このリリースには Windows Server 2016 以降が必要です。

### <a name="release-status"></a>リリースの状態
2021 年 9 月 30 日: ダウンロード専用にリリース。自動アップグレードには使用できません。

### <a name="bug-fixes"></a>バグの修正
 - ウィザードの [Group Writeback Permissions]\(グループの書き戻しアクセス許可\) ページで、PowerShell スクリプトのダウンロード ボタンを削除し、PowerShell スクリプトが掲載されているオンライン記事にリンクされている詳細情報リンクを含めるようにウィザード ページのテキストを変更しました。

 - サーバー上の .NET バージョンが 4.6 以降の場合に、レジストリ キーがないためにウィザードによって誤ってインストールがブロックされていたバグを修正しました。 これらのレジストリ キーは必須ではなく、意図的に false に設定されている場合にのみインストールがブロックされます。

- 同期手順の初期化中にファントム オブジェクトが見つかった場合に、エラーがスローされるバグを修正しました。 これにより、同期手順がブロックされたり、一時的なオブジェクトが削除されたりします。 ファントム オブジェクトは無視されるようになりました。
注: ファントム オブジェクトとは、実際には存在しない、またはまだ認識されていないオブジェクトのプレースホルダーです。たとえば、存在しないターゲット オブジェクトへの参照がソース オブジェクトにある場合、ターゲット オブジェクトをファントムとして作成します。

### <a name="functional-changes"></a>機能の変更点

 - 使用中であっても、ユーザーが包含リストからオブジェクトと属性の選択を解除できるように変更されました。 これはブロックされるのではなく、警告が生成されるようになりました。 

## <a name="16142"></a>1.6.14.2
>[!NOTE] 
>これは Azure AD Connect の修正プログラムのリリースです。 このバージョンは、古いバージョンの Windows Server を実行していて、この時点でサーバーを Windows Server 2016 以降にアップグレードできないお客様が使用することを目的としています。 このバージョンを使用して Azure AD Connect V2.0 サーバーを更新することはできません。
>このバージョンのダウンロードが可能になると、対象となるテナントの自動アップグレードが開始されます。自動アップグレードが完了するまで、数週間かかります。

### <a name="release-status"></a>リリースの状態
2021 年 9 月 21 日: ダウンロードと自動アップグレード向けにリリース済み。

### <a name="functional-changes"></a>機能の変更点
 - 最新バージョンの MIM コネクタ (1.1.1610.0) を追加しました。 詳細な情報は、[MiM コネクタのリリース履歴ページ](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#1116100-september-2021)に記載されています。
 - Azure AD Connect でソフト マッチング機能を無効にする構成オプションを追加しました。 Microsoft では、クラウド専用アカウントを引き継ぐ目的で必要としない限り、ソフト マッチングは無効にすることをお客様に提案しています。 この[記事](/powershell/module/msonline/set-msoldirsyncfeature#example-2--block-soft-matching-for-the-tenant)では、ソフト マッチングを無効にする方法を紹介します。

### <a name="bug-fixes"></a>バグの修正
 - 以前のバージョンからのアップグレード後に DesktopSSO 設定が保持されないバグを修正しました。
 - Set-ADSync\*Permission コマンドレットが失敗する原因となるバグを修正しました。

## <a name="20251"></a>2.0.25.1

>[!NOTE] 
> これは Azure AD Connect の修正プログラムのリリースです。 このリリースには Windows Server 2016 以降が必要で、Azure AD Connect のバージョン 2.0 に存在するセキュリティの問題が修正されています。また、その他いくつかのバグ修正が含まれます。

### <a name="release-status"></a>リリースの状態
2021 年 9 月 14 日: ダウンロード専用にリリース。自動アップグレードには使用できません。

### <a name="bug-fixes"></a>バグの修正

 - Azure AD Connect サービスを指し示すために引用符で囲まれていないパスが使用された場合のセキュリティ上の問題を修正しました。 このパスは、現在では引用符で囲まれたパスです。
 - 既存の AD コネクタ アカウントの使用時に書き戻しが有効になっている場合のインポート構成の問題を修正しています。
 - Set-ADSyncExchangeHybridPermissions およびその他の関連コマンドレットの問題を修正しました。これらは無効な継承型のために 1.6 から破損しました。
 - TLS バージョンを設定するために以前のリリースで公開したコマンドレットには、キーが上書きされて、その中にあったすべての値が破棄されるという問題がありました。 キーがまだ存在しない場合にのみ新しいキーを作成することで、これを修正しました。 また、TLS レジストリの変更が Azure AD Connect 専用ではなく、同じサーバー上の他のアプリケーションにも影響を及ぼす可能性があることをユーザーに知らせる警告も追加されます。
 - Windows Server 2016 以降を必須とするために、V2.0 の自動アップグレードを強制するためのチェックを追加しました。
 - Set-ADSyncBasicReadPermissions コマンドレットに "ディレクトリの変更のレプリケート" アクセス許可を追加しました。
 - UseExistingDatabase とインポート構成が一緒に使用されることを防ぐための変更を加えました。これらに、競合する構成設定が含まれる可能性があるためです。
 - アプリケーション管理者ロールを持つユーザーがアプリ プロキシ サービスの構成を変更できるように変更を加えました。
 - [設定のインポート/エクスポート] のラベルから "(プレビュー)" ラベルを削除しました。この機能は、これまでかなりの期間、一般提供されてきました。
 - まだ "会社の管理者" と呼ばれていたいくつかのラベルを変更します。現在は、ロール名として "グローバル管理者" を使用しています。
 - 要求変換規則を AAD サービス プリンシパルに追加するために、新しい AAD Kerberos PowerShell コマンドレット "\*-AADKerberosServer" を作成しました。

### <a name="functional-changes"></a>機能の変更点
 - 最新バージョンの MIM コネクタ (1.1.1610.0) を追加しました。 詳細な情報は、[MiM コネクタのリリース履歴ページ](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#1116100-september-2021)に記載されています。
 - Azure AD Connect でソフト マッチング機能を無効にする構成オプションを追加しました。 Microsoft では、クラウド専用アカウントを引き継ぐ目的で必要としない限り、ソフト マッチングは無効にすることをお客様に提案しています。 この[記事](/powershell/module/msonline/set-msoldirsyncfeature#example-2--block-soft-matching-for-the-tenant)では、ソフト マッチングを無効にする方法を紹介します。

## <a name="20100"></a>2.0.10.0

### <a name="release-status"></a>リリースの状態
2021 年 8 月 19 日: ダウンロード専用にリリース。自動アップグレードには使用できません。

>[!NOTE] 
>これは Azure AD Connect の修正プログラムのリリースです。 このリリースには Windows Server 2016 以降が必要です。 この修正プログラムは、バージョン 2.0 と、Azure AD Connect バージョン 1.6 の問題を修正します。 以前の Windows Server を使用して Azure AD Connect を実行している場合は、代わりに [1.6.13.0](#16130) ビルドをインストールする必要があります。

### <a name="release-status"></a>リリースの状態
2021 年 8 月 19 日: ダウンロード専用にリリース。自動アップグレードには使用できません。

### <a name="known-issues"></a>既知の問題
 - 特定の状況において、このバージョンのインストーラーに、TLS 1.2 が有効になっていないことを伝えるエラーが表示され、インストールが停止されます。 これは、TLS 1.2 のレジストリ設定を検証するコードのエラーが原因となっています。 これは将来のリリースで修正する予定です。 この問題が発生したお客様は、記事「[Azure AD Connect に対する TLS 1.2 の強制](reference-connect-tls-enforcement.md)」に記載されている、TLS 1.2 を有効にする手順に従う必要があります。

### <a name="bug-fixes"></a>バグの修正

 - ドメインの名前が変更された場合、イベント ログに「指定されたキャストが無効です」というエラーが表示され、パスワード ハッシュ同期が失敗するバグを修正しました。 これは、以前のビルドからの回帰です。

## <a name="16130"></a>1.6.13.0
>[!NOTE] 
>これは Azure AD Connect の修正プログラムのリリースです。 このリリースは、Windows Server 2012 または 2012 R2 を搭載したサーバーで Azure AD Connect を実行しているお客様を対象としています。

2021 年 8 月 19 日: ダウンロード専用にリリース。自動アップグレードには使用できません。

### <a name="bug-fixes"></a>バグの修正

 - ドメインの名前が変更された場合、イベント ログに「指定されたキャストが無効です」というエラーが表示され、パスワード ハッシュ同期が失敗するバグを修正しました。 これは、以前のビルドからの回帰です。

### <a name="functional-changes"></a>機能の変更点
このリリースでは、機能上の変更はありません

## <a name="2090"></a>2.0.9.0

### <a name="release-status"></a>リリースの状態
2021 年 8 月 17 日: ダウンロード専用にリリース。自動アップグレードには使用できません。

### <a name="bug-fixes"></a>バグの修正
>[!NOTE] 
>これは Azure AD Connect の修正プログラムのリリースです。 このリリースには Windows Server 2016 以降が必要です。 このリリースでは、バージョン 2.0.8.0 に存在する問題に対処しています。この問題は Azure AD Connect バージョン 1.6 には存在しません

 - 多数のパスワード ハッシュの同期トランザクションを同期するときに、イベント ログ エントリの長さがパスワード ハッシュの同期イベント エントリで許容される最大長を超える可能性があるというバグを修正しました。 長いログ エントリを複数のエントリに分割するようにしました。

## <a name="2080"></a>2.0.8.0
>[!NOTE] 
>これは Azure AD Connect のセキュリティ更新プログラム リリースです。 このリリースには Windows Server 2016 以降が必要です。 以前のバージョンの Windows Server を使用している場合は、[バージョン 1.6.11.3](#16113) を使用してください。
>このリリースで[こちらの CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949) に記載されている脆弱性に対処します。 この脆弱性に関する詳細については、CVE を参照してください。
>Azure AD Connect 2.0 の最新バージョンは、[こちらのリンク](https://www.microsoft.com/en-us/download/details.aspx?id=47594)からダウンロードできます。

### <a name="release-status"></a>リリースの状態
2021 年 8 月 10 日: ダウンロード専用にリリース。自動アップグレードには使用できません。 

### <a name="functional-changes"></a>機能の変更点
このリリースでは、機能上の変更はありません

## <a name="16113"></a>1.6.11.3 
>[!NOTE] 
>これは Azure AD Connect のセキュリティ更新プログラム リリースです。 このバージョンは、Windows Server の古いバージョンを実行していて、この時点でサーバーを Windows Server 2016 以降にアップグレードできないお客様が使用することを目的としています。 このバージョンを使用して Azure AD Connect V2.0 サーバーを更新することはできません。
>このリリースで[こちらの CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949) に記載されている脆弱性に対処します。 この脆弱性に関する詳細については、CVE を参照してください。
>Azure AD Connect 1.6 の最新バージョンは、[こちらのリンク](https://www.microsoft.com/download/details.aspx?id=103336)からダウンロードできます。

### <a name="release-status"></a>リリースの状態
2021 年 8 月 10 日: ダウンロード専用にリリース。自動アップグレードには使用できません。

### <a name="functional-changes"></a>機能の変更点
このリリースでは、機能上の変更はありません

## <a name="2030"></a>2.0.3.0
>[!NOTE] 
>これは Azure AD Connect のメジャー リリースです。 詳細については、[Azure Active Directory V2.0 の記事](whatis-azure-ad-connect-v2.md)を参照してください。

### <a name="release-status"></a>リリースの状態
2021 年 7 月 20 日: ダウンロード専用にリリース。自動アップグレードには使用できません
### <a name="functional-changes"></a>機能の変更点
 - SQL Server の LocalDB コンポーネントを SQL 2019 にアップグレードしました。 
 - SQL Server 2019 の要件により、このリリースには Windows Server 2016 以降が必要です。 Azure AD Connect サーバー上の Windows Server のインプレース アップグレードはサポートされていないため、[スイング移行](how-to-upgrade-previous-version.md#swing-migration)の使用が必要になる場合があることに注意してください。
 -    このリリースでは、TLS 1.2 の使用を適用しています。 お使いの Windows Server で TLS 1.2 を有効にしている場合は、AADConnect でこのプロトコルが使用されます。 サーバーで TLS 1.2 が有効になっていない場合、AADConnect をインストールしようとするとエラー メッセージが表示され、TLS 1.2 を有効にするまでインストールは続行されません。 お使いのサーバーで新しい "Set-ADSyncToolsTls12" コマンドレットを使用して TLS 1.2 を有効にできることに注意してください。
 -    このリリースでは、Azure AD Connect をインストールするときに、"ハイブリッド ID の管理者" のユーザー ロールを持つユーザーを使用して認証を行うことができます。 これには、全体管理者ロールは不要になりました。
 - SQL Server 2019 の前提条件として、Visual C++ ランタイム ライブラリをバージョン 14 にアップグレードしました    
 -    このリリースでは、認証に MSAL ライブラリを使用しており、2022 年に廃止予定の古い ADAL ライブラリを削除しました。    
 -    Windows セキュリティ ガイダンスに従って、AdminSDHolders に対するアクセス許可が適用されなくなりました。 ADSyncConfig.psm1 モジュールのパラメーター "SkipAdminSdHolders" を "IncludeAdminSdHolders" に変更しました。
 -    パスワードは、それ自体が変更されたかどうかに関係なく、期限切れのパスワードの "有効期限が切れていない" ときに、再評価されるようになりました。 ユーザーのパスワードが [ユーザーは次回ログオン時にパスワードの変更が必要] に設定されていて、このフラグがオフになっている (つまり、パスワードの "有効期限が切れていない") 場合は、"有効期限が切れていない" 状態とパスワード ハッシュが Azure AD に同期され、ユーザーが Azure AD にサインインしようとしたときに、有効期限が切れていないパスワードを使用できます。
期限切れのパスワードを Active Directory から Azure Active Directory に同期するには、Azure AD Connect の[一時パスワードの同期](how-to-connect-password-hash-synchronization.md#synchronizing-temporary-passwords-and-force-password-change-on-next-logon)機能を使用してください。 この機能を使用するには、パスワード ライトバックを有効にして、更新プログラムに使用するパスワードも Active Directory に書き戻されるようにする必要があることに注意してください。
 - Windows Server から TLS 1.2 の設定を有効化または取得するために、ADSyncTools モジュールに次の 2 つの新しいコマンドレットを追加しました。 
   - Get-ADSyncToolsTls12
   - Set-ADSyncToolsTls12
   
これらのコマンドレットを使用して、TLS 1.2 の有効化ステータスを取得したり、必要に応じて設定したりできます。 インストールまたは AADConnect を正常に行うには、サーバーで TLS 1.2 を有効にする必要があることに注意してください。

 -    いくつかの新しいおよび改良されたコマンドレットにより、ADSyncTools を改良しました。 [ADSyncTools の記事](reference-connect-adsynctools.md)には、これらのコマンドレットの詳細が記載されています。 
  追加または更新されたコマンドレットは次のとおりです 
    - Clear-ADSyncToolsMsDsConsistencyGuid    
    - ConvertFrom-ADSyncToolsAadDistinguishedName    
    - ConvertFrom-ADSyncToolsImmutableID    
    - ConvertTo-ADSyncToolsAadDistinguishedName    
    - ConvertTo-ADSyncToolsCloudAnchor    
    - ConvertTo-ADSyncToolsImmutableID    
    - Export-ADSyncToolsAadDisconnectors    
    - Export-ADSyncToolsObjects    
    - Export-ADSyncToolsRunHistory    
    - Get-ADSyncToolsAadObject    
    - Get-ADSyncToolsMsDsConsistencyGuid    
    - Import-ADSyncToolsObjects    
    - Import-ADSyncToolsRunHistory    
    - Remove-ADSyncToolsAadObject    
    - Search-ADSyncToolsADobject    
    - Set-ADSyncToolsMsDsConsistencyGuid    
    - Trace-ADSyncToolsADImport    
    - Trace-ADSyncToolsLdapQuery    
-    インポートとエクスポートに V2 エンドポイントを使用し、Get-ADSyncAADConnectorExportApiVersion コマンドレットの問題を修正しました。 V2 エンドポイントの詳細については、[Azure AD Connect 同期 V2 エンドポイントの記事](how-to-connect-sync-endpoint-api-v2.md)を参照してください。
-    オンプレミスの AD から Azur AD に同期するために、次の新しいユーザー プロパティを追加しました    
    - employeeType    
    - employeeHireDate    
-    このリリースでは、Windows Server に PowerShell バージョン 5.0 以降がインストールされている必要があります。 このバージョンは Windows Server 2016 以降のバージョンの一部であることに注意してください。    
-    新しい V2 エンドポイントにより、グループ同期メンバーシップの制限を 250,000 に引き上げました。
-    Generic LDAP コネクタと Generic SQL コネクタを最新バージョンに更新しました。 これらのコネクタの詳細については、こちらをご覧ください。
    - [Generic LDAP コネクタ リファレンス ドキュメント](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap)
    - [Generic SQL コネクタ リファレンス ドキュメント](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql)
-    M365 管理センターでは、Azure AD へのエクスポート アクティビティがあるたびに、AADConnect クライアントのバージョンを報告するようになりました。 これにより、M365 管理センターには常に最新の AADConnect クライアント バージョンが確保されるため、期限切れのバージョンを使用している場合は検出できるようになります

### <a name="bug-fixes"></a>バグの修正
- スクリーン リーダーが [詳細情報] リンクの間違ったロールをアナウンスするアクセシビリティのバグを修正しました。
-    優先順位の大きな値 (387163089 など) の同期規則によってアップグレードが失敗するバグを修正しました。 値をインクリメントする前に、優先順位番号を整数としてキャストするようにストアド プロシージャ 'mms_UpdateSyncRulePrecedence' を更新しました。
-    グループの書き戻しの構成がインポートされた場合に、グループの書き戻しアクセス許可が同期アカウントに設定されないバグを修正しました。 インポートされた構成でグループの書き戻しが有効になっている場合に、グループの書き戻しアクセス許可が設定されるようにしました。
-    インストール エラーを修正するために、Azure AD Connect Health エージェントのバージョンを 3.1.110.0 に更新しました。
-    ディレクトリ拡張属性が構成されているエクスポートされた構成からの既定以外の属性で問題が発生しています。 これらの構成を新しいサーバーまたはインストールにインポートすると、対象属性リストはディレクトリ拡張構成ステップによってオーバーライドされるため、インポート後は、既定およびディレクトリ拡張の属性のみが Sync Service Manager で選択されます (既定以外の属性はインストールには含まれていないため、インポートした同期規則を機能させる場合は、ユーザーが Sync Service Manager から手動でこれらの属性を再度有効にする必要があります)。 対象属性リストから既存の属性を保持するようにディレクトリ拡張を構成する前に、AAD コネクタを更新するようにしました。
-    ページ ヘッダーのフォントの太さが "細い" に設定されるアクセシビリティの問題を修正しました。 ページ タイトルのフォントの太さが "太字" に設定され、すべてのページのヘッダーに適用されるようになりました。
-    ADSyncSingleObjectSync.ps1 の関数 Get-AdObject は、AD コマンドレットとのあいまいさを防ぐために、Get-AdDirectoryObject に名前変更されました。
-    SQL 関数 'mms_CheckSynchronizationRuleHasUniquePrecedence' では、異なるコネクタの送信同期規則で重複する優先順位が許可されています。 重複する規則の優先順位を許可する条件を削除しました。
-    属性フロー データが null の場合 (削除操作をエクスポートする場合) に、単一オブジェクト同期コマンドレットが失敗するバグを修正しました。    
-    ADSync ブートストラップ サービスを開始できないためにインストールが失敗するバグを修正しました。 ブートストラップ サービスを開始する前に、ローカルの Builtin ユーザー グループに同期サービス アカウントを追加しました。
-    AAD Connect ウィザードのアクティブなタブが、ハイ コントラスト テーマで正しい色で表示されないアクセシビリティの問題を修正しました。 選択した色コードは、通常の色のコード構成で条件が不足していたため上書きされました。
-    ユーザーが UI と PowerShell を使用して同期規則で使用されるオブジェクトと属性の選択を解除できる問題に対処しました。 任意の同期規則で使用されている属性またはオブジェクトの選択を解除しようとすると、わかりやすいエラー メッセージが表示されるようになりました。
-    古いバージョンの Azure AD Connect でスクリプトを実行した場合の下位互換性の問題を確認して修正するために、"設定の移行コード" にいくつかの更新を加えました。    
-    PHS が不完全なオブジェクトの検索を試みる際に、パスワードのフェッチに最初に使用したのと同じアルゴリズムを使用して DC を解決しないバグを修正しました。 特に、アフィニティ化された DC 情報が無視されます。 不完全なオブジェクトの検索では、両方のインスタンスで DC を見つけるために同じロジックを使用する必要があります。
-    AAD Connect クライアント ID に基づく Microsoft Graph の直接呼び出しに関するアクセス許可の問題により、AADConnect が Microsoft Graph を使用してアプリケーション プロキシ項目を読み取れないバグを修正しました。これを修正するために、Microsoft Graph への依存関係を削除し、代わりに AAD PowerShell を使用してアプリ プロキシ アプリケーション オブジェクトを操作します。
-    'Out to AD - Group SOAInAAD Exchange' 同期規則から書き戻しメンバーの制限を削除しました。    
-    コネクタ アカウントのアクセス許可を変更するときに、最後の差分インポート以降に変更されていないスコープ内にオブジェクトが含まれていると、差分インポートでそれがインポートされないバグを修正しました。 ユーザーにこの問題の警告アラートが表示されるようになりました。
-    スクリーン リーダーでラジオ ボタンの位置が読み上げられないアクセシビリティの問題を修正しました。 ラジオ ボタンのアクセシビリティ テキスト フィールドに、位置テキストを追加しました。
-    パススルー認証エージェント バンドルを更新しました。 古いバンドルには、US Gov での HIP のファースト パーティ アプリケーションの正しい応答 URL がありませんでした。    
-    既存のデータベースを使用 (既定では DirSyncWebServices API V2 を使用) して AADConnect バージョン 1.6.X.X をクリーン インストールすると、AAD コネクタのエクスポートに 'stopped-extension-dll-exception' があるバグを修正しました。    以前は、v2 へのエクスポート バージョンの設定はアップグレードのためにのみ行われていましたが、クリーン インストールでも設定されるように変更しました。
-    "ADSyncPrep.psm1" モジュールは使用されなくなり、インストールから削除されます。

### <a name="known-issues"></a>既知の問題
-    AADConnect ウィザードで、[同期設定をインポート] オプションが "プレビュー" として表示されていますが、この機能は一般公開されています。
-    一部の Active Directory コネクタは、移行設定スクリプトの出力を使用して製品をインストールすると、異なる順序でインストールされる場合があります。 
-    Azure AD Connect ウィザードの [ユーザー サインイン オプション] ページに、"社内管理者" と記載されています。 この用語は使用されなくなったため、"全体管理者" に置き換える必要があります。
-    サインイン オプションが PingFederate を使用するように構成されている場合、[設定のエクスポート] オプションは破損します。
-    Azure AD Connect は、ハイブリッド ID の管理者ロールを使用してデプロイできるようになりましたが、セルフサービス パスワード リセットを構成するには、引き続きユーザーに全体管理者ロールが必要です。
-    デプロイ中に元の AADConnect 構成とは別のテナントに接続するために AADConnect 構成をインポートすると、ディレクトリ拡張属性が正しく構成されません。

## <a name="1640"></a>1.6.4.0

>[!NOTE]
> こちらの Azure 環境で Azure AD Connect 同期 V2 エンドポイント API を使用できるようになりました。
> - Azure Commercial
> - Azure China Cloud
> - Azure US Government クラウド
> - このリリースは、Azure German Cloud では利用できません

### <a name="release-status"></a>リリースの状態
3/31/2021: ダウンロード専用にリリース。自動アップグレードには使用できません

### <a name="bug-fixes"></a>バグの修正
- このリリースでは、バージョン 1.6.2.4 のバグが修正されています。このリリースにアップグレードした後に、Azure AD Connect Health 機能が正しく登録されず、機能しませんでした。 ビルド 1.6.2.4 をデプロイしたお客様には、このビルドを使用して Azure AD Connect サーバーを更新することが要求されます。これにより、Health 機能が正しく登録されます。 

## <a name="1624"></a>1.6.2.4
>[!IMPORTANT]
> 2021 年 3 月 30 日の更新: このビルドで問題が検出されました。 このビルドをインストールすると、Health サービスが登録されません。 このビルドをインストールしないことをお勧めします。 修正プログラムが間もなくリリースされる予定です。
> このビルドが既にインストールされている場合は、[こちらの記事](./how-to-connect-health-agent-install.md#manually-register-azure-ad-connect-health-for-sync)に示されているように、コマンドレットを使用して手動で Health サービスを登録できます

>[!NOTE]
> - このリリースはダウンロードでのみ提供されます。
> - 同期規則の変更のため、このリリースにアップグレードする場合は完全な同期が必要になります。
> - このリリースでは、AADConnect サーバーは既定で、新しい V2 エンド ポイントに設定されます。 このエンド ポイントは、ドイツ国内クラウドではサポートされていないことに注意してください。この環境にこのバージョンをデプロイする必要がある場合は、[こちらの手順](./how-to-connect-sync-endpoint-api-v2.md#rollback)に従って V1 エンド ポイントに戻す必要があります。 そのようにしないと、同期でエラーが発生します。

### <a name="release-status"></a>リリースの状態
3/19/2021: ダウンロード用にリリース。自動アップグレードには使用できません

### <a name="functional-changes"></a>機能の変更点

 - 書き戻されるグループ内のメンバーシップ数を 5 万に制限するように、既定の同期規則が更新されました。
   - グループの書き戻しにおけるメンバーシップ数の制限 (Out to AD - Group Writeback Member Limit) と、Azure Active Directory グループへのグループの同期の制限 (Out to AAD - Group Writeup Member Limit) のため、新しい既定の同期規則が追加されました。
   - 書き戻しグループ内のメンバー数を 5 万に制限するため、"Out to AD - Group SOAInAAD - Exchange" ルールにメンバー属性が追加されました
 - "In from AAD - Group SOAInAAD" 規則がクローンされ、AADConnect がアップグレードされた場合に Group Writeback v2 をサポートするため、同期規則が更新されました。
     - 更新された規則は既定で無効になるため、targetWritebackType は null になります。
     - AADConnect では (書き戻しが有効になっている Azure Active Directory セキュリティ グループを含め)、すべてのクラウド グループを配布グループとして書き戻します。
   -"Out to AD - Group SOAInAAD" 規則がクローンされ、AADConnect がアップグレードされた場合。
     - 更新された規則は既定で無効になります。 ただし、追加された新しい同期規則 "Out to AD - Group SOAInAAD - Exchange" が有効になります。
     - Cloned Custom Sync Rule の優先順位に応じて、AADConnect により、Mail 属性と Exchange 属性が流されます。
     - 何らかの Mail 属性や Exchange 属性が Cloned Custom Sync Rule によって流されない場合、それらの属性は新しい Exchange Sync Rule によって追加されます。
     - Group Writeback V2 は、現時点ではプライベート プレビューであり、一般公開されていない点に注意してください。
 - [選択的なパスワード ハッシュ同期](./how-to-connect-selective-password-hash-synchronization.md)のサポートが追加されました
 - 新しい[単一オブジェクト同期コマンドレット](./how-to-connect-single-object-sync.md)が追加されました。 このコマンドレットは、Azure AD Connect の同期の構成をトラブルシューティングするために使用します。 
 -  Azure AD Connect では、サービスを構成するための、ハイブリッド ID の管理者の役割をサポートするようになりました。
 - AADConnectHealth エージェントが 3.1.83.0 に更新されました
 - 新しいバージョンの [ADSyncTools PowerShell モジュール](./reference-connect-adsynctools.md)。これには、新しいコマンドレットや改良されたコマンドレットがいくつかあります。 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - トークン取得の失敗に関するエラーのログ記録が更新されました。
 - リンクされている情報の詳細を表示するための、構成ページの [詳細情報] リンクが更新されました。
 - 以前の同期 UI の [CS 検索] ページから、[明示] 列が削除されました
 - 以前の手順で資格情報がまだ指定されていない場合に、ユーザーに資格情報の入力を求めたり、ADSyncConfig モジュールを使用して独自のアクセス許可を構成したりするため、グループ書き戻しフローに追加の UI が設けられました。
 - DC 上の ADSync サービス アカウント用の MSA を自動作成します。 
 - 既存のコマンドレットで Azure Active Directory DirSync 機能の Group Writeback V2 を設定し、取得する機能が追加されました。
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - AWS API バージョンを読み取るコマンドレットが 2 つ追加されました
    - Get-ADSyncAADConnectorImportApiVersion - AWS API の get import のバージョンを取得します
    - Get-ADSyncAADConnectorExportApiVersion - AWS API の get export のバージョンを取得します

 - サービスでの変更に関するトラブルシューティングを支援するため、同期規則に加えられた変更が追跡されるようになりました。 コマンドレット "Get-ADSyncRuleAudit" を使用して、追跡された変更を取得します。
 - ADSyncAdmin グループのユーザーが AD DS コネクタ アカウントを変更できるように、[ADSyncConfig PowerShell モジュール](./how-to-connect-configure-ad-ds-connector-account.md#using-the-adsyncconfig-powershell-module)の Add-ADSyncADDSConnectorAccount が更新されました。 

### <a name="bug-fixes"></a>バグの修正
 - 白い背景での明るさの要件を満たすため、無効にされる前景色が更新されました。 明るさの要件を満たすため、無効にされているページが選択されたときは前景のテキストの色を白に設定する、ナビゲーション ツリーでの追加の条件が用意されました。
 - Set-ADSyncPasswordHashSyncPermissions コマンドレットの粒度が向上しています。省略可能な "ADobjectDN" パラメーターを含めるため、PHS アクセス許可スクリプト (Set-ADSyncPasswordHashSyncPermissions) が更新されています。 
 - アクセシビリティ バグの修正。 スクリーン リーダーでは、フォレストの一覧を保持する UX 要素を、"**フォレストの一覧の一覧**" ではなく "**フォレストの一覧**" として記述するようになりました。
 - Azure AD Connect ウィザードの一部の項目について、スクリーン リーダーの出力が更新されました。 コントラストの要件を満たすため、ボタンをポイントしたときの色が更新されました。 コントラストの要件を満たすため、Sychronization Service Manager のタイトル色が更新されました。
 - カスタム拡張属性を持つエクスポートされた構成から AADConnect をインストールする場合の問題を修正しました。同期規則の適用中に、ターゲット スキーマで拡張属性の確認をスキップするための条件が追加されました。
 - グループ書き戻し機能が有効になっている場合は、インストール時に適切なアクセス許可が追加されます。
 - インポート時に、重複している既定の同期規則の優先順位が修正されます
 - 正常性ポータルで修正されたオブジェクトが競合するとき、V2 API 差分インポート中、ステージング エラーが発生した問題を解決しました。
 - CS オブジェクトのリンク状態の一貫性が失われる原因となった同期エンジンの問題が修正されています
 - Get-ADSyncConnectorStatistics 出力にインポート カウンターが追加されました。
 - pass2 ウィザード中のいくつかのまれなケースで発生する、到達できないドメインの選択解除 (以前に選択済み) に関する問題が修正されています。
 - カスタム規則の優先順位が重複している場合はポリシーのインポートとエクスポートが失敗するように変更されています 
 - ドメイン選択ロジックのバグが修正されています。
 - ソース アンカーとして mS-DS-ConsistencyGuid を使用し、In from AD - Group Join 規則をクローンした場合にビルド 1.5.18.0 で起きる問題を修正します。
 - AADConnect を新規インストールすると、使用可能なものが存在しており、別のものが渡されていない場合は、クラウドに格納されているエクスポート削除のしきい値が使用されます。
 - AADConnect で、ハイブリッド参加デバイスの AD displayName の変更が読み取られない問題を修正しました

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>リリースの状態
2020 年 7 月 29 日ダウンロード対象としてリリース済み

### <a name="functional-changes"></a>機能の変更点
これはバグ修正プログラムのリリースです。 このリリースでは、機能上の変更はありません。

### <a name="fixed-issues"></a>修正する問題

- AZUREADSSOACC コンピューター アカウントが "" に既に存在する場合、管理者が "シームレス シングル サインオン" を有効にできない問題を解決しました。
- 正常性ポータルで修正されたオブジェクトが競合するとき、V2 API 差分インポート中、ステージング エラーが発生した問題を解決しました。
- 無効にしたカスタム ルールが有効としてインポートされるという、インポートおよびエクスポート構成の問題を解決しました。

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>リリースの状態
07/10/2020:ダウンロード対象としてリリース済み

### <a name="functional-changes"></a>機能の変更点
このリリースには、既存の Azure AD Connect サーバーの構成を .JSON ファイルにエクスポートする機能のパブリック プレビューが含まれています。ファイルはその後、新しい Azure AD Connect サーバーをインストールして元のサーバーのコピーを作成するときに使用できます。

この新機能の詳細については、[この記事](./how-to-connect-import-export-config.md)を参照してください。

### <a name="fixed-issues"></a>修正された問題
- アップグレード中、ローカライズされたビルドのローカル DB サイズに関する警告が誤って発生するバグを修正しました。
- アカウント名とドメイン名の入れ替えで、アプリ イベントに誤ってエラーが発生するバグを修正しました。
- DC に Azure AD Connect をインストールできず、"メンバーが見つかりませんでした" というエラーが表示されるエラーを修正しました。


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>リリースの状態
2020 年 5 月 7 日:ダウンロード対象としてリリース済み

### <a name="fixed-issues"></a>修正された問題
この修正プログラムのビルドでは、孫コンテナーのみが選択されている場合に、選択されていないドメインがウィザードの UI から誤って選択されるという問題が修正されます。


>[!NOTE]
>このバージョンには、新しい Azure AD Connect 同期 V2 エンドポイント API が含まれています。  現在、この新しい V2 エンドポイントはパブリック プレビュー段階にあります。  新しい V2 エンドポイント API を使用するには、このバージョン以降が必要です。  ただし、このバージョンをインストールするだけでは、V2 エンドポイントは有効になりません。 V2 エンドポイントを有効にしない限り、V1 エンドポイントが引き続き使用されます。  有効にしてパブリック プレビューにオプトインするには、[「Azure AD Connect 同期 V2 エンドポイント API (パブリック プレビュー)」](how-to-connect-sync-endpoint-api-v2.md)の手順に従う必要があります。  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>リリースの状態
2020 年 4 月 23 日:ダウンロード対象としてリリース済み

### <a name="fixed-issues"></a>修正された問題
この修正プログラム ビルドでは、MFA を使用するテナント管理者が DSSO を有効にできなかった、ビルド 1.5.20.0 で発生した問題を修正しました。

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>リリースの状態
2020 年 4 月 20 日:ダウンロード対象としてリリース済み

### <a name="fixed-issues"></a>修正された問題
この修正プログラム ビルドでは、 **[In from AD - Group Join]\(AD からの受信 - グループ結合\)** 規則を複製し、 **[In from AD - Group Common]\(AD からの受信 - グループ共通\)** 規則を複製していない場合のビルド 1.5.20.0 の問題を修正します。

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>リリースの状態
2020 年 4 月 9 日ダウンロード対象としてリリース済み

### <a name="fixed-issues"></a>修正された問題
- この修正プログラム ビルドでは、グループ フィルタリング機能を有効にし、ソース アンカーとして mS-DS-ConsistencyGuid を使用している場合、ビルド 1.5.18.0 の問題が修正されます。
- すべての Set-ADSync* Permissions コマンドレットで使用される DSACLS コマンドを呼び出すと、次のいずれかのエラーが発生するという ADSyncConfig PowerShell モジュールの問題を修正しました。
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> **[In from AD - Group Join]\(AD からの受信 - グループ結合\)** 同期ルールを複製し、 **[In from AD - Group Common]\(AD からの受信 - グループ共通\)** 同期ルールを複製しておらず、アップグレードを計画している場合は、アップグレードの一環として次の手順を実行します。
> 1. アップグレード中は、 **[構成が完了したら、同期プロセスを開始する]** オプションをオフにします。
> 2. 複製された結合同期規則を編集し、次の 2 つの変換を追加します。
>     - ダイレクト フロー `objectGUID` を `sourceAnchorBinary` に設定します。
>     - 式フロー `ConvertToBase64([objectGUID])` を `sourceAnchor` に設定します。     
> 3. `Set-ADSyncScheduler -SyncCycleEnabled $true` を使用してスケジューラを有効にします。



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>リリースの状態
2020 年 4 月 2 日ダウンロード対象としてリリース済み

### <a name="functional-changes-adsyncautoupgrade"></a>機能変更 ADSyncAutoUpgrade 

- グループ オブジェクトの mS-DS-ConsistencyGuid 機能のサポートが追加されました。 これにより、グループをフォレスト間で移動したり、AD 内のグループを AD グループの objectID が変更された Azure AD に再接続したりすることができます。たとえば、AD サーバーが災害の後に再構築されたときなどです。 詳細については、[フォレスト間でのグループの移動](how-to-connect-migrate-groups.md)に関する記事を参照してください。
- mS-DS-ConsistencyGuid 属性は、同期されたすべてのグループに対して自動的に設定されるため、この機能を有効にするために何もする必要はありません。 
- Get-ADSyncRunProfile は使用されなくなったため、削除されました。 
- AD DS コネクタ アカウントにエンタープライズ管理者またはドメイン管理者アカウントを使用しようとしたときに表示される警告を変更し、より多くのコンテキストを提供します。 
- コネクタ スペースからオブジェクトを削除する新しいコマンドレットを追加しました。古い CSDelete.exe ツールは削除され、新しい Remove-ADSyncCSObject コマンドレットに置き換えられています。 Remove-ADSyncCSObject コマンドレットは、入力として CsObject を受け取ります。 このオブジェクトは、Get-ADSyncCSObject コマンドレットを使用して取得できます。

>[!NOTE]
>以前の CSDelete.exe ツールは削除され、新しい Remove-ADSyncCSObject コマンドレットに置き換えられました。 

### <a name="fixed-issues"></a>修正された問題

- 機能を無効にした後に Azure AD Connect ウィザードを再実行するときの、グループ書き戻しの forest/OU セレクターのバグを修正しました。 
- 必要な DCOM レジストリ値が見つからない場合に新しいヘルプ リンクと共に表示される、新しいエラーページが導入されました。 情報はログ ファイルにも書き込まれます。 
- 使用する前に Azure Active Directory 同期アカウントがすべてのサービス レプリカに伝達されていないために、ディレクトリ拡張または PHS を有効にできないことがあるという、アカウントの作成に関する問題を修正しました。 
- 同期エラーの圧縮ユーティリティにおいて、サロゲート文字が正しく処理されないバグを修正しました。 
- サーバーがスケジューラの中断状態のままになる自動アップグレードのバグを修正しました。 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>リリースの状態
2019 年 12 月 9 日:ダウンロード向けリリース。 自動アップグレードでは使用できません。
### <a name="new-features-and-improvements"></a>新機能と機能強化
- Kerberos ハッシュのパディングが正しく考慮されるように、Azure AD Domain Services のパスワード ハッシュ同期を更新しました。  これにより、Azure AD から Azure AD Domain Services へのパスワード同期中のパフォーマンスが向上します。
- 認証エージェントとサービス バスの間の信頼できるセッションのサポートを追加しました。
- 認証エージェントとクラウド サービス間に websocket 接続用の DNS キャッシュを追加しました。
- クラウドから特定のエージェントをターゲットにして、エージェントの接続をテストする機能を追加しました。

### <a name="fixed-issues"></a>修正された問題
- リリース 1.4.18.0 には、DSSO の PowerShell コマンドレットで、ps の実行中に指定される管理者資格情報ではなく、ログインの Windows 資格情報が使用されるバグがありました。 その結果、Azure AD Connect ユーザー インターフェイスを使用して複数のフォレストで DSSO を有効にすることができませんでした。 
- Azure AD Connect ユーザー インターフェイスを使用して、すべてのフォレストで同時に DSSO を有効にする修正が行われました。

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>リリースの状態
11/08/2019:ダウンロード対象としてリリース済み。 自動アップグレードでは使用できません。

>[!IMPORTANT]
>このリリースの Azure AD Connect では、内部的なスキーマ変更があるため、MSOnline PowerShell を使用して AD FS 信頼関係の構成設定を管理する場合は、MSOnline PowerShell モジュールをバージョン 1.1.183.57 以上に更新する必要があります

### <a name="fixed-issues"></a>修正された問題

このバージョンは、既存の Hybrid Azure AD 参加済みデバイスの問題を修正します。 このリリースには、この問題を修正する新しいデバイス同期規則が含まれています。
この規則の変更により、古いデバイスが Azure AD から削除される可能性があることに注意してください。 これらのデバイス オブジェクトは、条件付きアクセスの承認時に Azure AD によって使用されないため、それについて心配する必要はありません。 一部のお客様については、この規則の変更によって削除されるデバイスの数が、削除のしきい値を超える場合があります。 Azure AD でのデバイス オブジェクトの削除がエクスポート削除しきい値を超えていることが確認された場合は、これらの削除の実行を許可することをお勧めします。 [削除のしきい値を超えた場合に削除の実行を許可する方法](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>リリースの状態
9/28/2019:一部のテナントを対象に自動アップグレード用がリリース。 ダウンロードでは利用できません。

このバージョンでは、前のバージョンから 1.4.18.0 に自動アップグレードされたサーバーの一部で SSPR (セルフサービス パスワード リセット ) とパスワード ライトバックに問題を発生させたバグが修正されました。

### <a name="fixed-issues"></a>修正された問題

特定の状況下では、バージョン 1.4.18.0 に自動アップグレードされたサーバーで、アップグレードの完了後、セルフサービス パスワード リセット とパスワード ライトバックを再度有効にできませんでした。 この自動アップグレードでは、その問題が解決され、セルフサービス パスワード リセット とパスワード ライトバックが再度有効になります。

同期エラーの圧縮ユーティリティにおいて、サロゲート文字が正しく処理されないバグを修正しました。

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>このバージョンの Azure AD Connect にアップグレードした後、既存の Hybrid Azure AD 参加済みデバイスで一部のお客様に問題が発生しているインシデントを調査しています。 Hybrid Azure AD 参加をデプロイしたお客様は、これらの問題の根本原因が完全に認識され軽減されるまで、このバージョンへのアップグレードを延期することをお勧めします。 詳細については、できるだけ早く提供します。

>[!IMPORTANT]
>このバージョンの Azure AD Connect をご利用のお客様に、Windows デバイスの一部または全部が Azure AD から消えるという現象が発生することがあります。 これらのデバイス ID が、条件付きアクセスの承認時に Azure AD によって使用されることはないため、これについて心配する必要はありません。 詳細については、[Azure AD Connect 1.4.xx.x デバイスが消える現象](/troubleshoot/azure/active-directory/reference-connect-device-disappearance)に関するページを参照してください。


### <a name="release-status"></a>リリースの状態
9/25/2019:自動アップグレード向けにのみリリース済み。

### <a name="new-features-and-improvements"></a>新機能と機能強化
- 新しいトラブルシューティング ツールは、"ユーザーが同期していない"、"グループが同期していない"、"グループ メンバーが同期していない" という各シナリオのトラブルシューティングに役立ちます。
- Azure AD Connect トラブルシューティング スクリプトに、各国のクラウドのサポートが追加されました。
- MIIS_Service の非推奨の WMI エンドポイントが削除されたことを顧客に通知する必要があります。 今後、すべての WMI 操作は、PS コマンドレットを使用して実行する必要があります。
- AZUREADSSOACC オブジェクトの制約付き委任のリセットによるセキュリティ強化。
- 同期規則を追加または編集するときに、規則で使用されている属性で、コネクタ スキーマに含まれているがコネクタに追加されていないものがある場合、それらの属性はコネクタに自動的に追加されます。 規則が影響を与えるオブジェクトの種類についても同じことが当てはまります。 コネクタに何かが追加されると、コネクタは次の同期サイクルでフル インポートのマークが付けられます。
- 新しい Azure AD Connect のデプロイでは、エンタープライズ管理者またはドメイン管理者のコネクタ アカウントとしての使用がサポートされなくなりました。 エンタープライズまたはドメイン管理者をコネクタ アカウントとして使用する現在の Azure AD Connect デプロイは、このリリースによる影響を受けません。
- 同期マネージャーでは、規則の作成、編集、削除時に同期が実行されます。 フル インポートまたは完全同期が実行される場合は、規則の変更時にユーザーに通知するポップアップが表示されます。
- [コネクタ] > [プロパティ] > [接続] ページにパスワード エラーの軽減手順が追加されました。
- コネクタのプロパティ ページに、Sync Service Manager の非推奨警告が追加されました。 この警告は、ユーザーに対して変更は Azure AD Connect ウィザードを使用して行う必要があることを通知します。
- ユーザーのパスワード ポリシーに関する問題に対して新しいエラーが追加されました。
- ドメインおよび OU フィルターによるグループ フィルターの構成の誤りを防止します。 入力したグループのドメインまたは OU が既にフィルターで除外されている場合、グループ フィルターでエラーが表示され、その問題が解決されるまでユーザーが先に進まないように抑制されます。
- ユーザーは、Synchronization Service Manager UI で Active Directory Domain Services または Windows Azure Active Directory 用のコネクタを作成できなくなりました。
- Sychronization Service Manager のカスタム UI コントロールのアクセシビリティが修正されました
- Azure AD Connect のすべてのサインイン方法に対して 6 つのフェデレーション管理タスクが有効になりました。  (以前は、すべてのサインインで "AD FS TLS/SSL 証明書の更新" タスクのみ使用できました。)
- フェデレーションから PHS または PTA にサインイン方法を変更したときに表示する、すべての Azure AD ドメインとユーザーがマネージド認証に変換されるという警告が追加されました。
- "Azure AD と AD FS 信頼のリセット" タスクからトークン署名証明書が削除され、これらの証明書を更新するための別のサブタスクが追加されました。
- "証明書の管理" という新しいフェデレーション管理タスクが追加されました。これには、AD FS ファームの TLS またはトークン署名証明書を更新するサブタスクが含まれています。
- "プライマリ サーバーの指定" という新しいフェデレーション管理サブタスクが追加されました。これにより、管理者は、AD FS ファームの新しいプライマリ サーバーを指定できます。
- "サーバーの管理" という新しいフェデレーション管理タスクが追加されました。これには、AD FS サーバーのデプロイ、Web アプリケーション プロキシ サーバーのデプロイ、およびプライマリ サーバーの指定を行うサブタスクが含まれています。
- 現在の AD FS 設定を表示する、"フェデレーション構成の表示" という新しいフェデレーション管理タスクが追加されました。  (この追加により、AD FS 設定は [ソリューションのレビュー] ページから削除されました。)

### <a name="fixed-issues"></a>修正された問題
- 対応する連絡先オブジェクトを引き継ぐユーザー オブジェクトが自己参照 (ユーザーが自身のマネージャーであるなど) を持つシナリオでの同期エラーの問題を解決しました。
- ヘルプのポップアップがキーボード フォーカスで表示されるようになりました。
- 自動アップグレードでは、競合するアプリが 6 時間実行されている場合は、それを強制終了し、アップグレードを続行します。
- ディレクトリ拡張機能を選択するときに、顧客が選択できる属性の数をオブジェクトあたり 100 個に制限します。 Azure にはオブジェクトあたり最大 100 個の拡張属性があるため、これでエクスポート中のエラーの発生を防止します。
- AD 接続スクリプトがより堅固になるようにバグを修正しました。
- 既存の名前付きパイプ WCF サービスを使用したマシンへの Azure AD Connect のインストールがより堅牢になるようにバグを修正しました。
- 初期インストール時に ADSync サービスを開始できないグループ ポリシーに関する診断およびトラブルシューティングを機能強化しました。
- Windows コンピューターの表示名が正しく書き込まれなかったバグを修正しました。
- Windows コンピューターの OS の種類が正しく書き込まれなかったバグを修正しました。
- Windows 10 以外のコンピューターが予期せず同期していたバグを修正しました。 この変更の影響として、以前に同期された Windows 10 以外のコンピューターが削除されるようになったことに注意してください。 Windows コンピューターの同期は、Windows 10 デバイスでのみ機能する Hybrid Azure AD ドメイン参加にのみ使用されるため、これはどの機能にも影響しません。
- ADSync PowerShell モジュールに新しい (内部) コマンドレットをいくつか追加しました。


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Azure AD Connect の以前のバージョンから 1.3.21.0 へのアップグレードに関する既知の問題 (Azure AD Connect が正常にアップグレードされても Microsoft 365 ポータルによって更新されたバージョンが反映されない) があります。
>
> これを解決するには、**AdSync** モジュールをインポートしてから、Azure AD Connect サーバー上で `Set-ADSyncDirSyncConfiguration` PowerShell コマンドレットを実行します。  次の手順を使用できます。
>
>1.    管理者モードで PowerShell を開きます。
>2.    `Import-Module "ADSync"` を実行します。
>3.    `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""` を実行します。
 
### <a name="release-status"></a>リリースの状態 

2019/05/14:ダウンロード対象としてリリース済み

### <a name="fixed-issues"></a>修正された問題 

- Microsoft Azure Active Directory Connect ビルド 1.3.20.0 に存在する特権の昇格の脆弱性を修正しました。  この脆弱性により、特定の条件下で、攻撃者は特権アカウントのコンテキストで 2 つの PowerShell コマンドレットを実行し、特権の必要なアクションを実行することができる可能性があります。  このセキュリティ更新プログラムは、これらのコマンドレットを無効にすることで問題を修正します。 詳細については、[セキュリティ更新プログラム](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
