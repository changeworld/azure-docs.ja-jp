---
title: "Azure SQL Database との接続に関する一般的な問題のトラブルシューティング"
description: "Azure SQL Database の一般的な接続エラーを特定して解決する手順。"
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: ac463d1c-aec8-443d-b66e-fa5eadcccfa8
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: 676cecdd886cfb557e7859e1e9583f0a0f9f749c
ms.openlocfilehash: 222b9fe98592e0c78ec3d7c5ae4804bf75dd0d1e


---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Azure SQL Database との接続に関する一般的な問題のトラブルシューティング
Azure SQL Database との接続に失敗すると、[エラー メッセージ](sql-database-develop-error-messages.md)が表示されます。 この記事では、Azure SQL Database の接続に関する問題のトラブルシューティングに役立つトピックを紹介します。 ここでは接続の問題の[一般的な原因](#cause)を説明し、問題の特定に役立つ[トラブルシューティング ツール](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues)を紹介しています。また、[一時的なエラー](#troubleshoot-transient-errors)、および[永続的または一時的でないエラー](#troubleshoot-the-persistent-errors)を解決するトラブルシューティングの手順についてもご紹介します。 最後に、[Azure SQL Database の接続の問題に関連したすべての記事](#all-topics-for-azure-sql-database-connection-problems)をまとめています。

接続の問題が発生したら、この記事のトラブルシューティング手順を実行してみてください。
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>原因
接続の問題が発生した原因としては、次のいずれかが考えられます。

* アプリケーションの設計プロセスで、ベスト プラクティスと設計のガイドラインが適用されていない。  開始するには、「 [SQL Database の開発: 概要](sql-database-develop-overview.md) 」をご覧ください。
* Azure SQL Database の再構成
* ファイアウォールの設定
* 接続のタイムアウト
* 不適切なログイン情報
* Azure SQL Database リソースの一部が上限に達している

一般的に、Azure SQL Database の接続の問題は、次のカテゴリに分類されます。

* [一時的なエラー (短期間または断続的)](#troubleshoot-transient-errors)
* [永続的または一時的でないエラー (定期的に発生するエラー)](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Azure SQL Database の接続の問題のトラブルシューティング ツールを試してみる
特定の接続エラーが発生した場合は、[こちらのツール](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)をお試しください。問題の速やかな特定と解決に役立ちます。

## <a name="troubleshoot-transient-errors"></a>一時的なエラーのトラブルシューティング
アプリケーションで一時的なエラーが発生する場合は、次のトピックで、トラブルシューティングの手順やエラーの頻度を抑える方法に関するヒントを確認してください。

* [Troubleshooting Database &lt;x&gt; on Server &lt;y&gt; is unavailable (Error: 40613)](sql-database-troubleshoot-connection.md) (サーバー y 上のデータベース x が使用できない問題のトラブルシューティング (エラー: 40613))
* [SQL Database の SQL 接続エラーと一時エラーのトラブルシューティング、診断、防止](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>永続的 (一時的でない) エラーのトラブルシューティング
アプリケーションが Azure SQL Database の接続に引き続き失敗する場合は、一般的に、次のいずれかの問題が考えられます。

* ファイアウォールの構成:  Azure SQL データベースまたはクライアント側のファイアウォールにより、Azure SQL Database への接続がブロックされています。
* クライアント側のネットワークの再構成: 新しい IP アドレス、プロキシ サーバーなど。
* ユーザー エラー: 接続パラメーター (接続文字列のサーバー名など) の入力間違いなど。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>永続的な接続の問題を解決する手順
1. クライアントの IP アドレスを許可するには、 [ファイアウォール規則](sql-database-configure-firewall-settings.md) を設定します。 一時的なテストのために、0.0.0.0 を IP アドレス範囲の開始アドレス、255.255.255.255 を終了アドレスとするファイアウォール規則を設定します。 これにより、サーバーがすべての IP アドレスに開かれます。 これによって接続の問題が解決する場合は、この規則を削除した後、IP アドレスまたはアドレス範囲を適切に制限するファイアウォール規則を作成します。 
2. クライアントとインターネットの間のすべてのファイアウォールで、送信接続用のポート 1433 が開いていることを確認します。 Azure Active Directory 認証のために追加で開く必要があるポートに関する詳しい情報は、「[Configure the Windows Firewall to Allow SQL Server Access (SQL Server のアクセスを許可するための Windows ファイアウォールの構成)](https://msdn.microsoft.com/library/cc646023.aspx)」および「[Hybrid Identity Required Ports and Protocols (ハイブリッド ID で必要なポートとプロトコル)](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports)」を参照してください。
3. 接続文字列およびその他の接続設定を確認します。 [接続の問題に関するトピック](sql-database-connectivity-issues.md#connections-to-azure-sql-database)で、接続文字列のセクションを確認します。
4. ダッシュ ボードでサービスの正常性を確認します。 リージョンの障害があると思われる場合は、新しいリージョンへの回復手順を [障害からの回復](sql-database-disaster-recovery.md) のトピックで参照してください。

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>Azure SQL Database の接続の問題に関するすべてのトピック
次の表は、Azure SQL Database サービスに直接当てはまる、接続の問題に関するすべてのトピックをまとめたものです。

| &nbsp; | タイトル | 説明 |
| ---:|:--- |:--- |
| 1 |[Azure SQL Database との接続に関する一般的な問題のトラブルシューティング](sql-database-troubleshoot-common-connection-issues.md) |Azure SQL Database の接続に関する問題のトラブルシューティングは、まずこちらを参照してください。 Azure SQL Database での、一時的なエラー、および永続的もしくは一時的でないエラーの特定と解決の方法について説明しています。 |
| 2 |[SQL Database の SQL 接続エラーと一時エラーのトラブルシューティング、診断、防止](sql-database-connectivity-issues.md) |Azure SQL Database での SQL 接続エラーまたは一時エラーのトラブルシューティング、診断、防止の方法について説明します。 |
| 3 |[一時的なエラーに対処するための一般的なガイダンス](../best-practices-retry-general.md) |Azure SQL Database 接続時に発生した一時的なエラーに対処するための、一般的なガイダンスを紹介しています。 |
| 4 |[Troubleshoot connectivity issues with Microsoft Azure SQL Database](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) |このツールは、問題の特定と、接続エラーの解決に役立ちます。 |
| 5 |["サーバー &lt;y&gt; のデータベース &lt;x&gt; は現在使用できません。後で接続を再試行してください」エラーのトラブルシューティング](sql-database-troubleshoot-connection.md) |40613 エラーの特定と解決の方法を説明します: 「サーバー &lt;y&gt; 上のデータベース &lt;x&gt; は現在使用できません。 後で接続を再試行してください" のトラブルシューティングを行う |
| 6 |[SQL Database クライアント アプリケーションの SQL エラー コード: データベース接続エラーとその他の問題](sql-database-develop-error-messages.md) |よくあるデータベース接続エラー、データベース コピーの問題、一般エラーなど、SQL Database クライアント アプリケーションの SQL エラー コードについて説明します。 |
| 7 |[データベースが&1; 台の場合の Azure SQL Database のパフォーマンス ガイダンス](sql-database-performance-guidance.md) |お使いのアプリケーションに適したサービス レベルを判断する際に役立つガイダンスを紹介しています。 お使いの Azure SQL Database を最大限に活用するための、アプリケーションの調整に関する推奨事項も掲載しています。 |
| 8 |[SQL Database の開発: 概要](sql-database-develop-overview.md) |ここには、Azure SQL Database に接続して対話的に作業するためのさまざまなテクノロジのコード サンプルへのリンクがあります。 |

## <a name="next-steps"></a>次のステップ
* [Azure SQL Database のパフォーマンスに関する問題のトラブルシューティング](sql-database-troubleshoot-performance.md)
* [Microsoft Azure ドキュメントの検索](http://azure.microsoft.com/search/documentation/)
* [Azure SQL Database サービスの更新情報](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>その他のリソース
* [SQL Database の開発: 概要](sql-database-develop-overview.md)
* [一時的なエラーに対処するための一般的なガイダンス](../best-practices-retry-general.md)
* [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)




<!--HONumber=Feb17_HO3-->


