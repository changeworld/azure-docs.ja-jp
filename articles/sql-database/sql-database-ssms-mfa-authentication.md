---
title: "SQL Database と SQL Data Warehouse での Azure AD MFA のための SSMS のサポート| Microsoft Docs"
description: "SQL Database と SQL Data Warehouse の SSMS で Multi-Factored Authentication を使用します。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/04/2016
ms.author: rick.byham@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3281ce8eba82c2cbfcb34415aa2145419c91332d


---
# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>SQL Database と SQL Data Warehouse での Azure AD MFA のための SSMS のサポート
Azure SQL Database と Azure SQL Data Warehouse では、" *Active Directory ユニバーサル認証*" を使用して SQL Server Management Studio (SSMS) からの接続をサポートするようになりました。 Active Directory ユニバーサル認証とは、 *Azure Multi-Factor Authentication* (MFA) をサポートする対話型ワークフローです。 Azure MFA では、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することができます。 電話、テキスト メッセージ、スマート カードと PIN、モバイル アプリ通知など、簡単な各種確認オプションによって強力な認証が実現するため、ユーザーは自分に最も合った方法を選択できます。 Multi-Factor Authentication の説明については、 [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)に関する記事を参照してください。

現在、SSMS では以下がサポートされています。

* ポップアップ ダイアログ ボックスでの検証が可能な、Azure AD との対話型 MFA。
* 多くの異なるアプリケーション (ADO.NET、JDBC、ODBC など) で使用可能な、非対話型 Active Directory パスワードおよび Active Directory 統合認証方式。 これら 2 つの方式では、ポップアップ ダイアログ ボックスは表示されません。

ユーザー アカウントが MFA 用に構成されている場合、対話型の認証ワークフローでは、ポップアップ ダイアログ ボックスやスマート カードの使用など、追加のユーザー操作が必要になります。ユーザー アカウントが MFA 用に構成されている場合、ユーザーは接続する Azure ユニバーサル認証を選択する必要があります。 ユーザー アカウントが MFA を必要としない場合は、引き続き他の 2 つの Azure Active Directory 認証オプションを使用できます。

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>SQL Database と SQL Data Warehouse でのユニバーサル認証の制限事項
* SSMS は、Active Directory ユニバーサル認証を介する MFA で現在有効になっている唯一のツールです。
* ユニバーサル認証を使用して SSMS のインスタンスにログインできるのは、1 つの Azure Active Directory アカウントのみです。 別の Azure AD アカウントとしてログインするには、SSMS の別のインスタンスを使用する必要があります (この制限は Active Directory ユニバーサル認証に限定されます。 Active Directory パスワード認証、Active Directory 統合認証、または SQL Server 認証の使用時は別のサーバーにログインできます)。
* SSMS では、オブジェクト エクスプローラー、クエリ エディター、クエリ ストアの視覚化で Active Directory ユニバーサル認証がサポートされます。
* DacFx とスキーマ デザイナーでは、ユニバーサル認証はサポートされません。
* MSA アカウントは Active Directory ユニバーサル認証でサポートされていません。
* 他の Azure Active Directory から現在の Active Directory にインポートされたユーザーの場合、SSMS での Active Directory ユニバーサル認証はサポートされていません。 アカウントの検証にテナント ID が必要になるが、それを実行するメカニズムがないため、これらのユーザーはサポートされていません。
* サポートされているバージョンの SSMS を使用する必要があることを除き、Active Directory ユニバーサル認証に関する追加のソフトウェア要件はありません。

## <a name="configuration-steps"></a>構成の手順
Multi-Factor Authentication の実装では、4 つの基本手順を行います。

1. **Azure Active Directory の構成** – 詳細については、「[オンプレミス ID と Azure Active Directory の統合](../active-directory/active-directory-aadconnect.md)」、[Azure AD への独自のドメイン名の追加](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)に関する記事、「[Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)」 (Microsoft Azure による Windows Server Active Directory とのフェデレーションのサポートの実現)、「[Azure AD ディレクトリの管理](https://msdn.microsoft.com/library/azure/hh967611.aspx)」、[Windows PowerShell による Azure AD の管理](https://msdn.microsoft.com/library/azure/jj151815.aspx)に関する記事をご覧ください。
2. **MFA の構成** – 手順については、「 [Azure Multi-Factor Authentication の構成](../multi-factor-authentication/multi-factor-authentication-whats-next.md)」を参照してください。 
3. **SQL Database または SQL Data Warehouse での Azure AD 認証の構成** – 手順については、「 [Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](sql-database-aad-authentication.md)」を参照してください。
4. **SSMS のダウンロード** – クライアント コンピューターで、「 [SQL Server Management Studio (SSMS) のダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)」から SSMS の最新版 (少なくとも 2016 年 8 月) をダウンロードします。

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>SSMS でユニバーサル認証を使用して接続する
最新版の SSMS を使用して SQL Database または SQL Data Warehouse に接続する手順は、次のとおりです。

1. ユニバーサル認証を使用して接続するには、**[サーバーに接続]** ダイアログ ボックスで、**[Active Directory Universal Authentication (Active Directory ユニバーサル認証)]** を選択します。
   ![1mfa-universal-connect][1]
2. SQL Database と SQL Data Warehouse では通常どおり、**[オプション]** をクリックし、**[オプション]** ダイアログ ボックスでデータベースを指定します。 次いで **[接続]**をクリックします。
3. **[アカウントにサインイン]** ダイアログ ボックスが開いたら、Azure Active Directory ID のアカウントとパスワードを入力します。
   ![2mfa-sign-in][2]
   
   > [!NOTE]
   > MFA を必要としないアカウントでのユニバーサル認証の場合は、この時点で接続が完了します。 MFA を必要とするユーザーの場合は、次の手順に進みます。
   > 
   > 
4. MFA 設定用の 2 つのダイアログ ボックスが表示される場合があります。 この 1 度限りの操作は MFA 管理者の設定に応じて異なり、省略することも可能です。 MFA が有効なドメインでは、この手順が事前に定義されている場合もあります (たとえば、ユーザーはスマート カードと PIN を使用するように求められます)。  
   ![3mfa-setup][3]
5. 2 つ目に表示される可能性のある 1 度限りのダイアログ ボックスでは、認証方式の詳細を選択できます。 使用可能なオプションは、管理者によって構成されます。
   ![4mfa-verify-1][4]
6. Azure Active Directory から確認用の情報が送信されます。 確認コードを受け取ったら、**[確認コードを入力する]** ボックスにコードを入力し、**[サインイン]** をクリックします。
   ![5mfa-verify-2][5]

確認が完了すると、有効な資格情報とファイアウォール アクセスが推定され、SSMS の接続が通常どおり行われます。

## <a name="next-steps"></a>次のステップ
他のユーザーへのデータベースへのアクセス権の付与: [SQL Database の認証と承認: アクセス権の付与](sql-database-manage-logins.md)  
他のユーザーがファイアウォール経由で接続する手順は、「[Azure Portal を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を作成する](sql-database-configure-firewall-settings.md)」をご覧ください。

[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png




<!--HONumber=Nov16_HO3-->


