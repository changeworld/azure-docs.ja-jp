<properties
	pageTitle="Azure AD Connect: 接続に関する問題のトラブルシューティング | Microsoft Azure"
	description="Azure AD Connect での接続に関する問題のトラブルシューティング方法について説明します。"
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
	ms.date="04/27/2016"
	ms.author="andkjell"/>

# Azure AD Connect での接続に関する問題のトラブルシューティング
この記事では、Azure AD Connect と Azure AD の間の接続のしくみと、接続に関する問題のトラブルシューティング方法について説明します。このような問題は、プロキシ サーバーを備えた環境において発生する可能性が最も高くなります。

## インストール ウィザードにおける接続に関する問題のトラブルシューティング
Azure AD Connect では、認証に先進認証方式 (ADAL ライブラリを使用) を採用しています。インストール ウィザードと同期エンジンは、.NET アプリケーションであるため、machine.config を適切に構成する必要があります。

この記事では、Fabrikam がプロキシを介して Azure AD に接続する方法について説明します。プロキシ サーバーは fabrikamproxy という名前で、ポート 8080 を使用しています。

まず、[**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) が正しく構成されていることを確認する必要があります。 ![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

>[AZURE.NOTE]
Microsoft 以外の一部のブログでは、machine.config ではなく miiserver.exe.config に変更を加える必要があると記載されていますが、このファイルはアップグレードのたびに上書きされるため、初回インストール時には有効であっても、初回アップグレード時には機能しなくなります。このような理由から、machine.config を更新することをお勧めします。

プロキシ サーバーでは、必須となる URL を開いておくことも必要です。公式の URL 一覧は、「[Office 365 URL および IP アドレス範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」に記載されています。

その中でも、次の表に記載したものは Azure AD への接続に最低限必要な URL です。この一覧には、パスワード ライトバックや Azure AD Connect Health のようなオプション機能は含まれていません。ここには、初期構成に関するトラブルシューティングに役立つものが記載されています。

| URL | ポート | 説明 |
| ---- | ---- | ---- |
| mscrl.microsoft.com | HTTP/80 | CRL リストのダウンロードに使用します。 |
| **.verisign.com | HTTP/80 | CRL リストのダウンロードに使用します。 | | *.entrust.com | HTTP/80 | MFA の CRL リストのダウンロードに使用します。 | | *.windows.net | HTTPS/443 | Azure AD へのサインインに使用します。 | | secure.aadcdn.microsoftonline-p.com | HTTPS/443 | MFA に使用します。 | | *.microsoftonline.com | HTTPS/443 | Azure AD ディレクトリを構成し、データをインポートまたはエクスポートするために使用します。 |

## ウィザードでのエラー
インストール ウィザードでは、2 種類のセキュリティ コンテキストを使用しています。**[Azure AD に接続]** ページでは、現在サインインしているユーザーを使用しています。**[構成]** ページでは、使用するセキュリティ コンテキストを[同期エンジンのサービスを実行しているアカウント](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts)に変更します。ここで行うプロキシ構成はコンピューターに対してグローバルになるため、構成になんらかの問題があると、ほとんどの場合はすぐにウィザードの **[Azure AD に接続]** ページに反映されることになります。

インストール ウィザードに表示される最も一般的なエラーを次に示します。

### インストール ウィザードが正しく構成されていない
このエラーは、ウィザード自体がプロキシに接続できない場合に表示されます。 ![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- これが表示された場合は、[machine.config](active-directory-aadconnect-prerequisites.md#connectivity) が正しく構成されていることを確認します。
- 正しいようであれば、「[プロキシ接続を検証する](#verify-proxy-connectivity)」の手順に従って、ウィザード外部でも同じように問題が発生するかどうかを確認してください。

### MFA エンドポイントに到達できない
このエラーは、エンドポイント ****https://secure.aadcdn.microsoftonline-p.com** に到達できず、グローバル管理者が MFA を有効にしている場合に表示されます。 ![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

- これが表示された場合は、エンドポイント secure.aadcdn.microsoftonline-p.com がプロキシに追加されていることを確認します。

### パスワードを確認できない
インストール ウィザードによる Azure AD への接続は成功したものの、パスワード自体を確認できない場合は、次のようなメッセージが表示されます。 ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- そのパスワードは一時パスワードで、変更が必要ではないでしょうか。 また、本当に正しいパスワードでしょうか。 Azure AD Connect サーバーとは別のコンピューターで https://login.microsoftonline.com へのサインインを試し、アカウントが使用可能であることを確認してください。

### プロキシ接続を検証する
Azure AD Connect サーバーがプロキシおよびインターネットと実際に接続できているかどうかを検証するには、いくつかの PowerShell を使用して、プロキシが Web 要求を許可しているかどうかを確認します。PowerShell プロンプトで、`Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc` を実行します (厳密には、最初に呼び出すのは https://login.microsoftonline.com で、同様に機能しますが、応答が速いのは前述の URI です)。

PowerShell は、machine.config 内の構成を使用してプロキシに接続します。winhttp や netsh 内の設定値がこれらのコマンドレットに影響することはありません。

プロキシが正しく構成されていると、成功を示す次のようなステータスが表示されます。 ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

"**リモート サーバーに接続できません**" というメッセージが表示された場合は、PowerShell がプロキシを使用せずに直接呼び出しを試みているか、DNS が正しく構成されていません。**machine.config** ファイルが正しく構成されていることを確認してください。 ![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

プロキシが正しく構成されていない場合、次のエラーが表示されます。 ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png) ![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| エラー | エラー テキスト | コメント |
| ---- | ---- | ---- |
| 403 | 許可されていません | 要求された URL に対してプロキシが開かれていません。プロキシ構成を再検討し、[URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) が開かれていることを確認してください。 |
| 407 | プロキシの認証が必要です | プロキシ サーバーがサインイン情報を要求しましたが、何も指定されていません。お使いのプロキシ サーバーで認証が必要な場合は、それが machine.config ファイル内で構成されているようにしてください。また、サービス アカウントだけでなく、ウィザードを実行しているユーザーにドメイン アカウントを使用していることを確認してください。 |

## Azure AD Connect と Azure AD の間の通信パターン
上記の手順をすべて実行しても接続できない場合は、その時点でネットワーク ログを確認することをお勧めします。このセクションでは、通常の成功を示す接続パターンについて記載しています。また、ネットワーク ログを確認する場合に無視できる情報についても一覧にまとめています。

- https://dc.services.visualstudio.com への呼び出しがあります。インストールを正常に実行するうえで、この URL をプロキシで開いておくことは必須ではないため、これらの呼び出しは無視できます。
- DNS 解決によって DNS 名前空間の nsatc.net、および microsoftonline.com に属していない他の名前空間に含まれる実際のホストが一覧表示されます。ただし、実際のサーバー名に対する Web サービス要求は発生しないため、これらをプロキシに追加する必要はありません。
- エンドポイントの adminwebservice と provisioningapi (下のログを参照) は検出エンドポイントであり、実際に使用するエンドポイントを見つけるために使用されるため、リージョンによって異なります。

### 参照用プロキシ ログ
実際のプロキシ ログのダンプと、その取得元のインストール ウィザード ページを次に示します (エンドポイントが重複する項目は削除してあります)。これは、お使いの環境でのプロキシおよびネットワーク ログの参照用としてご利用ください。実際のエンドポイントは環境によって異なる場合があります (特に*斜体*で示したもの)。

**Azure AD に接続**

時刻 | URL
--- | ---
1/11/2016 8:31 | connect://login.microsoftonline.com:443
1/11/2016 8:31 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:32 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:32 | connect://login.microsoftonline.com:443
1/11/2016 8:33 | connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:33 | connect://*bwsc02-relay*.microsoftonline.com:443

**構成**

時刻 | URL
--- | ---
1/11/2016 8:43 | connect://login.microsoftonline.com:443
1/11/2016 8:43 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:43 | connect://login.microsoftonline.com:443
1/11/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | connect://*bba900-anchor*.microsoftonline.com:443
1/11/2016 8:44 | connect://login.microsoftonline.com:443
1/11/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:44 | connect://login.microsoftonline.com:443
1/11/2016 8:46 | connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:46 | connect://*bwsc02-relay*.microsoftonline.com:443

**初期同期**

時刻 | URL
--- | ---
1/11/2016 8:48 | connect://login.windows.net:443
1/11/2016 8:49 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:49 | connect://*bba900-anchor*.microsoftonline.com:443
1/11/2016 8:49 | connect://*bba800-anchor*.microsoftonline.com:443

## 認証エラー
このセクションでは、ADAL (Azure AD Connect で使用される認証ライブラリ) および PowerShell から返される可能性があるエラーについて説明します。エラーの説明は、次に進むステップを理解するうえで役立ちます。

### 無効な許可
ユーザー名またはパスワードが無効です。詳細については、「[パスワードを確認できない](#the-password-cannot-be-verified)」を参照してください。

### ユーザーの種類が不明
Azure AD ディレクトリが見つからないか、解決できません。確認されていないドメインのユーザー名でログインしようとした可能性があります。

### ユーザー領域を検出できない
ネットワークまたはプロキシの構成の問題です。ネットワークに接続できない場合は、「[インストール ウィザードにおける接続に関する問題のトラブルシューティング](#troubleshoot-connectivity-issues-in-the-installation-wizard)」を参照してください。

### ユーザー パスワードの期限切れ
資格情報が有効期限切れです。パスワードを変更してください。

### AuthorizationFailure
既知の問題です。

### 認証が取り消された
Multi-Factor Authentication (MFA) 要求が取り消されました。

### ConnectToMSOnline
認証は成功しましたが、Azure AD PowerShell に認証の問題があります。

### AzureRoleMissing
認証に成功しました。全体管理者ではありません。

### PrivilegedIdentityManagement
認証に成功しました。Privileged Identity Management が有効になっており、現時点では全体管理者ではありません。詳細については、「[Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)」を参照してください。

### CompanyInfoUnavailable
認証に成功しました。Azure AD から会社情報を取得できませんでした。

### RetrieveDomains
認証に成功しました。Azure AD からドメイン情報を取得できませんでした。

## 以前のリリース用のトラブルシューティング手順です。
ビルド番号 1.1.105.0 (2016 年 2 月リリース) 以降のリリースでは、サインイン アシスタントが提供されなくなりました。このセクションと構成は必要がなくなりますが、参照用に残されています。

シングル サインイン アシスタントを機能させるには、winhttp を構成する必要があります。これには、[**netsh**](active-directory-aadconnect-prerequisites.md#connectivity) を使用します。 ![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### サインイン アシスタントが正しく構成されていない
このエラーは、サインイン アシスタントがプロキシに接続できないか、プロキシが要求を許可していない場合に表示されます。 ![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- これが表示された場合は、[netsh](active-directory-aadconnect-prerequisites.md#connectivity) でプロキシ構成が正しいことを確認します。 ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- 正しいようであれば、「[プロキシ接続を検証する](#verify-proxy-connectivity)」の手順に従って、ウィザード外部でも同じように問題が発生するかどうかを確認してください。

## 次のステップ
「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_0518_2016-->