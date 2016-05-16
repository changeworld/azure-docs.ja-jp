<properties
    pageTitle="Java 用 Azure ライブラリ パッケージの Javadoc コンテンツの Eclipse での表示"
    description="Azure ライブラリの Javadoc コンテンツを Eclipse で表示する方法"
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
    ms.date="05/04/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# Java 用 Azure ライブラリ パッケージの Javadoc コンテンツの Eclipse での表示 #

Java 用 Azure ライブラリの Javadoc コンテンツは、Javadoc コンテンツを Java 用 Azure ライブラリに関連付けることで、Eclipse 環境内で表示できます。次の手順は、Eclipse 内でこの機能を使用する方法を示しています。

この手順は、Java 用 Azure ライブラリをビルド パスに既に追加していることを前提としています。

## Java 用 Azure ライブラリの Javadoc コンテンツを Eclipse で表示するには ##

* Eclipse の Project Explorer で、プロジェクトの **[Referenced Libraries]** セクションにある Java JAR 用 Azure ライブラリ(例: **microsoft Microsoft Azure api 0.1.0.jar** (インストールされているバージョンによって、バージョン番号が異なることがあります)) のコンテキスト メニューを開きます。
* **[プロパティ]** をクリックします。
* **[Properties]** ダイアログの左側のウィンドウで、**[Javadoc Location]** をクリックします。**[Javadoc Location]** ダイアログが表示されます。
* **[Javadoc URL]** または **[Javadoc in archive]** を指定できます。
    * **[Javadoc URL]** を指定する場合は、****http://dl.windowsazure.com/javadoc** や ****http://dl.windowsazure.com/storage/javadoc** などの URL を使用します。
* **[Javadoc in archive]** を使用する場合は、外部ファイルまたはワークスペース ファイルを指定できます。選択したら、必要に応じて参照または検証します。次の例では、Java 用 Azure ライブラリを、**c:\\MyAzureJARs** という名前のローカル フォルダーにダウンロードされている対応する Javadoc JAR に関連付けています。![][ic553487]
* *省略可能*: **[Validate]** をクリックします。Javadoc JAR に関する潜在的な問題があれば、ここに表示されます。
* **[OK]** をクリックします。

ライブラリに関連付けると、Javadoc コンテンツが Eclipse IDE 内で表示されます。たとえば、コードで `CloudBlockBlob` 型の `blob` が定義されている場合、コードに「`blob.acquireLease`」と入力すると、次のような Javadoc コンテンツが表示されます。

![][ic553488]

## 関連項目 ##

[Azure Toolkit for Eclipse][]

[Azure の Hello World アプリケーションを Eclipse で作成する][]

[Azure Toolkit for Eclipse のインストール][]

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター][]を参照してください。

<!-- URL List -->

[Azure Java デベロッパー センター]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure の Hello World アプリケーションを Eclipse で作成する]: http://go.microsoft.com/fwlink/?LinkID=699533
[Azure Toolkit for Eclipse のインストール]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png

<!---HONumber=AcomDC_0504_2016-->