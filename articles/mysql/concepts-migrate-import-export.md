---
title: "Azure Database for MySQL でのインポートとエクスポート | Microsoft Docs"
description: "Azure Database for MySQL でのデータベースのインポートおよびエクスポートについて紹介します"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 582f83e3e80d855049abece42726748fcd113ab7
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-using-import-and-export"></a>インポートとエクスポートを使用した MySQL データベースの移行
このチュートリアルでは、MySQL Workbench を使用して、Azure MySQL データベースのデータをインポートおよびエクスポートする一般的な方法について説明します。 

## <a name="before-you-begin"></a>開始する前に
このハウツー ガイドの手順を実行するには、以下が必要です。
- Azure Database for MySQL サーバー ([最初の Azure MySQL データベースの設計](quickstart-create-mysql-server-database-using-azure-portal.md))
- MySQL Workbench ([MySQL Workbench のダウンロード](https://dev.mysql.com/downloads/workbench/))

## <a name="use-common-tools"></a>一般的なツールの使用
MySQL Workbench、Toad、Navicat などの一般的なツールを使用して、Azure Database for MySQL にリモートで接続し、データをインポートまたはエクスポートします。 こうしたツールは、インターネットに接続されたクライアント コンピューターで、Azure Database for MySQL に接続するときに使用します。 SSL で暗号化された接続を使ってセキュリティを強化するために、[Azure Database for MySQL での SSL 接続の構成](concepts-ssl-connection-security.md)に関するページもご覧ください。 Azure Database for MySQL に移行するとき、インポート ファイルおよびエクスポート ファイルをクラウドの特別な場所に移動する必要はありません。 

## <a name="create-a-database-on-the-target-azure-database-for-mysql-service"></a>対象 Azure Database for MySQL サービスでのデータベースの作成
MySQL Workbench、Toad、Navicat、または MySQL 用のサード パーティ製ツールを使用して、データの移行先 Azure Database for MySQL サーバーで空のデータベースを作成する必要があります。 データベースの名前は、ダンプされたデータが含まれるデータベースと同じにすることも、別の名前でデータベースを作成することもできます。

![Azure Database for MySQL の接続文字列](./media/concepts-migrate-import-export/p5.png)

![MySQL Workbench の接続文字列](./media/concepts-migrate-import-export/p4.png)

## <a name="import-and-export-using-mysql-workbench"></a>MySQL Workbench を使用したインポートおよびエクスポート
MySQL Workbench では、目的に応じて 2 つの方法でデータをインポートおよびエクスポートできます。 

## <a name="table-data-export-and-import-wizard-using-object-browser-context-menu"></a>オブジェクト ブラウザーのコンテキスト メニューを使用したテーブル データのエクスポート/インポート ウィザード
![オブジェクト ブラウザーのコンテキスト メニューを使用した MySQL Workbench のインポート/エクスポート](./media/concepts-migrate-import-export/p1.png)

このウィザードでは、CSV ファイルと JSON ファイルを使用してインポートおよびエクスポート操作を行うことができ、区切り記号、列の選択、エンコードの選択などの構成オプションを指定できます。 ウィザードはローカル、またはリモートで接続されている MySQL サーバーに対して実行でき、インポート操作には、テーブル、列、および型のマッピングが含まれます。 このウィザードには、オブジェクト ブラウザーのコンテキスト メニューからアクセスできます (テーブルを右クリックして、**[Table Data Export Wizard (テーブル データのエクスポート ウィザード)]** または **[Table Data Import Wizard (テーブル データのインポート ウィザード)]** を選択)。 

## <a name="table-data-export-wizard"></a>テーブル データのエクスポート ウィザード
次の例では、テーブルを CSV ファイルにエクスポートします。 
- エクスポート元のデータベースのテーブルを右クリックします。 
- **[Table Data Export Wizard (テーブル データのエクスポート ウィザード)]** を選択します。 エクスポートする列、行のオフセット (該当する場合)、および数 (該当する場合) を選択します。 
- [エクスポートするデータの選択] ウィンドウで **[次へ]** をクリックします。 ファイルのパス、ファイルの種類 (CSV または JSON)、行区切り記号、囲む対象の文字列、およびフィールド区切り記号を選択します。 
- [Select output file location (出力ファイルの場所の選択)] ウィンドウで **[次へ]** を選択し、[データのエクスポート] ウィンドウで [次へ] を選択します。


## <a name="table-data-import-wizard"></a>テーブル データのインポート ウィザード
次の例では、テーブルを CSV ファイルからインポートします。
- インポート先のデータベースのテーブルを右クリックします。 
- インポートする CSV ファイルを参照して選択し、[次へ] を選択します。 
- インポート先テーブル (新規または既存) を選択し、[Truncate table before import (インポート前にテーブルの切り詰め処理を行う)] チェック ボックスをオンまたはオフにして、[次へ] をクリックします。
- エンコードと、インポートする列を選択して、[次へ] を選択します。 
- [データのインポート] ウィンドウで [次へ] を選択すると、設定に従ってデータがインポートされます。

## <a name="sql-data-export-and-import-wizard-from-management-navigator"></a>管理ナビゲーターの SQL データのエクスポートとインポート ウィザード
このウィザードを使用して、MySQL Workbench または mysqldump コマンドで生成された SQL をエクスポートまたはインポートできます。 ウィザードにアクセスするには、ナビゲーター パネルで、またはメイン メニューからサーバーを選択し、[データのインポート] または [データのエクスポート] のいずれかをを選択します。 

## <a name="data-export"></a>データのエクスポート
![管理ナビゲーターを使用した MySQL Workbench のデータ エクスポート](./media/concepts-migrate-import-export/p2.png)

このタブでは、MySQL のデータをエクスポートできます。 
- エクスポートする各スキーマを選択し、必要に応じて各スキーマから特定のスキーマ オブジェクト/テーブルを選択して、エクスポートを生成します。 構成オプションには、プロジェクト フォルダーまたは自己完結型 SQL ファイルへのエクスポートが含まれており、必要に応じて、格納されているルーチンやイベントをダンプしたり、テーブル データをスキップしたりできます。 
- また、**[Export a Result Set (結果セットのエクスポート)]** を使用して、SQL エディターの特定の結果セットを、CSV、JSON、HTML、XML などの別の形式にエクスポートします。 
- エクスポートするデータベース オブジェクトを選択し、関連するオプションを構成します。 
- **[更新]** をクリックして、現在のオブジェクトを読み込みます。 
- 必要に応じて、[詳細オプション] タブを開きます。このタブでは、エクスポート操作を調整できます。 たとえば、テーブル ロックを追加したり、insert ステートメントではなく replace ステートメントを使用したり、バッククォート文字を含む識別子を引用したりします。 
- **[エクスポートの開始]** をクリックし、エクスポート処理を開始します。 


## <a name="data-import"></a>データのインポート
![管理ナビゲーターを使用した MySQL Workbench のデータ インポート](./media/concepts-migrate-import-export/p3.png)

このタブでは、データのエクスポート操作によってエクスポートされたデータ、または mysqldump コマンドによってエクスポートされた他のデータをインポートまたは復元できます。 
- プロジェクト フォルダーまたは自己完結型 SQL ファイルを選択するか、データのインポート先スキーマを選択するか、[新規] を選択して新しいスキーマを定義します。 
- **[インポートの開始]** をクリックしてインポート処理を開始します。これにより、設定に従ってインポートが行われます。

## <a name="next-steps"></a>次のステップ
このデータベース サービスを開始する方法がよくわからない場合は、次のトピックを参照してください。
-  [Azure Portal を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-portal.md) 
- [Azure CLI を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-cli.md)

