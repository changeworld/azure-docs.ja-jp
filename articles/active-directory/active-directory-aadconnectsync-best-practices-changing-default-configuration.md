<properties
	pageTitle="既定の構成を変更するためのベスト プラクティス | Microsoft Azure"
	description="Azure AD Connect Sync の既定の構成を変更するためのベスト プラクティスを紹介します。"
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
	ms.date="11/11/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync: 既定の構成を変更するためのベスト プラクティス

このトピックの目的は、Azure AD Connect Sync に対する、サポートされている変更とサポートされていない変更について説明することです。

Azure AD Connect で作成された構成は、オンプレミスの Active Directory を Azure AD と同期する環境の大半において "そのままで" 動作します。ただし、場合によっては、特定のニーズや要件を満たすために構成にいくつか変更を適用する必要があります。

## サービス アカウントに対する変更
Azure AD Connect Sync は、インストール ウィザードによって作成されたサービス アカウントで実行されます。このサービス アカウントは、同期で使用されるデータベースの暗号化キーを保持します。アカウントの作成には 127 文字の長いパスワードが使用され、そのパスワードは無期限に設定されています。

- サービス アカウントのパスワードの変更またはリセットは、**サポートされていません**。サービス アカウントのパスワードを変更またはリセットすると、暗号化キーが破棄され、サービスはデータベースにアクセスすることも、開始することもできなくなります。

## スケジューラに対する変更
Azure AD Connect Sync は、3 時間ごとに ID データを同期するよう設定されています。インストール中に、同期サーバーを操作するアクセス許可を持つサービス アカウントで実行される、スケジュールされたタスクが作成されます。

- スケジュールされたタスクに対する変更は**サポートされていません**。サービス アカウントのパスワードが不明です。「[サービス アカウントに対する変更](#changes-to-the-service-account)」を参照してください。
- 間隔が既定の 3 時間より短い同期は**サポートされていません**。

## 同期規則に対する変更

インストール ウィザードには、最も一般的なシナリオに対応できる構成が用意されています。構成を変更する必要がある場合、サポートされる構成を維持するには、次の規則に従う必要があります。

- 標準の同期規則を変更する場合、唯一サポートされる変更は無効にすることのみです。その他の変更は、アップグレード時に失われる可能性があります。
- 標準の規則に他の変更を加える必要がある場合は、標準の規則をコピーし、元の規則を無効にしてください。同期規則エディターのプロンプトに従って無効にすることができます。
- 同期規則エディターを使用して、カスタムの同期規則をエクスポートします。これにより、障害復旧シナリオの場合に、簡単に再作成をするために使用できる PowerShell スクリプトが提供されます。

>[AZURE.WARNING]標準の同期規則には拇印があります。標準の規則を変更すると、拇印が変わるので、今後、新しいリリースの Azure AD Connect を適用するときに問題が発生する可能性があります。変更する場合は、この記事の方法に従ってください。

### 不要な同期規則の削除
標準の同期規則は削除しないでください。削除しても次のアップグレード時に再作成されます。

場合によっては、トポロジで動作しない構成がインストール ウィザードで生成されることがあります。たとえば、アカウント リソース フォレスト トポロジがあり、アカウント フォレストのスキーマを Exchange スキーマで拡張している場合、Exchange の規則は、アカウント フォレスト用とリソース フォレスト用の両方が作成されます。この場合、Exchange 用の同期規則を無効にする必要があります。

![無効な同期規則](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

上の図では、インストール ウィザードによって、アカウント フォレスト内にある古い Exchange 2003 スキーマが検出されています。このスキーマが追加されてから、リソース フォレストが Fabrikam の環境に導入されました。古い Exchange 実装の属性が同期されないようにするには、図のように同期規則を無効にする必要があります。

### 標準の規則の変更
標準の規則を変更する必要がある場合は、標準の規則のコピーを作成し、元の規則を無効にする必要があります。そして、コピーした規則を変更します。この処理には同期規則エディターを使用できます。標準の規則を開くと、次のダイアログ ボックスが表示されます。

![標準の規則の警告](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

**[はい]** を選択して規則のコピーを作成します。コピーした規則を開きます。

![コピーした規則](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

このコピーした規則のスコープ、結合、変換について必要な変更を加えます。

### 属性をフローしない
属性をフローしない方法は 2 つあります。1 つ目の方法は、インストール ウィザードで使用できます。[選択した属性を削除](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering)できます。このオプションは、以前に属性を同期したことがない場合に使用できます。ただし、この属性を同期した後に、この機能で属性を削除した場合、同期エンジンによるその属性の管理は停止され、既存の値は Azure AD に残ります。

属性の値を削除し、今後はフローが実行されないようにするには、代わりにカスタム規則を作成する必要があります。

Fabrikam では、クラウドと同期する属性の一部が不要であることに気づきました。不要な属性が Azure AD から削除されるようにする必要があります。

![拡張属性](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- 新しい受信同期規則を作成し、説明を入力します。![説明](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- 種類が **Expression** でソースが **AuthoritativeNull** の属性フローを作成します。リテラル **AuthoritativeNull** は、低い優先度の同期規則が値を設定しようとしても、MV の値は空になることを示します。![拡張属性](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- 同期規則を保存します。**同期サービス**を開始し、[コネクタ] を探して **[実行]**、**[完全同期]** の順に選択します。これで、すべての属性フローが再計算されます。
- コネクタ スペースを検索して、目的の変更がエクスポート対象になっていることを確認します。![段階的な削除](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

## 次のステップ
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=Nov15_HO4-->