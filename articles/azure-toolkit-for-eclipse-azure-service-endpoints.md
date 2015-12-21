<properties
    pageTitle="Azure サービス エンドポイント"
    description="Azure Toolkit for Eclipse での Azure サービス エンドポイントの設定について説明します。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/19/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# Azure サービス エンドポイント #

Azure サービス エンドポイントは、アプリケーションがどこにデプロイされて管理されるかを決定します (グローバル Azure プラットフォーム、中国の 21Vianet が運営する Azure、プライベート Azure プラットフォームのいずれか)。 **[サービス エンドポイント]** ダイアログで、使用するサービス エンドポイントを指定できます。**[サービスエンド ポイント]** ダイアログを開くには、Eclipse で **[Window]**、**[Preferences]** の順にクリックし、**[Azure]** を展開し、**[Service Endpoints]** をクリックします。**[Active Set]** フィールドを設定することで、現在のワークスペース内の Azure プロジェクトで使用される Azure サービス エンドポイントが決まります。

**[Service Endpoints]** ダイアログを次に示します。

![][ic719493]

## サービス エンドポイントを設定するには ##

**[Service Endpoints]** ダイアログで、次のいずれかを行います。

* グローバル Azure プラットフォームを使用する場合は、**[Active Set]** ドロップダウン リストから **[windowsazure.com]** を選択し、**[OK]** をクリックします。
* 中国の 21Vianet が運営する Azure を使用する場合は、**[Active Set]** ドロップダウン リストから **[windowsazure.cn (China)]** を選択し、**[OK]** をクリックします。
* プライベート Azure プラットフォームを使用する場合:
    1. **[編集]** をクリックします。
    2. **[Service Endpoints]** ダイアログを閉じて基本設定ファイルを開くことを通知するダイアログ ボックスが表示されます。**[OK]** をクリックします。
    3. Preferencesets.xml ファイルに、新しい `preferenceset` 要素を作成します。この新しい要素の `name`、`blob`、`management`、`portalURL`、および `publishsettings` 属性を作成し、各属性にプライベート Azure プラットフォームに該当する値を追加します。既存の `preferenceset` 要素に指定されている値をテンプレートとして使用できます。**注**: `blob` 属性で使用する値には、URL に "blob" というテキストを含める必要があります。
    4. preferencesets.xml を保存して閉じます。
    5. **[Service Endpoints]** ダイアログを再度開きます。
    6. **[Active Set]** ドロップダウン リストから、作成したアクティブ セットを選択し、**[OK]** をクリックします。
    7. プライベート Azure プラットフォームの `preferenceset` 要素をを作成した後は、**[Services Endpoint]** ダイアログの **[Edit]** ボタンをクリックすることで、要素に割り当てられている値を変更できます。必要であれば、複数のプライベート Azure プラットフォームの `preferenceset` 要素を作成することもできます。

## 関連項目 ##

[Azure Toolkit for Eclipse][]

[Azure Toolkit for Eclipse のインストール][]

[Azure の Hello World アプリケーションを Eclipse で作成する][]

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター][]を参照してください。

<!-- URL List -->

[Azure Java デベロッパー センター]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure の Hello World アプリケーションを Eclipse で作成する]: http://go.microsoft.com/fwlink/?LinkID=699533
[Azure Toolkit for Eclipse のインストール]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png

<!---HONumber=AcomDC_1210_2015-->