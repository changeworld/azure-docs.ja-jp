---
title: Azure Active Directory アプリケーション プロキシの GDPR | Microsoft Docs
description: Azure Active Directory アプリケーション プロキシの GDPR について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a3df15743b4918d72fac5f8769a2d3ee721a452f
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2018
---
# <a name="gdpr-in-the-azure-active-directory-application-proxy"></a>Azure Active Directory アプリケーション プロキシの GDPR  

Azure Active Directory (Azure AD) のアプリケーション プロキシは、他のすべての Microsoft サービスおよび機能と共に GDPR に準拠しています。 Microsoft の GDPR サポートについて詳しくは、「[Licensing Terms and Documentation (ライセンス条項およびドキュメント)](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)」をご覧ください。

この機能にはコンピューター上のコネクタが含まれているため、GDPR 準拠の維持を監視する必要のある状況がいくつかあります。 アプリケーション プロキシでは、EUII を含めることができる次の種類のログが作成されます。

- コネクタ イベント ログ
- Windows イベント ログ

GDPR 準拠を維持する方法は 2 つあります。

- 発生した要求を削除およびエクスポートする

- ログ記録をオフにする

お困りの場合:

- Windows イベント ログのデータ保有期間の構成方法については、「[Settings for event logs (イベント ログの設定)](https://technet.microsoft.com/library/cc952132.aspx)」をご覧ください。 
- Windows イベント ログの一般情報については、「[Using Windows Event Log (Windows イベント ログの使用)](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx)」をご覧ください。


接続のイベント ログは `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace` にあります。 以下のセクションでは、コネクタ イベント ログの関連手順を示します。 すべてのコネクタ コンピューターでこれらの手順を完了する必要があります。
 

## <a name="processing-requests"></a>要求の処理

お客様は次の 3 種類の要求に責任を負います。 

- 削除
- 表示
- エクスポート
 
表示/エクスポート要求を処理するには、各ログ ファイルで関連エントリを検索する必要があります。 

ログはテキスト ファイルであるため、たとえば `findstr`コマンドを使って、ユーザーに関連するエントリを検索できます。 ログには次のフィールドがある可能性があるため、これらのフィールドを検索します。 

- UserId
- Kerberos の制約付き委任を使っているアプリケーション用に構成されたユーザー名の種類:
    - ユーザー プリンシパル名
    - オンプレミスのユーザー プリンシパル名
    - ユーザー プリンシパル名のユーザー名部分
    - オンプレミスのユーザー プリンシパル名のユーザー名部分
    - オンプレミスの SAM のアカウント名 

 
これらのフィールドを収集し、ユーザーと共有することができます。
削除要求を処理するには、関連ログを削除する必要があります。 コネクタ サービス (Microsoft Azure AD アプリケーション プロキシ コネクタ) を再起動して、新しいログ ファイルを生成することができます。 新しいログ ファイルにより、以前のログ ファイルを削除することができます。 表示/エクスポートのプロセスに従ってすべての関連ログを検索し、それらのフィールドやファイルを選択的に削除できます。 不要になった場合は、すべての以前のログ ファイルをいつでも削除できます。


## <a name="turn-off-connector-logs"></a>コネクタ ログをオフにする

コネクタ ログをオフにするには、強調表示された行を削除することで `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` を調整する必要があります。 


![構成](./media/active-directory-application-proxy-gdpr/01.png)


## <a name="next-steps"></a>次の手順

Azure AD アプリケーション プロキシの概要については、「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](manage-apps/application-proxy.md)」をご覧ください。

