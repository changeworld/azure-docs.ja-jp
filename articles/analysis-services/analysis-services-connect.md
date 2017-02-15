---
title: "Azure Analysis Services からデータを取得 | Microsoft Docs"
description: "Azure の Analysis Services サーバーに接続してデータを取得する方法を説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: b37f70a0-9166-4173-932d-935d769539d1
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/28/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 6b65689c75247af81e916730f125ff39bb49489f


---
# <a name="get-data-from-azure-analysis-services"></a>Azure Analysis Services からデータを取得
Azure でサーバーを作成して、表形式モデルを展開すると、社内のユーザーはすぐに接続してデータの分析を開始できるようになります。

Azure Analysis Services では、xmla 経由でサーバーに接続するために、[更新されたオブジェクト モデル](#client-libraries) (TOM、AMO、Adomd.Net、または MSOLAP) を使用してクライアントの接続がサポートされます。 たとえば、Power BI、Power BI Desktop、Excel、またはオブジェクト モデルに対応する任意のサード パーティ クライアント アプリケーションです。

## <a name="server-name"></a>サーバー名
Analysis Services サーバーを Azure に作成するとき、一意の名前と、サーバーが作成されるリージョンを指定します。 接続にサーバー名を指定する場合、サーバーの命名方法は次のとおりです。

```
<protocol>://<region>/<servername>
```
 ここで、protocol は文字列 **asazure**、region はサーバーが作成されたリージョンの Uri (たとえば、米国西部の場合は westus.asazure.windows.net)、servername はそのリージョンの一意のサーバー名です。

## <a name="get-the-server-name"></a>サーバー名の取得
接続する前に、サーバー名を取得する必要があります。 **Azure Portal** でサーバーを選び、**[概要]** > **[サーバー名]** のサーバー名全体をコピーします。 社内の他のユーザーもこのサーバーに接続する場合は、このサーバー名をそれらのユーザーと共有してください。 サーバー名を指定するときは、パス全体を使用する必要があります。

![Azure でサーバー名を取得する](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop での接続
> [!NOTE]
> この機能はプレビューです。
> 
> 

1. [Power BI Desktop](https://powerbi.microsoft.com/desktop/) で、**[データの取得]** > **[データベース]** > **[Azure Analysis Services]** をクリックします。
2. **[サーバー]** に、クリップボードからサーバー名を貼り付けます。
3. **[データベース]** には、接続先の表形式モデル データベースまたはパースペクティブの名前がわかっている場合はその名前を貼り付けます。 それ以外の場合、このフィールドは空にしておくことができます。 次の画面でデータベースまたはパースペクティブを選択できます。
4. 既定の **[ライブ接続]** オプションが選択された状態のままで、**[接続]** をクリックします。 アカウントの入力を求められた場合は、会社のアカウントを入力します。
5. **[ナビゲーター]** で、サーバーを展開し、接続先のモデルまたはパースペクティブを選択してから **[接続]** をクリックします。 モデルまたはパースペクティブを 1 回クリックすると、そのビューのすべてのオブジェクトが表示されます。

## <a name="connect-in-power-bi"></a>Power BI での接続
1. サーバー上のモデルにライブ接続できる Power BI Desktop ファイルを作成します。
2. [Power BI](https://powerbi.microsoft.com) で、**[データの取得]** > **[ファイル]** をクリックします。 ファイルを検索して選択します。

## <a name="connect-in-excel"></a>Excel での接続
Excel での Azure Analysis Services サーバーへの接続は、Excel 2016 の Get Data または以前のバージョンの Power Query を使用してサポートされます。 [MSOLAP.7 プロバイダー](https://aka.ms/msolap)が必要です。 Power Pivot での [テーブルのインポート] ウィザードを使用した接続はサポートされません。

1. Excel 2016 では、**[データ]** リボンで **[外部データの取り込み]** > **[その他のデータ ソース]** > **[From Analysis Services (Analysis Services)]** をクリックします。
2. データ接続ウィザードで、**[サーバー名]** にクリップボードのサーバー名を貼り付けます。 その後、**[ログオン資格情報]** で **[以下のユーザー名とパスワードを使用する]** を選択し、会社でのユーザー名 (たとえば nancy@adventureworks.com,) とパスワードを入力します。
   
    ![Excel での接続、ログオン](./media/analysis-services-connect/aas-connect-excel-logon.png)
3. **[データベースとテーブルの選択]** で、データベースとモデルまたはパースペクティブを選択し、**[完了]** をクリックします。
   
    ![Excel での接続、モデルの選択](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>接続文字列
表形式オブジェクト モデルを使用して Azure Analysis Services に接続するときは、次の接続文字列の形式を使用します。

###### <a name="integrated-azure-active-directory-authentication"></a>統合 Azure Active Directory 認証
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
統合認証では、Azure Active Directory 資格情報キャッシュが使用されます (ある場合)。 ない場合は、Azure ログイン ウィンドウが表示されます。

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>ユーザー名とパスワードによる Azure Active Directory の認証
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="client-libraries"></a>クライアント ライブラリ
Azure Analysis Services に Excel または他のインターフェイス (TOM、AsCmd、ADOMD.NET など) から接続するとき、場合によっては、最新のプロバイダー クライアント ライブラリをインストールする必要があります。 最新のライブラリは次で入手してください。  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>次のステップ
[サーバーの管理](analysis-services-manage.md)




<!--HONumber=Nov16_HO3-->


