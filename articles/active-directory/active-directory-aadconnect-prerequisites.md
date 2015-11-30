<properties
   pageTitle="Azure Active Directory Connect の前提条件 | Microsoft Azure"
   description="ランディング ページやほとんどの検索結果に表示されるアーティクルの説明"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor="curtand"/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="11/16/2015"
   ms.author="andkjell;billmath"/>

# Azure Active Directory Connect (Azure AD Connect) の前提条件
このトピックでは、Azure AD Connect を使用するための前提条件とハードウェア要件について説明します。

## Azure AD Connect をインストールする前に
Azure AD Connect をインストールする前に、いくつか必要な項目があります。

**Azure AD**

- Azure サブスクリプションまたは [Azure 試用版サブスクリプション](http://azure.microsoft.com/pricing/free-trial/): Azure ポータルにアクセスする場合にのみ必要です。Azure AD Connect の使用には必要ありません。PowerShell または Office 365 を使用している場合は、Azure サブスクリプションがなくても Azure AD Connect を使用できます。Office 365 ライセンスを持っている場合は、Office 365 ポータルも使用できます。有料の Office 365 ライセンスを使用して、Office 365 ポータルから Azure ポータルにアクセスすることもできます。
- Azure AD で使用する予定のドメインを検証します。たとえばユーザー向けに contoso.com を使用する予定の場合は、そのドメインが検証されていること、および使用しているのドメインが既定のドメインである contoso.onmicrosoft.com だけではないことを確認します。
- Azure AD ディレクトリでは、既定で 50,000個のオブジェクトが許可されます。ドメインを検証すると、制限が 300,000 個のオブジェクトに増加します。Azure AD でさらに多くのオブジェクトが必要な場合は、制限をさらに増加させるサポート ケースを開く必要があります。500,000 個を超えるオブジェクトが必要な場合は、Office 365、Azure AD Basic、Azure AD Premium、または Enterprise Mobility Suite などのライセンスが必要です。

**オンプレミスのサーバーと環境**

- AD スキーマのバージョンとフォレストの機能レベルは、Windows Server 2003 以降である必要があります。ドメイン コント ローラーは、スキーマとフォレスト レベルの要件を満たしていれば、任意のバージョンを実行できます。
- **パスワード ライトバック**機能を使用する予定がある場合、ドメイン コントローラーは (最新の SP が適用された) Windows Server 2008 以降にインストールされている必要があります。
- Azure AD Connect は、Windows Server 2008 以降にインストールする必要があります。このサーバーをドメイン コントローラーにすることができます。Express 設定を使用する場合はメンバー サーバーにすることもできます。カスタム設定を使用する場合、サーバーはスタンドアロンにすることもでき、ドメインに参加する必要はありません。
- Small Business Server または Windows Server Essentials には、Azure AD Connect をインストールできません。サーバーは Windows Server Standard 以上を使用する必要があります。
- **パスワード同期**機能を使用する場合、サーバーは Windows Server 2008 R2 SP1 以降にする必要があります。
- Active Directory Federation Services をデプロイする場合、AD FS または Web アプリケーション プロキシがインストールされるサーバーは、Windows Server 2012 R2 以降である必要があります。これらのサーバーで、リモート インストール用の Windows リモート管理を有効にする必要があります。
- Azure AD Connect には、ID データを格納する SQL Server データベースが必要です。既定では、SQL Server 2012 Express LocalDB (SQL Server Express の簡易バージョン) がインストールされ、サービスのサービス アカウントがローカル コンピューターに作成されます。SQL Server Express のサイズ制限は 10 GB で、約 100,000 オブジェクトを管理できます。さらに多くのディレクトリ オブジェクトを管理する必要がある場合は、インストール プロセスで別のバージョンの SQL Server を指定する必要があります。Azure AD Connect では、SQL Server 2008 (SP4) から SQL Server 2014 まで、すべてのエディションの Microsoft SQL Server がサポートされています。

**Accounts**

- 統合する Azure AD ディレクトリの Azure AD グローバル管理者アカウント。
- Express 設定を使用する、または DirSync からアップグレードする場合は、ローカルの Active Directory のエンタープライズ管理者アカウント。
- カスタム設定のインストール パスを使用する場合は、[Active Directory 内のアカウント](active-directory-aadconnect-accounts-permissions.md)。

**接続**

- 送信プロキシを使用してインターネットに接続する場合は、次の設定を **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config** ファイルに追加して、インストール ウィザードと Azure AD Sync がインターネットと Azure AD に接続できるようにする必要があります。

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

このテキストは、ファイルの末尾に入力する必要があります。このコードの &lt;PROXYADRESS&gt; は、実際のプロキシ IP アドレスまたはホスト名を表します。 - プロキシがアクセスできる URL を制限している場合は、「[Office 365 の URL と IP アドレスの範囲](https://support.office.com/zh-CN/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」に記載されている URL をプロキシで開く必要があります。

**その他**

- 省略可能: 同期を検証するテスト ユーザー アカウント。

## コンポーネントの前提条件

Azure AD Connect は、PowerShell と .Net 4.5.1 に依存しています。Windows Server のバージョンに応じて、次の操作を行います。


- Windows Server 2012R2
  - PowerShell は既定でインストールされているため、操作は必要ありません。
  - .Net 4.5.1 以降のリリースは、Windows Update によって提供されます。コントロール パネルで、Windows Server に最新の更新プログラムがインストールされていることを確認します。
- Windows Server 2008R2 と Windows Server 2012
  - PowerShell の最新バージョンは、[Microsoft ダウンロード センター](http://www.microsoft.com/downloads)の **Windows Management Framework 4.0** で入手できます。
  - .Net 4.5.1 以降のリリースは、[Microsoft ダウンロード センター](http://www.microsoft.com/downloads)で入手できます。
- Windows Server 2008
  - PowerShell の最新のサポート バージョンは、[Microsoft ダウンロード センター](http://www.microsoft.com/downloads)の **Windows Management Framework 3.0** で入手できます。
 - .Net 4.5.1 以降のリリースは、[Microsoft ダウンロード センター](http://www.microsoft.com/downloads)で入手できます。

## Azure AD Connect でサポートされるコンポーネント

Azure AD Connect によって Azure AD Connect のインストール先にインストールされるコンポーネントの一覧を次に示します。この一覧は、基本的な高速インストール用です。[同期サービスのインストール] ページで異なる SQL Server を使用することを選択した場合、SQL Express LocalDB はローカルにインストールされません。

- Microsoft SQL Server 2012 のコマンド ライン ユーティリティ
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Windows PowerShell 用の Azure Active Directory モジュール
- IT プロフェッショナル向け Microsoft Online Services サインイン アシスタント
- Microsoft Visual C++ 2013 再配布パッケージ


## Azure AD Connect のハードウェア要件
次の表は、Azure AD Connect Sync コンピューターの最小要件を示しています。

| Active Directory 内のオブジェクトの数 | CPU | メモリ | ハード ドライブのサイズ |
| ------------------------------------- | --- | ------ | --------------- |
| 10,000 未満 | 1\.6 GHz | 4 GB | 70 GB |
| 10,000 ～ 50,000 | 1\.6 GHz | 4 GB | 70 GB |
| 50,000 ～ 100,000 | 1\.6 GHz | 16 GB | 100 GB |
| オブジェクトが 100,000 個以上の場合は完全バージョンの SQL Server が必要| | | |
| 100,000 ～ 300,000 | 1\.6 GHz | 32 GB | 300 GB |
| 300,000 ～ 600,000 | 1\.6 GHz | 32 GB | 450 GB |
| 600,000 を超過 | 1\.6 GHz | 32 GB | 500 GB |

AD FS または Web アプリケーション サーバーを実行するコンピューターの最小要件を次に示します。

- CPU: デュアル コア 1.6 GHz 以上
- メモリ: 2 GB 以上
- Azure VM: A2 構成またはそれ以上


## 次のステップ
「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=Nov15_HO4-->