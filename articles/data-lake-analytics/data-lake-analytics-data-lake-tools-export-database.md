---
title: "Azure Data Lake Tools for Visual Studio を使用して U-SQL データをエクスポートする方法 | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio を使用して、U-SQL データベースをエクスポートし、同時にローカル アカウントにインポートする方法を説明します。"
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: yanacai
ms.openlocfilehash: 9f11e07f7fbaf2b708d6fbc8a746238745132bda
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-export-u-sql-database"></a>U-SQL データベースのエクスポート方法

このドキュメントでは、[Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs) を使用して U-SQL データベースを 1 つの U-SQL スクリプトとダウンロードされたリソースとしてエクスポートする方法について説明します。 同じプロセスでは、エクスポートされたデータベースの、ローカル アカウントへのインポートもサポートされています。

お客様は、通常、開発、テストおよび運用のために複数の環境を管理しています。 これらの環境は、開発者のローカル コンピューター上のローカル アカウントと、Azure 上の Azure Data Lake Analytics アカウントの両方でホストされています。 開発およびテスト環境で U-SQL クエリを開発およびチューニングする場合、一般的に、開発者は、実稼働データベース内のすべてを再構築する必要があります。 **データベースのエクスポート ウィザード**を使用することにより、このプロセスを高速化できます。 ウィザードを使用すると、開発者は、既存のデータベース環境とサンプル データを他の Azure Data Lake Analytics アカウントへ複製できます。

## <a name="export-steps"></a>エクスポートの手順

### <a name="step-1-right-click-the-database-in-server-explorer-and-click-export"></a>手順 1: サーバー エクスプローラーでデータベースを右クリックし、[エクスポート] をクリックします。

サーバー エクスプローラーに、自分が権限を持つすべての Azure Data Lake Analytics アカウントが表示されます。 エクスポートするデータベースが含まれているアカウントを展開して、データベースを右クリックして **[エクスポート]** を選択します。コンテキスト メニューが表示されない場合は、[ツールを最新リリースに更新](http://aka.ms/adltoolsvs)する必要があります。

![Data Lake Analytics ツールのデータベースのエクスポート](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

### <a name="step-2-configure-the-objects-you-want-to-export"></a>手順 2: エクスポートするオブジェクトを構成する

サイズの大きなデータベースのごく一部のみを使用する場合、エクスポート ウィザードでエクスポートするオブジェクトのサブセットを構成することができます。 エクスポート操作では U-SQL ジョブが実行されるため、Azure アカウントからエクスポートするとパフォーマンスに多少の影響があることに注意してください。

![Data Lake Analytics ツールのデータベースのエクスポート ウィザード](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-more-configurations"></a>手順 3: オブジェクト一覧や構成の詳細を確認する

この手順では、ダイアログ ボックスの上部で、選択されたオブジェクトを再確認することができます。 エラーがある場合は、[前へ] をクリックして前に戻って、エクスポートするオブジェクトを再構成できます。

エクスポート対象に関するその他の構成を行うこともでき、これらの構成については次の表で説明されています。

|構成|説明|
|-------------|-----------|
|宛先名|この名前は、アセンブリ、追加のファイルおよびサンプル データなどのエクスポートされたデータベース リソースを保存する場所を示します。 この名前のフォルダーは、ローカル データ ルート フォルダー下に作成されます。|
|プロジェクト ディレクトリ|このパスは、すべてのデータベース オブジェクトの定義を含む、エクスポートされた U-SQL スクリプトを保存する場所を定義します。|
|スキーマのみ|このオプションを選択すると、データベースの定義およびリソース (アセンブリやその他のファイルなど) のみがエクスポートされます。|
|スキーマとデータ|このオプションを選択すると、データベース定義、リソース、およびデータがエクスポートされます。 テーブルの上位 N 行がエクスポートされます。|
|ローカル データベースに自動的にインポートします|この構成をオンにすると、エクスポートが完了した後、エクスポートされたデータベースはローカル データベースに自動的にインポートされます。|

![Data Lake Analytics ツールのデータベースのエクスポート ウィザードの構成](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>手順 4: エクスポート結果を確認する

これらすべての設定が終わり、エクスポートが実行されると、ウィザードのログ ウィンドウにエクスポート結果が表示されます。 スクリーンショットで赤い四角形が付いて表示されているログに、アセンブリ、その他のファイル、サンプル データを含むエクスポートされた U-SQL スクリプトやデータベース リソース の場所が表示されます。

![完了した Data Lake Analytics ツールのデータベースのエクスポート ウィザード](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="how-to-import-the-exported-database-to-local-account"></a>ローカル アカウントにエクスポートされたデータベースをインポートする方法

このインポートを行う最も便利な方法は、エクスポート中に手順 3 の **[ローカル データベースに自動的にインポートします]** をオンにすることです。 これを忘れた場合は、エクスポート ログを使用してエクスポートされた U-SQL スクリプトを検索し、ローカルで U-SQL スクリプトを実行して、ローカル アカウントにデータベースをインポートします。

## <a name="how-to-import-the-exported-database-to-azure-data-lake-analytics-account"></a>Azure Data Lake Analytics アカウントにエクスポートされたデータベースをインポートする方法

その他の Azure Data Lake Analytics アカウントにデータベースをインポートするには、次の 2 つの手順が必要です。

1. アセンブリ、追加のファイル、サンプル データを含むエクスポートされたリソースを、インポートする Azure Data Lake Analytics アカウントの規定の Azure Data Lake Store アカウントにアップロードします。 エクスポートされたリソース フォルダーはローカルのデータ ルート フォルダーにあり、既定のストア アカウントのルートにフォルダー全体をアップロードできます。
2. アップロードが完了した後に、データベースをインポートする Azure Data Lake Analytics アカウントに、エクスポートされた U-SQL スクリプトを送信します。

## <a name="known-limitation"></a>既知の制限事項

現時点では、ウィザードで **[スキーマとデータ]** を選択した場合は、テーブルに格納されているデータをエクスポートするために、U-SQL ジョブが実行されます。 このため、データのエクスポート プロセスは速度が遅く、リソースも多く消費します。 

## <a name="next-steps"></a>次のステップ

* [U-SQL データベースの概要](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [ローカル実行と Azure Data Lake U-SQL SDK を使用した U-SQL ジョブのテストおよびデバッグ](data-lake-analytics-data-lake-tools-local-run.md)


