<properties
   pageTitle="Azure AD Connect: バージョンのリリース履歴 | Microsoft Azure"
   description="このトピックは、Azure AD Connect と Azure AD Sync のすべてのリリースの一覧です"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/02/2015"
   ms.author="andkjell"/>

# Azure AD Connect: バージョンのリリース履歴

Azure Active Directory チームは、Azure AD Connect を新機能で定期的に更新しています。すべての追加機能がすべてのユーザーに適用されるわけではありません。

この記事は、リリースされたバージョンを追跡し、最新バージョンに更新する必要があるかどうかを判断できるようにするためのものです。

関連リンク:

- 更新プログラムの適用に必要な空きについては、「[Azure AD Connect に必要なアカウントとアクセス許可](active-directory-aadconnect-accounts-permissions.md#upgrade)」を参照してください。
- [Azure AD Connect のダウンロード](http://go.microsoft.com/fwlink/?LinkId=615771)

## 1\.0.9131.0
リリース日: 2015 年 12 月

**修正された問題:**

- AD DS でパスワードを変更するときにはパスワードの同期が機能しない場合があるが、パスワードの設定時には機能する。
- プロキシ サーバーがある場合、Azure AD に対する認証が、構成ページ上でのインストールまたはアップグレード中に失敗する場合がある。
- SQL の SA でない場合、完全な SQL Server で以前のリリースの Azure AD Connect から更新すると失敗する。
- リモートの SQL Server で以前のリリースの Azure AD Connect から更新すると、「ADSync SQL データベースにアクセスできません」というエラーが表示される。

## 1\.0.9125.0
リリース: 2015 年 11 月

**新機能:**

- ADFS から Azure AD に対する信頼を再構成できるようになりました。
- Active Directory スキーマを更新し、同期規則を再生成できるようになりました。
- 同期規則を無効にできるようになりました。
- 同期規則の新しいリテラルとして "AuthoritativeNull" を定義できるようになりました。

**新しいプレビュー機能:**

- [Azure AD Connect Health for Sync](active-directory-aadconnect-health-sync.md)。
- [Azure AD Domain Services](active-directory-ds-getting-started.md) のパスワード同期がサポートされました。

**新しくサポートされたシナリオ:**

- 複数のオンプレミス Exchange 組織がサポートされました。詳細については、「[複数の Active Directory フォレストを伴うハイブリッド デプロイメント](https://technet.microsoft.com/library/jj873754.aspx)」を参照してください。

**修正された問題:**

- パスワード同期の問題:
    - スコープ外からスコープ内に移動されたオブジェクトのパスワードは同期されなくなります。これには、OU と属性フィルターの両方が含まれます。
    - 同期に含める新しい OU を選択する際に、完全なパスワード同期は必要ありません。
    - 無効なユーザーが有効になっても、パスワードは同期されません。
    - パスワード再試行キューは無制限です。5,000 個のオブジェクトを上限として削除されるという以前の制限は削除されました。
    - [トラブルシューティングが改善されました](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization)。
- Windows Server 2016 フォレスト機能レベルを使用して Active Directory に接続することはできなくなりました。
- 最初のインストール後にグループ フィルターに使用したグループを変更できなくなりました。
- パスワード ライトバックを有効にした状態でパスワードを変更して、各ユーザーの Azure AD Connect の新しいユーザー プロファイルを作成することはできなくなりました。
- 同期規則スコープに Long Integer 値を使用できなくなりました。
- 到達不能なドメイン コントローラーがある場合、[デバイスの書き戻し] チェックボックスは無効なままです。

## 1\.0.8667.0
リリース日: 2015 年 8 月

**新機能:**

- Azure AD Connect インストール ウィザードが、すべての Windows Server 言語にローカライズされました。
- Azure AD パスワード管理を使用する場合のアカウント ロック解除のサポートが追加されました。

**修正された問題:**

- インストールを開始したユーザー以外のユーザーがインストールを続けると、Azure AD Connect インストール ウィザードがクラッシュします。
- 以前の Azure AD Connect のアンインストールで Azure AD Connect Sync を完全にアンインストールできなかった場合、再インストールすることができません。
- ユーザーがフォレストのルート ドメインに属していないか、英語以外のバージョンの Active Directory が使用されている場合、高速インストールを使用して Azure AD Connect をインストールすることはできません。
- Active Directory ユーザー アカウントの FQDN を解決できない場合、スキーマをコミットできなかったという誤ったエラー メッセージが表示されます。
- Active Directory Connector で使用されているアカウントがウィザードの外部で変更された場合、ウィザードのその後の実行が失敗します。
- ドメイン コントローラーで、Azure AD Connect のインストールが失敗することがあります。
- 拡張属性が追加されている場合、"ステージング モード" の有効化や無効化ができません。
- Active Directory Connector での正しくないパスワードのために、一部の構成ではパスワード ライトバックが失敗します。
- 属性フィルターで dn が使用されている場合、DirSync をアップグレードできません。
- パスワード リセットの使用時に CPU 使用量が過剰になります。

**削除されたプレビュー機能:**

- [ユーザーの書き戻し](active-directory-aadconnect-feature-preview.md#user-writeback)プレビュー機能は、プレビューを利用されているお客様からのフィードバックに基づいて一時的に削除されました。このプレビュー機能は、提供されたフィードバックに対処した後で、再度追加されます。

## 1\.0.8641.0
リリース日: 2015 年 6 月

**Azure AD Connect の最初のリリースです。**

名前が Azure AD Sync から Azure AD Connect に変更されました。

**新機能:**

- [簡単設定](active-directory-aadconnect-get-started-express.md)を使用したインストール
- [ADFS の構成](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- [DirSync からのアップグレード](active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [誤って削除されないように保護する](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- [ステージング モード](active-directory-aadconnectsync-operations.md#staging-mode)の導入

**新しいプレビュー機能:**

- [ユーザーの書き戻し](active-directory-aadconnect-feature-preview.md#user-writeback)
- [グループの書き戻し](active-directory-aadconnect-feature-preview.md#group-writeback)
- [デバイスの書き戻し](active-directory-aadconnect-get-started-custom-device-writeback.md)
- [ディレクトリ拡張機能](active-directory-aadconnect-feature-preview.md#directory-extensions)


## 1\.0.494.0501
リリース日: 2015 年 5 月

**新しい要件:**

- Azure AD Sync のインストールに .Net framework 4.5.1 が必要になりました。

**修正された問題:**

- Azure AD からのパスワード ライトバックが、servicebus 接続のエラーで失敗します。

## 1\.0.491.0413
リリース日: 2015 年 4 月

**修正された問題と機能強化:**

- ごみ箱が有効になっていて、フォレスト内に複数のドメインがある場合、Active Directory Connector が削除を正しく処理しません。
- Azure Active Directory Connector で、インポート操作のパフォーマンスが改善されました。
- グループがメンバーシップの制限を超えた場合 (既定では、制限は 50 k オブジェクトに設定)、Azure Active Directory でグループが削除されます。新しい動作では、グループは残り、エラーがスローされ、新しいメンバーシップの変更はエクスポートされません。
- 同じ DN のステージングされた削除がコネクタ スペース内に既に存在する場合、新しいオブジェクトをプロビジョニングすることはできません。
- オブジェクトでステージングされている変更はありませんが、一部のオブジェクトが差分同期中に同期中としてマークされます。
- パスワード同期を強制すると、優先 DC リストも削除されます。
- CSExportAnalyzer には、一部のオブジェクトの状態に関する問題があります。

**新機能:**

- 結合で、MV の "任意" のオブジェクト型に接続できるようになりました。

## 1\.0.485.0222
リリース日: 2015 年 2 月

**機能強化:**

- インポートのパフォーマンスが強化されました。

**修正された問題:**

- パスワード同期が、属性フィルターで使用される cloudFiltered 属性を受け取ります。フィルター処理されたオブジェクトが、パスワード同期のスコープに含まれなくなります。
- トポロジが非常に多くのドメイン コントローラーを持つまれな状況では、パスワード同期が機能しません。
- Azure AD/Intune でデバイス管理が有効化された後、Azure AD Connector からのインポート時に、"サーバーが停止" します。
- 同じフォレスト内の複数のドメインの外部セキュリティ プリンシパル (FSP) を結合すると、あいまい結合のエラーが発生します。

## 1\.0.475.1202
リリース日: 2014 年 12 月

**新機能:**

- 属性ベースのフィルターでのパスワード同期がサポートされるようになりました。詳細については、[フィルターによるパスワード同期](active-directory-aadconnectsync-configure-filtering.md)に関するページを参照してください。
- 属性 msDS-ExternalDirectoryObjectID が AD に書き戻されます。これによって、ハイブリッド Exchange デプロイでオンラインとオンプレミスの両方のメールボックスにアクセスするために OAuth2 を使用する Office 365 アプリケーションのサポートが追加されます。

**修正されたアップグレードの問題:**

- より新しいバージョンのサインイン アシスタントをサーバーで利用できます。
- Azure AD Sync をインストールするために、カスタム インストール パスが使用されていました。
- 無効なカスタム結合条件によって、アップグレードがブロックされます。

**その他の修正:**

- Office Pro Plus 用のテンプレートが修正されました。
- ダッシュで始まるユーザー名によって発生する、インストールの問題が修正されました。
- インストール ウィザードを 2 回目に実行しているときに sourceAnchor 設定が失われる問題を修正しました。
- パスワード同期の ETW トレースの問題が修正されました。

## 1\.0.470.1023
リリース日: 2014 年 10 月

**新機能:**

- 複数のオンプレミス AD から Azure AD へのパスワードの同期。
- すべての Windows Server 言語にローカライズされたインストール UI。

**AADSync 1.0 GA からのアップグレード**

Azure AD Sync が既にインストールされている場合、標準の同期規則を変更したのであれば、追加の手順が 1 つ必要になります。1.0.470.1023 リリースにアップグレードした後で、変更した同期規則は複製されます。変更された各同期規則で、次の操作を行ってください。

- 変更した同期規則を探して、変更内容をメモしておきます。
- 同期規則を削除します。
- Azure AD Sync によって作成された新しい同期規則を探して、変更を再適用します。

**AD アカウントのアクセス許可**

AD アカウントには、AD からのパスワード ハッシュを読み取ることができるように、追加のアクセス許可を与える必要があります。付与するアクセス許可の名前は、[ディレクトリの変更のレプリケート] と [ディレクトリの変更をすべてにレプリケート] です。パスワード ハッシュを読み取るためには、両方のアクセス許可が必要です。

## 1\.0.419.0911
リリース日: 2014 年 9 月

**Azure AD Sync の最初のリリースです。**

## 次のステップ
「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_1203_2015-->