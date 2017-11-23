---
title: "SQL Database のマルチテナント アプリ例のガイダンス - Wingtip SaaS | Microsoft Docs"
description: "Azure SQL データベースを使用する、サンプルのマルチ テナント アプリケーション (Wingtip SaaS サンプル) をインストールして実行するための手順とガイダンスを示します。"
keywords: "SQL データベース チュートリアル"
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: genemi
ms.openlocfilehash: fbfaea938676991cf6280e5dd8c1e1190aa268a8
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2017
---
# <a name="guidance-and-tips-for-azure-sql-database-multi-tenant-saas-app-example"></a>Azure SQL Database のマルチテナント SaaS アプリ例のガイダンスとヒント


## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Wingtip SaaS スクリプトのダウンロードとブロック解除

実行可能なコンテンツ (スクリプト、dll) は、zip ファイルが外部ソースからダウンロードされ、抽出されると、Windows によってブロックされる場合があります。 zip ファイルからスクリプトを抽出するとき、***以下の手順に従って、.zip ファイルをブロック解除してから抽出します***。 これにより、スクリプトの実行が許可されます。

1. [Wingtip SaaS GitHub のリポジトリ](https://github.com/Microsoft/WingtipSaaS)を参照します。
2. **[複製またはダウンロード]** をクリックします。
3. **[Download ZIP]** をクリックし、ファイルを保存します。
4. **WingtipSaaS master.zip** ファイルを右クリックし、**[プロパティ]** を選択します。
5. **[全般]** タブで **[ブロックの解除]** を選択します。
6. **[OK]**をクリックします。
7. ファイルを解凍します。

スクリプトは、*..\\WingtipSaaS-master\\Learning Modules* フォルダーにあります。


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>Wingtip SaaS PowerShell スクリプトを操作する

サンプルを最大限に活用するには、指定されたスクリプトを詳細に把握する必要があります。 ブレークポイントを使用してスクリプトをステップ実行し、さまざまな SaaS パターンがどのように実装されているか詳細に調べます。 指定されたスクリプトおよびモジュールを簡単にステップ実行してしっかりと理解するには、[PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise) の使用をお勧めします。

### <a name="update-the-configuration-file-for-your-deployment"></a>デプロイの構成ファイルを更新する

次の手順に従って、デプロイ時に設定したリソース グループとユーザー値で **UserConfig.psm1** ファイルを編集します。

1. *PowerShell ISE* を開き、...\\Learning Modules\\*UserConfig.psm1* をロードします。 
2. *ResourceGroupName*と*Name*をデプロイの固有の値で更新します (10 行目および 11 行目のみ)。
3. 変更を保存します。

これらの値を設定しておくと、これらのデプロイ固有の値をスクリプト毎に更新する必要がなくなります。

### <a name="execute-scripts-by-pressing-f5"></a>F5 キーを押してスクリプトを実行する

複数のスクリプトでは *$PSScriptRoot* を使用してフォルダー間を移動します。*$PSScriptRoot* が評価されるのは、**F5** キーを押してスクリプトが実行される場合のみです。  選択項目を強調表示して実行する (**F8** キー) とエラーが発生するため、スクリプトの実行時には **F5** キーを押してください。

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>スクリプトをステップスルーして実装を確認する

スクリプトを理解する最善の方法は、スクリプトをステップ実行して内容を確認することです。 わかりやすいワークフローの概要を提示している、組み込みの **Demo-** スクリプトを確認してください。 **Demo-** スクリプトは、各タスクを完了するために必要なステップを示しているので、ブレークポイントを設定して個々のコールをより詳しく検証し、さまざまな SaaS パターンの実装の詳細を確認してください。

PowerShell スクリプトの調査およびステップ実行に関するヒント

- PowerShell ISE で **Demo-** スクリプトを開きます。
- **F5** キーを使って実行または続行します (*$PSScriptRoot* は選択したスクリプトの実行時に評価されないため、**F8** キーの使用はお勧めできません)。
- 行をクリックまたは選択して **F9** キーを押し、ブレークポイントを設定します。
- **F10** キーを使用して、関数またはスクリプトの呼び出しをステップ オーバーします。
- **F11** キーを使用して、関数またはスクリプトの呼び出しにステップ インします。
- **Shift + F11** キーを使用して、現在の関数またはスクリプトの呼び出しからステップ アウトします。


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>SSMS を使用してデータベース スキーマを調べ、SQL クエリを実行する

アプリケーション サーバーとデータベースに接続して参照するには、[SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) を使用します。

デプロイには、接続先となる 2 つの SQL Database サーバー (*tenants1-&lt;ユーザー&gt;* サーバーと *catalog-&lt;ユーザー&gt;* サーバー) が最初に用意されています。 デモ接続を確実に成功させるために、どちらのサーバーにも、すべての IP を許可する[ファイアウォール規則](sql-database-firewall-configure.md)があります。


1. *SSMS* を開き、*tenants1-&lt;ユーザー&gt;.database.windows.net* サーバーに接続します。
2. **[接続]** > **[データベース エンジン...]** をクリックします。

   ![カタログ サーバー](media/saas-dbpertenant-wingtip-app-guidance-tips/connect.png)

3. デモの資格情報は、ログイン = *developer*、パスワード = *P@ssword1* です。

   ![connection](media/saas-dbpertenant-wingtip-app-guidance-tips/tenants1-connect.png)

4. 手順 2 と 3 を繰り返し、*catalog-&lt;ユーザー&gt;.database.windows.net* サーバーに接続します。


接続に成功すると、両方のサーバーが表示されます。 データベースの一覧は、プロビジョニングしたテナントに応じて異なる場合があります。

![オブジェクト エクスプローラー](media/saas-dbpertenant-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>次のステップ

[Wingtip SaaS アプリケーションのデプロイ](saas-dbpertenant-get-started-deploy.md)

