---
title: Multi-Factor Authentication - Azure SQL | Microsoft Docs
description: "SQL Database と SQL Data Warehouse の SSMS で Multi-Factored Authentication を使用します。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: security-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 04/07/2017
ms.author: rickbyh
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 33282f1ce0c31056524359aeb42399be550334e2
ms.contentlocale: ja-jp
ms.lasthandoff: 04/27/2017


---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>SQL Database と SQL Data Warehouse でのユニバーサル認証 (MFA 対応の SSMS サポート)
Azure SQL Database と Azure SQL Data Warehouse では、*Active Directory ユニバーサル認証*を使用して、SQL Server Management Studio (SSMS) からの接続をサポートするようになりました。 

- Active Directory ユニバーサル認証では、2 つの非対話型の認証方式 (Active Directory パスワード認証と Active Directory 統合認証) をサポートします。 非対話型 Active Directory パスワード認証方式および Active Directory 統合認証方式は、さまざまなアプリケーション (ADO.NET、JDBC、ODBC など) で使用できます。 これら 2 つの方式では、ポップアップ ダイアログ ボックスは表示されません。

- Active Directory ユニバーサル認証とは、*Azure Multi-Factor Authentication* (MFA) もサポートする対話型の認証方式です。 Azure MFA では、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することができます。 電話、テキスト メッセージ、スマート カードと PIN、モバイル アプリ通知など、簡単な各種確認オプションによって強力な認証が実現するため、ユーザーは自分に最も合った方法を選択できます。 Azure AD との対話型 MFA はポップアップ ダイアログ ボックスで検証できます。

Multi-Factor Authentication の説明については、 [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)に関する記事を参照してください。
構成手順については、「[SQL Server Management Studio 用に Azure SQL Database の多要素認証を構成する](sql-database-ssms-mfa-authentication-configure.md)」をご覧ください。

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD ドメイン名またはテナント ID パラメーター   

[SSMS バージョン 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 以降では、別の Azure Active ディレクトリから現在の Active Directory にインポートされたユーザーが、接続時に Azure AD のドメイン名またはテナント ID を指定できます。 これにより、**Active Directory ユニバーサル認証**の際に、正しい認証機関を識別できます。 また、このオプションでは、outlook.com、hotmail.com、live.com などの Microsoft のアカウント (MSA) のサポートが必要です。 ユニバーサル認証を使用して認証される、これらすべてのユーザーは、Azure AD ドメイン名またはテナント ID を入力する必要があります。 このパラメーターは、Azure サーバーがリンクしている、現在の Azure AD ドメイン名またはテナント ID を表しています。 たとえば、Azure サーバーが Azure AD ドメイン `contosotest.onmicrosoft.com` と関連付けられていて、このドメインにユーザー `joe@contosodev.onmicrosoft.com` が Azure AD ドメイン `contosodev.onmicrosoft.com` からインポートされたユーザーとしてホストされている場合、このドメイン名はこのユーザーを `contosotest.onmicrosoft.com` として認証する必要があります。 ユーザーが Azure サーバーにリンクされている Azure AD のネイティブ ユーザーであるが、MSA アカウントではない場合、ドメイン名またはテナント ID は必要ありません。 パラメーター (SSMS バージョン 17 以降) を入力するには、**[データベースへの接続]** ダイアログ ボックスで **[Active Directory のユニバーサル認証]** を選択して入力を完了します。次に、**[オプション]**、**[接続プロパティ]** タブの順にクリックし、**[AD ドメイン名またはテナント ID]** ボックスをオンにして、ドメイン名 (**contosotest.onmicrosoft.com**) またはテナント ID の GUID などの認証機関を指定します。

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>SQL Database と SQL Data Warehouse でのユニバーサル認証の制限事項
* SSMS は、Active Directory ユニバーサル認証を介する MFA で現在有効になっている唯一のツールです。
* ユニバーサル認証を使用して SSMS のインスタンスにログインできるのは、1 つの Azure Active Directory アカウントのみです。 別の Azure AD アカウントとしてログインするには、SSMS の別のインスタンスを使用する必要があります (この制限は Active Directory ユニバーサル認証に限定されます。 Active Directory パスワード認証、Active Directory 統合認証、または SQL Server 認証の使用時は別のサーバーにログインできます)。
* SSMS では、オブジェクト エクスプローラー、クエリ エディター、クエリ ストアの視覚化で Active Directory ユニバーサル認証がサポートされます。
* DacFx とスキーマ デザイナーでは、ユニバーサル認証はサポートされません。
* サポートされているバージョンの SSMS を使用する必要があることを除き、Active Directory ユニバーサル認証に関する追加のソフトウェア要件はありません。


## <a name="next-steps"></a>次のステップ

* 構成手順については、「[SQL Server Management Studio 用に Azure SQL Database の多要素認証を構成する](sql-database-ssms-mfa-authentication-configure.md)」をご覧ください。
* 他のユーザーへのデータベースへのアクセス権の付与: [SQL Database の認証と承認: アクセス権の付与](sql-database-manage-logins.md)  
他のユーザーがファイアウォール経由で接続する手順は、「[Azure Portal を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を作成する](sql-database-configure-firewall-settings.md)」をご覧ください。
* Azure AD の構成と管理については、「[SQL Database または SQL Data Warehouse で Azure Active Directory 認証を構成して管理する](sql-database-aad-authentication-configure.md)」を参照してください。



