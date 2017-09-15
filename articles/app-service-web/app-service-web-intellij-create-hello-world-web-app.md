---
title: "IntelliJ での基本的な Azure Web アプリの作成"
description: "このチュートリアルでは、Azure Toolkit for IntelliJ を使用して、Azure 用の Hello World Web アプリを作成する方法について説明します。"
services: app-service\web
documentationcenter: java
author: selvasingh
manager: routlaw
editor: 
ms.assetid: 75ce7b36-e3ae-491d-8305-4b42ce37db4e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/20/2017
ms.author: robmcm;asirveda
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: 260302a49c65106728e0aa8347e9fb89a8c8c270
ms.contentlocale: ja-jp
ms.lasthandoff: 09/02/2017

---
# <a name="create-a-basic-azure-web-app-in-intellij"></a>IntelliJ での基本的な Azure Web アプリの作成

このチュートリアルでは、[Azure Toolkit for IntelliJ] を使用して、Web アプリとして基本的な Hello World アプリケーションを作成し、Azure にデプロイする方法について説明します。

> [!NOTE]
> 
> Azure Toolkit for IntelliJ は 2017 年 8 月に更新され、別のワークフローが導入されました。 このことを念頭に、この記事には次の 2 つのセクションがあります。
>
> * 最初のセクションでは、2017 年 8 月 (またはそれ以降) のバージョンの Azure Toolkit for IntelliJ を使用して Hello World Web アプリを作成する方法を示します。
>
> * 2 番目のセクションでは、2017 年 4 月 (またはそれ以前) のバージョンのツールキットを使用して Hello World Web アプリを作成する方法を示します。
> 

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="create-a-hello-world-web-app-by-using-the-version-307-or-later-toolkit"></a>バージョン 3.0.7 以降のツールキットを使用して Hello World Web アプリを作成する

### <a name="create-a-new-web-app-project"></a>新しい Web アプリ プロジェクトの作成

1. 「Azure Toolkit for IntelliJ のサインイン手順」の記事の手順を使用して、IntelliJ を起動して Azure アカウントにサインインします。

1. **[File]\(ファイル\)** メニューの **[New]\(新規作成\)** をクリックし、**[Project]\(プロジェクト\)** をクリックします。
   
   ![新しいプロジェクトの作成][file-new-project]

1. **[New Project]\(新しいプロジェクトの作成\)** ダイアログ ボックスで、**[Maven]**、**[maven-archetype-webapp]** の順に選択し、**[Next]\(次へ\)** をクリックします。
   
   ![Maven アーキタイプ Web アプリの選択][maven-archetype-webapp]
   
1. Web アプリの **[GroupId]** と **[ArtifactId]** を指定し、**[Next]\(次へ\)** をクリックします。
   
   ![GroupId と ArtifactId の指定][groupid-and-artifactid]

1. Maven 設定をカスタマイズするか、既定の設定をそのまま使用し、**[Next]\(次へ\)** をクリックします。
   
   ![Maven 設定の指定][maven-options]

1. プロジェクト名と場所を指定し、**[Finish]\(完了\)** をクリックします。
   
   ![プロジェクト名の指定][project-name]

1. IntelliJ のプロジェクト エクスプローラー ビューで、**[src]**、**[main]**、**[webapp]** の順に展開し、**index.jsp** をダブルクリックします。
   
   ![インデックス ページを開く][open-index-page]

1. index.jsp ファイルが IntelliJ で開いたら、"**Hello World!**" を動的に表示するためのテキストを 既存の `<body>` 要素に追加します。 更新された `<body>` コンテンツは、次のようになります。
   
   ```java
   <body><b><% out.println("Hello World!"); %></b></body>
   ``` 

   ![インデックス ページの編集][edit-index-page]

1. index.jsp を保存します。

### <a name="deploy-your-web-app-to-azure"></a>Azure への Web アプリのデプロイ

1. IntelliJ のプロジェクト エクスプローラー ビューでプロジェクトを右クリックし、**[Azure]**、**[Run on Web App]\(Web アプリで実行\)** の順に選択します。
   
   ![[Run on Web App]\(Web アプリで実行\) メニュー][run-on-web-app-menu]

1. [Run on Web App]\(Web アプリで実行\) ダイアログ ボックスで、次のいずれかのオプションを選択できます。

   * 既存の Web アプリ (存在する場合) を選択して **[Run]\(実行\)** をクリックする。

      ![[Run on Web App]\(Web アプリで実行\) ダイアログ ボックス][run-on-web-app-dialog]

   * **[Create New Web App]\(新しい Web アプリを作成する\)**をクリックする。 新しい Web アプリを作成する場合は、Web アプリに必要な情報を指定し、**[Run]\(実行\)** をクリックします。

      ![新しい Web アプリの作成][create-new-web-app-dialog]

1. Web アプリが正常にデプロイされると、その Web アプリの URL を含むステータス メッセージがツールキットに表示されます。

   ![デプロイに成功][successfully-deployed]

1. ステータス メッセージに表示されたリンクを使用して、Web アプリを参照できます。

   ![Web アプリの参照][browse-web-app]

1. Web アプリを発行した後、使用した設定が既定の設定として保存されます。ツール バーの緑色矢印のアイコンをクリックすることで、Azure でアプリケーションを実行できます。 設定を変更するには、Web アプリのドロップダウン メニューをクリックし、**[Edit Configurations]\(構成の編集\)** をクリックします。

   ![[Edit Configurations]\(構成の編集\) メニュー][edit-configuration-menu]

1. **[Run/Debug Configurations]\(構成の実行/デバッグ\)** ダイアログ ボックスが表示されたら、既定の設定を変更し、**[OK]** をクリックします。

   ![[Edit Configurations]\(構成の編集\) ダイアログ ボックス][edit-configuration-dialog]

<hr />

## <a name="create-a-hello-world-web-app-by-using-the-version-306-or-earlier-toolkit"></a>バージョン 3.0.6 以前のツールキットを使用して Hello World Web アプリを作成する

### <a name="create-a-new-web-app-project"></a>新しい Web アプリ プロジェクトの作成

1. IntelliJ を起動し、**[File (ファイル)]** メニュー、**[New (新規)]**、**[Project (プロジェクト)]** の順にクリックします。
   
   ![[File (ファイル)]、[New (新規)]、[Project (プロジェクト)]][02]

2. **[New Project]\(新しいプロジェクトの作成\)** ダイアログ ボックスで、**[Java]**、**[Web Application]\(Web アプリケーション\)**、**[Next]\(次へ\)** の順にクリックし、Project SDK を追加します。
   
   ![[新しいプロジェクト] ダイアログ][03a]
   
3. [Select Home Directory for JDK (JDK のホーム ディレクトリの選択)] ダイアログ ボックスで JDK がインストールされているフォルダーを選択し、**[OK]** をクリックします。 [New Project (新しいプロジェクト)] ダイアログ ボックスで **[Next (次へ)]** をクリックして続行します。
   
   ![JDK ホーム ディレクトリを指定する][03b]

4. このチュートリアルでは、プロジェクトに **Java-Web-App-On-Azure** という名前を付け、**[Finish (完了)]** をクリックします。
   
   ![[新しいプロジェクト] ダイアログ][04]

5. IntelliJ の [Project Explorer (プロジェクト エクスプローラー)] ビューで、**[Java-Web-App-On-Azure]**、**[Web]** の順に展開し、**index.jsp** をダブルクリックします。
   
   ![インデックスを開くページ][05c]

6. index.jsp ファイルが IntelliJ で開いたら、"**Hello World!**" を動的に表示するためのテキストを 既存の `<body>` 要素に追加します。 更新された `<body>` コンテンツは、次のようになります。
   
   ```java
   <body><b><% out.println("Hello World!"); %></b></body>
   ```

7. index.jsp を保存します。

### <a name="deploy-your-web-app-to-azure"></a>Azure への Web アプリのデプロイ
Java Web アプリを Azure にデプロイする方法はいくつかあります。 このチュートリアルでは、アプリケーションを Azure Web アプリ コンテナーにデプロイするという最も簡単な方法について説明します。特殊なプロジェクトの種類や追加のツールは必要ありません。 JDK と Web コンテナー ソフトウェアは Azure から提供されるので、自分でアップロードする必要はありません。必要なものは Java Web アプリのみです。 結果として、アプリケーションの発行プロセスにかかる時間は分単位ではなく、秒単位になります。

アプリケーションを発行する前に、まずモジュール設定を構成する必要があります。 そのためには、次の手順を実行してください。

1. IntelliJ の Project Explorer で、 **Java-Web-App-On-Azure** プロジェクトを右クリックします。 コンテキスト メニューが表示されたら、**[Open Module Settings (モジュール設定を開く)]** をクリックします。

   ![モジュール設定を開く][05a]

2. [プロジェクト構造] ダイアログが表示されます。

   a. **[プロジェクト設定]** の一覧で、**[Artifacts (アーティファクト)]** をクリックします。

   b. **[名前]** ボックスのアーティファクトの名前を、空白または特殊文字が含まれないように変更します。この名前が Uniform Resource Identifier (URI) で使用されるため、変更が必要になります。

   c. **[タイプ]** を **[Web Application: Archive (Web アプリケーション: アーカイブ)]** に変更します。

   d. **[OK]** をクリックして、[プロジェクト構造] ダイアログ ボックスを閉じます。

   ![モジュール設定を開く][05b]

モジュール設定を構成したら、次の手順を使用してアプリケーションを Azure に発行できます。

1. IntelliJ の Project Explorer で、 **Java-Web-App-On-Azure** プロジェクトを右クリックします。 コンテキスト メニューが表示されたら、**[Azure]**、**[Publish as Azure Web App (Azure Web アプリとして発行)]** の順に選択します。
   
   ![Azure に発行するためのコンテキスト メニュー][06]

2. まだ IntelliJ から Azure にサインインしていない場合、Azure アカウントにサインインするように求められます。 (複数の Azure アカウントがある場合、サインイン プロセス中に同じようなプロンプトが何度も表示されることがあります。 このような状況の場合、次のサインイン手順を続行します。)
   
   ![Azure のログイン ダイアログ][07]

3. Azure アカウントに正常にサインインすると、**[サブスクリプションの管理]** ダイアログ ボックスに、資格情報に関連付けられたサブスクリプションの一覧が表示されます。 (複数のサブスクリプションが表示された場合、その一部のみを使うには、使わないサブスクリプションのチェックボックスを必要に応じてオフにします)。サブスクリプションを選択したら、 **[閉じる]**をクリックします。
   
   ![サブスクリプションの管理][08]

4. **[Deploy to Azure Web App Container (Azure Web アプリ コンテナーにデプロイ)]** ダイアログ ボックスを開くと、以前に作成した Web アプリ コンテナーがすべて表示されます。コンテナーを作成していない場合、一覧は空欄です。
   
   ![アプリ コンテナー][09]

5. 以前に Azure Web アプリ コンテナーを作成していない場合、またはアプリケーションを新しいコンテナーに発行する場合は、次の手順を実行します。 作成済みの場合は、既存の Web アプリ コンテナーを選択し、以下の手順 6. に進みます。
   
   a. **+** 記号をクリックします。
      
      ![アプリ コンテナーの追加][10]

   b. **[New Web App Container (新しい Web アプリ コンテナー)]** ダイアログ ボックスが表示されます。このコンテナーは、後続のいくつかの手順で使用します。
      
      ![新しいアプリ コンテナー][11a]
   
   c. Web アプリ コンテナーの **[DNS Label (DNS ラベル)]** を入力します。これで、Azure の Web アプリケーションについて、ホスト URL のリーフ DNS ラベルが構成されます。 この名前は使用可能であり、Azure Web アプリの名前付け要件に準拠している必要があります。

   d. **[Web Container (Web コンテナー)]** ドロップダウン メニューで、アプリケーションに適したソフトウェアを選択します。
      
      現在、Tomcat 8、Tomcat 7 または Jetty 9 から選択することができます。 選択したソフトウェアの最新の配信が Azure で提供されます。また、これは Oracle によって作成され、Azure で提供された JDK 8 の最新の配信で実行されます。

   e. **[サブスクリプション]** ドロップダウン メニューで、このデプロイに使用するサブスクリプションを選択します。

   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Resource Group (リソース グループ)]** ドロップダウン メニューで、Web アプリに関連付けるリソース グループを選択します。 (Azure リソース グループを使用すると、関連リソースをグループ化できるため、たとえば、リソースをまとめて削除できます。)
      
      (所有している場合は) 既存のリソース グループを選択して、下記のステップ g にスキップするか、以下のステップに従って、新しいリソース グループを作成します。
      
      * **[リソース グループ]** ドロップダウン メニューから、**&lt;&lt;新規リソース グループの作成&gt;&gt;** を選択します。
      * **[New Resource Group (新しいリソース グループ)]** ダイアログ ボックスが表示されます。
        
         ![New Resource Group][12]

      * **[Name]\(名前\)** テキスト ボックスに、新しいリソース グループの名前を入力します。
      * **[Region]\(リージョン\)** ドロップダウン メニューで、リソース グループに適した Azure データ センターの場所を選択します。
      * **[OK]**をクリックします。

   g. **[App Service Plan (App Service プラン)]** ドロップダウン メニューには、選択したリソース グループに関連付けられた App Service プランが表示されます。 (App Service プランでは、Web アプリの場所、価格レベル、およびコンピューティング インスタンス サイズなどの情報を指定します。 単一の App Service プランを複数の Web Apps に使用できます。そのため、App Service プランは、特定の Web アプリのデプロイとは別に保持されます。)
      
      (所有している場合は) 既存の App Service プランを選択して、下記のステップ h にスキップするか、以下のステップに従って、新しい App Service プランを作成します。
      
      * **[App Service プラン]** ドロップダウン メニューから、**&lt;&lt;新しい App Service プランの作成&gt;&gt;** を選択します。
      * **[New App Service Plan (新しい App Service プラン)]** ダイアログ ボックスが表示されます。
        
         ![新しい App Service プラン][13]

      * **[Name]\(名前\)** ボックスに、新しい App Service プランの名前を入力します。
      * **[Location]\(場所\)** ドロップダウン メニューで、プランに適した Azure データ センターの場所を選択します。
      * **[Pricing Tier]\(価格レベル\)** ドロップダウン メニューで、プランに適した価格を選択します。 テスト目的の場合は、 **[Free]**を選択できます。
      * **[Instance Size]\(インスタンス サイズ\)** ドロップダウン メニューで、プランに適したインスタンス サイズを選択します。 テスト目的の場合は、 **[Small]**を選択できます。
      * **[OK]**をクリックします。

   h. (オプション) 既定では、Java 8 の最新のディストリビューションは、Azure によって Web アプリ コンテナーに JVM として自動的にデプロイされます。 ただし、JVM の別のバージョンおよびディストリビューションを選択することができます。 そのためには、次の手順を実行してください。
      
      * **[New Web App Container (新しい Web アプリ コンテナー)]** ダイアログ ボックスの **[JDK]** タブをクリックします。
      * 次のオプションのいずれかを選択できます。
        
         * Azure によって提供される既定の JDK をデプロイする
         * Azure で利用可能な追加の JDK のドロップダウン リストからサード パーティの JDK をデプロイする
         * カスタム JDK をデプロイする。カスタム JDK は、ZIP ファイルとしてパッケージ化され、一般に入手可能であるか、Azure ストレージ アカウントに格納されている必要があります。
        
      ![[New Web App Container (新しい Web アプリ コンテナー)] の [JDK] タブ][11b]

   i. これらの手順をすべて完了すると、[New Web App Container] \(新しい Web アプリ コンテナー) ダイアログ ボックスは次の図のようになります。
      
      ![新しいアプリ コンテナー][14]
   
   j. **[OK]** をクリックすると、新しい Web アプリ コンテナーの作成が完了します。
       
      数秒待つと Web アプリ コンテナーの一覧が更新されます。一覧で新しく作成した Web アプリ コンテナーが選択されています。

6. これで、Azure への Web アプリの初期デプロイを完了する準備ができました。**[OK]** をクリックして、Java アプリケーションを選択した Web アプリ コンテナーにデプロイします。 既定では、アプリケーションはアプリケーション サーバーのサブディレクトリとしてデプロイされます。 ルート アプリケーションとしてデプロイする場合、**[Deploy to root (ルートにデプロイ)]** チェック ボックスをオンにして **[OK]** をクリックします。
   
   ![Azure へのデプロイ][15]

7. **[Azure Activity Log (Azure アクティビティ ログ)]** ビューが開き、Web アプリのデプロイの状態が表示されます。
   
   ![進捗状況インジケーター][16]
   
   Web アプリを Azure にデプロイするプロセスは、わずか数秒で完了します。 アプリケーションの準備ができると、 **[Status]\(状態\)** 列に **[Published]\(発行済み\)** というリンクが表示されます。 リンクをクリックすると、デプロイされた Web アプリのホーム ページに移動します。また、次のセクションの手順を使用して Web アプリを参照することもできます。

### <a name="browsing-to-your-web-app-on-azure"></a>Azure の Web アプリの参照
**Azure Explorer** ビューを使用すると、Azure の Web アプリを参照できます。

**[Azure Explorer (Azure エクスプローラー)]** ビューがまだ開いていない場合、IntelliJ の **[View (ビュー)]** メニュー、**[Tool Windows (ツール ウィンドウ)]**、**[Service Explorer (サービス エクスプローラー)]** の順にクリックして開きます。 まだログインしていない場合は、ログインするように求められます。

**[Azure Explorer]** ビューが表示されたら、次の手順に従って Web アプリを参照します。 

1. **[Azure]** ノードを展開します。
2. **[Web Apps (Web アプリ)]** ノードを展開します。 
3. 目的の Web App を右クリックします。
4. コンテキスト メニューが表示されたら、 **[Open in Browser]**(ブラウザーで開く) をクリックします。
   
   ![Web アプリの参照][17]

### <a name="updating-your-web-app"></a>Web アプリの更新
既存の実行中の Azure Web アプリを更新するプロセスは短時間で簡単です。更新には 2 つのオプションがあります。

* 既存の Java Web アプリのデプロイを更新できます。
* 同じ Web アプリ コンテナーに追加の Java アプリケーションを発行できます。

いずれの場合でもプロセスは同じで、かかる時間は数秒です。

1. IntelliJ の Project Explorer で、更新する Java アプリケーションを右クリックするか、既存の Web アプリ コンテナーに追加します。
2. コンテキスト メニューが表示されたら、**[Azure]**、**[Publish as Azure Web App (Azure Web アプリとして発行)]** の順に選択します。
3. 既にログインしているので、既存の Web アプリ コンテナーの一覧が表示されます。 Java アプリケーションを発行または再発行するコンテナーを選択し、 **[OK]**をクリックします。

数秒後、**[Azure Activity Log (Azure アクティビティ ログ)]** ビューに更新されたデプロイが **[Published (発行済み)]** と表示され、Web ブラウザーで更新されたアプリケーションを確認できます。

### <a name="starting-stopping-or-restarting-an-existing-web-app"></a>既存の Web アプリの起動、停止、再起動
既存の Azure Web アプリ コンテナー (コンテナー内にデプロイされているすべての Java アプリケーションを含む) を起動または停止するには、 **[Azure Explorer]** (Azure Explorer) ビューを使用できます。

**[Azure Explorer (Azure エクスプローラー)]** ビューがまだ開いていない場合、IntelliJ の **[View (ビュー)]** メニュー、**[Tool Windows (ツール ウィンドウ)]**、**[Service Explorer (サービス エクスプローラー)]** の順にクリックして開きます。 まだログインしていない場合は、ログインするように求められます。

**[Azure Explorer]** ビューが表示されたら、次の手順に従って Web アプリを起動また停止します。 

1. **[Azure]** ノードを展開します。
2. **[Web Apps (Web アプリ)]** ノードを展開します。 
3. 目的の Web App を右クリックします。
4. コンテキスト メニューが表示されたら、**[Start (起動)]****[Stop (停止)]**、または **[Restart (再起動)]** をクリックします。 メニュー項目はコンテキストに依存します。つまり、Web アプリが実行しているときは停止操作のみ、Web アプリが現在実行されていないときは起動操作のみを行うことができます。
   
   ![Web アプリの停止][18]

## <a name="next-steps"></a>次のステップ

[!INCLUDE [azure-toolkit-additional-resources](../../includes/azure-toolkit-additional-resources.md)]

Azure Web Apps の作成の詳細については、「 [Web Apps の概要]」を参照してください。

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ../azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World Web App for Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[What's New in the Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[What's New in the Azure Toolkit for IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Web Apps の概要]: ./app-service-web-overview.md
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/

<!-- IMG List -->

[01]: ./media/app-service-web-intellij-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-intellij-create-hello-world-web-app/02-File-New-Project.png
[03a]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-Dialog.png
[03b]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-SDK-Dialog.png
[04]: ./media/app-service-web-intellij-create-hello-world-web-app/04-New-Project-Dialog.png
[05a]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Module-Settings.png
[05b]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Project-Structure-Dialog.png
[05c]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Index-Page.png
[06]: ./media/app-service-web-intellij-create-hello-world-web-app/06-Azure-Publish-Context-Menu.png
[07]: ./media/app-service-web-intellij-create-hello-world-web-app/07-Azure-Log-In-Dialog.png
[08]: ./media/app-service-web-intellij-create-hello-world-web-app/08-Manage-Subscriptions.png
[09]: ./media/app-service-web-intellij-create-hello-world-web-app/09-App-Containers.png
[10]: ./media/app-service-web-intellij-create-hello-world-web-app/10-Add-App-Container.png
[11a]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[11b]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container-JDK-Tab.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png

[file-new-project]: ./media/app-service-web-intellij-create-hello-world-web-app/file-new-project.png
[maven-archetype-webapp]: ./media/app-service-web-intellij-create-hello-world-web-app/maven-archetype-webapp.png
[groupid-and-artifactid]: ./media/app-service-web-intellij-create-hello-world-web-app/groupid-and-artifactid.png
[maven-options]: ./media/app-service-web-intellij-create-hello-world-web-app/maven-options.png
[project-name]: ./media/app-service-web-intellij-create-hello-world-web-app/project-name.png
[open-index-page]: ./media/app-service-web-intellij-create-hello-world-web-app/open-index-page.png
[edit-index-page]: ./media/app-service-web-intellij-create-hello-world-web-app/edit-index-page.png
[run-on-web-app-menu]: ./media/app-service-web-intellij-create-hello-world-web-app/run-on-web-app-menu.png
[run-on-web-app-dialog]: ./media/app-service-web-intellij-create-hello-world-web-app/run-on-web-app-dialog.png
[create-new-web-app-dialog]: ./media/app-service-web-intellij-create-hello-world-web-app/create-new-web-app-dialog.png
[successfully-deployed]: ./media/app-service-web-intellij-create-hello-world-web-app/successfully-deployed.png
[browse-web-app]: ./media/app-service-web-intellij-create-hello-world-web-app/browse-web-app.png
[edit-configuration-menu]: ./media/app-service-web-intellij-create-hello-world-web-app/edit-configuration-menu.png
[edit-configuration-dialog]: ./media/app-service-web-intellij-create-hello-world-web-app/edit-configuration-dialog.png

