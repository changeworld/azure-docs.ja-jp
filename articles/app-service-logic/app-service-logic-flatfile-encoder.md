<properties 
   pageTitle="ロジック アプリでの BizTalk Flat File Encoder の使用 | Microsoft Azure" 
   description="BizTalk Flat File Encoder API アプリまたはコネクタ" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps" 	
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# BizTalk Flat File Encoder

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

BizTalk Flat File Encoder コネクタを使用して、フラット ファイル データ (Excel ファイルや CSV ファイルなど) と XML データの相互運用を行います。フラット ファイル インスタンスを XML に、またはその逆方向に変換できます。

##BizTalk Flat File Encoder の使用
BizTalk Flat File Encoder を使用するには、まず、BizTalk Flat File Encoder API アプリのインスタンスを作成する必要があります。これは、ロジック アプリを作成するときにインラインで、または Azure Marketplace から BizTalk Flat File Encoder API アプリを選択することによって、行うことができます。Azure Marketplace から BizTalk Flat File Encoder API アプリを作成する手順を次に示します。
1. Azure ポータル (http://portal.azure.com) にログインします。
2. [新規]、[Marketplace]、[すべて] の順に選択します。
3. 検索ボックスで "BizTalk Flat File Encoder" を検索します。
4. 結果の一覧から BizTalk Flat File Encoder を選択します。
5. [作成] を選択し、名前とその他の必要な情報を指定します。
6. [作成] を選択します。スタート ページにリダイレクトされます。そこでは、作成の進行状況を確認できます。作成完了までしばらく時間がかかる場合があります。完了すると、通知が届きます。

###BizTalk Flat File Encoder の構成
API アプリの作成が完了すると、そのアプリは、Azure ポータルのスタート ページから直接起動することも、ロジック アプリの作成時にデザイナー画面から起動することもできます。

Azure のスタート ページから API アプリを起動するには、API アプリの作成時に BizTalk Flat File Encoder に指定した名前を入力して、起動するアプリを検索することができます。手順は次のとおりです。
1. Auzre ポータルの検索ボックスに BizTalk Flat File Encoder の名前を入力し、それを検索します。
2. 次に一覧から BizTalk Flat File Encoder を選択します。API アプリのブレードが開きます。このブレードで BizTalk Flat File Encoder API アプリを構成することができます。構成を開始するには、次の手順に従ってスキーマを追加する必要があります。
1. [スキーマ] コンポーネントを選択します。![BizTalk Flat File Encoder のスキーマ部分][2]
2. 表示された [スキーマ] ブレードで [新規追加] を選択します。![BizTalk Flat File Encoder のアクション一覧][7]
3. 3 つのオプションのいずれかを選択し、スキーマを提供します。対象となるオプションは、[新しいスキーマのアップロード]、[JSON から生成]、[フラット ファイルから生成] の 3 つです。![BizTalk Flat File Encoder のアクション一覧][8]
4. 前の手順での選択に基づいて、スキーマを提供する手順を実行します。スキーマのアップロードが完了したことが表示されます。![BizTalk Flat File Encoder のアクション一覧][9]

###デザイン画面での BizTalk Flat File Encoder の使用
これで Biztalk Flat File Encoder の構成が完了したので、それをロジック アプリで使用します。使用を開始するには、新しいロジック アプリを作成するか、前に作成したロジック アプリを起動し、次の手順に従います。
1. [ロジックの開始] カードで、[このロジックを手動で実行] をクリックします。
2. ギャラリーで、以前に作成した BizTalk Flat File Encoder API アプリを選択します (作成した BizTalk Flat File Encoder は画面の右の API Apps リストに表示されます)。
3. 黒の右矢印を選択します。使用可能な 2 つのアクション ([フラット ファイルを XML に] と [XML をフラット ファイルに]) が表示されます。![BizTalk Flat File Encoder のアクション一覧][1] ![BizTalk Flat File Encoder のアクション一覧][4]

選択したアクションに基づいて、次の手順を実行します。

####フラット ファイルを XML に

![BizTalk Flat File Encoder のアクション一覧][5]

パラメーター|型|パラメーターの説明
---|---|---
フラット ファイル|string|入力フラット ファイルの内容
スキーマ名|string|入力フラット ファイルを表すスキーマの名前
ルート名|string|フラット ファイル スキーマのルート ノード名


アクションは文字列として出力を返します (出力 XML)。出力 XML には、入力フラット ファイルの内容の XML 表現が含まれています。

####XML をフラット ファイルに

![BizTalk Flat File Encoder のアクション一覧][6]

パラメーター|型|パラメーターの説明
---|---|---
入力 XML|string|入力 XML の内容

アクションは文字列として出力を返します (フラット ファイル)。出力には、入力 XML の内容のフラット ファイル表現が含まれています。

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG
[7]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.addschema.PNG
[8]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.selectschemauploadoption.PNG
[9]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.shemauploaded.PNG

 

<!---HONumber=AcomDC_0727_2016-->