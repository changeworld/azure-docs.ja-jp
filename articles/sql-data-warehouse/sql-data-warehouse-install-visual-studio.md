---
title: "SQL Data Warehouse 用に Visual Studio と SSDT をインストールする | Microsoft Docs"
description: "Azure SQL Data Warehouse 用に Visual Studio と SQL Server Development Tools (SSDT) をインストールします"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 0ed9b406-9b42-4fe6-b963-fe0a5b48aac1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f7bafc793bf8abddd50b7976733b8fdf389a0366


---
# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>SQL Data Warehouse 用に Visual Studio 2015 と SSDT をインストールする
SQL Data Warehouse 用のアプリケーションを開発するには、Visual Studio 2015 を、最新バージョンの SQL Server Data Tools (SSDT) と組み合わせて使うことをお勧めします。  SSDT がインストールされている Visual Studio 2013 Update 5 も、下位互換性のためにサポートされています。  

SSDT がインストールされている Visual Studio を使用すると、SQL Server オブジェクト エクスプローラーを使用して、SQL Data Warehouse のテーブル、ビュー、ストアド プロシージャ、その他の多くのオブジェクトを視覚的に調査したり、クエリを実行したりすることができます。

> [!NOTE]
> SQL Data Warehouse では、Visual Studio データベース プロジェクトがまだサポートされていません。  この機能は、今後のバージョンで追加されます。
> 
> 

## <a name="step-1-install-visual-studio-2015"></a>手順 1: Visual Studio 2015 のインストール
Visual Studio 2015 をダウンロードしてインストールするには、以下のリンクをクリックしてください。 既に Visual Studio 2013 または 2015 がインストールされている場合は、手順 2. に進んで SSDT をインストールします。

1. [Visual Studio 2015 をダウンロードします][Visual Studio 2015 をダウンロード]。
2. MSDN の [Visual Studio のインストール][Visual Studio のインストール] に関するガイドに従ってインストールし、既定の構成を選択します。

## <a name="step-2-install-ssdt"></a>手順 2: SSDT のインストール
SSDT for Visual Studio をインストールするには、次の手順に従って Visual Studio 内から SSDT の更新プログラムをチェックします。

1. Visual Studio で、**[ツール]** / **[拡張機能と更新プログラム]** / **[更新プログラム]** をクリックします。
2. **[製品の更新プログラム]** を選択し、**[データベース ツール用の Microsoft SQL Server 更新プログラム]** を探します。

更新プログラムが見つからない場合は、最新のバージョンが既にインストールされています。  SSDT がインストールされていることを確認するには、**[ヘルプ]** / **[Microsoft Visual Studio のバージョン情報]** をクリックして表示される一覧の中から [SQL Server Data Tools] を探します。  SSDT の最新バージョンは、14.0.60525.0 です。  インストールするオプションを Visual Studio から使用できない場合は、代わりに [SSDT のダウンロード][SSDT のダウンロード] ページにアクセスし、手動で SSDT をダウンロードしてインストールします。

## <a name="next-steps"></a>次のステップ
これで、最新バージョンの SSDT がインストールされたので、SQL Data Warehouse に [接続][接続] できるようになりました。

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[接続]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Visual Studio 2015 をダウンロード]: https://www.visualstudio.com/downloads/
[Visual Studio のインストール]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT のダウンロード]: https://msdn.microsoft.com/library/mt204009.aspx



<!--HONumber=Nov16_HO3-->


