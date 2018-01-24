---
title: "SQL Data Warehouse 用に Visual Studio と SSDT をインストールする | Microsoft Docs"
description: "Azure SQL Data Warehouse 用に Visual Studio と SQL Server Development Tools (SSDT) をインストールします"
services: sql-data-warehouse
documentationcenter: NA
author: antvgski
manager: jhubbard
editor: 
ms.assetid: 0ed9b406-9b42-4fe6-b963-fe0a5b48aac1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: connect
ms.date: 12/14/2017
ms.author: kevin;barbkess
ms.openlocfilehash: e8170eefb2e359719684e08749180a4e7784f9b6
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2017
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>SQL Data Warehouse 用に Visual Studio と SSDT をインストールする
SQL Data Warehouse 用のアプリケーションを開発するには、最新バージョンの Visual Studio を、最新バージョンの SQL Server Data Tools (SSDT) と組み合わせて使うことをお勧めします。  SSDT がインストールされている Visual Studio 2013 Update 5 も、下位互換性のためにサポートされています。  

SSDT がインストールされている Visual Studio を使用すると、SQL Server オブジェクト エクスプローラーを使用して、SQL Data Warehouse のテーブル、ビュー、ストアド プロシージャ、その他の多くのオブジェクトを視覚的に調査したり、クエリを実行したりすることができます。

> [!NOTE]
> SQL Data Warehouse では、Visual Studio データベース プロジェクトがまだサポートされていません。  この機能は、今後のバージョンで追加されます。 この機能の定期的な更新を受け取りたい場合、[UserVoice] で投票してください。
> 
> 

## <a name="step-1-install-visual-studio"></a>手順 1: Visual Studio のインストール
Visual Studio をダウンロードしてインストールするには、以下のリンクをクリックしてください。 既に Visual Studio 2013 以降がインストールされている場合は、手順 2. に進んで SSDT をインストールします。

1. [Visual Studio をダウンロードします][]。
2. MSDN の [Visual Studio のインストール][Installing Visual Studio] ガイドに従ってインストールし、既定の構成を選択します。

## <a name="step-2-install-ssdt"></a>手順 2: SSDT のインストール
SSDT for Visual Studio をインストールするには、次の手順に従って Visual Studio 内から SSDT の更新プログラムをチェックします。

1. Visual Studio で、**[ツール]** / **[拡張機能と更新プログラム…]** をクリックします。 / **更新プログラム**
2. **[製品の更新プログラム]** を選択し、**[データベース ツール用の Microsoft SQL Server 更新プログラム]** を探します。

更新プログラムが見つからない場合は、最新のバージョンが既にインストールされています。  SSDT がインストールされていることを確認するには、**[ヘルプ]** / **[Microsoft Visual Studio のバージョン情報]** をクリックして表示される一覧の中から [SQL Server Data Tools] を探します。  SSDT の最新バージョンは、14.0.60525.0 です。  インストールするオプションを Visual Studio から使用できない場合は、代わりに [SSDT のダウンロード][SSDT Download] ページにアクセスし、SSDT を手動でダウンロードしてインストールします。

## <a name="next-steps"></a>次の手順
これで、最新バージョンの SSDT がインストールされたので、SQL Data Warehouse に[接続][connect]できるようになりました。

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Visual Studio をダウンロードします]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
