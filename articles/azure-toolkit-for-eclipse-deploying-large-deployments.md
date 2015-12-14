<properties
    pageTitle="大規模なデプロイ"
    description="Azure Toolkit for Eclipse を使用して大規模なデプロイを行う方法について説明します。"
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
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/ja-JP/library/azure/dn268601.aspx -->

# 大規模なデプロイ #

デプロイの規模が大きすぎて既定の approot フォルダーに格納できない場合は、JDK とアプリケーション サーバーのデプロイ ルート フォルダーとしてローカル ストレージ リソースを使用できます。

## 大規模なデプロイのデプロイ ルート フォルダーとしてローカル ストレージ リソースを使用するには ##

1. 新しいローカル ストレージ リソースを作成します。リソースの名前は問いません。ストレージ リソースはロール レベルで定義されます。新しいローカル ストレージ リソースを作成するためのローカル ストレージ構成ダイアログにアクセスする最も簡単な方法は、次の手順に従うことです。**プロジェクト エクスプ ローラー** ビューでロールを右クリックします (ロールが表示されていない場合は、Azure プロジェクト ノードを展開します)。**[Azure]** をクリックし、**[Local Storage]** をクリックします。**[Local Storage]** ダイアログで、**[Add]** をクリックして新しいローカル ストレージ リソースを作成します。
1. 2048 MB 以上の目的のサイズを設定します (このサイズより小さくすると、approot のように同一ファイル サイズ問題が発生する可能性があります)。
1. **[Clean the contents when the role instance is recycled]** がオンになっていることを確認します。これで、ロール インスタンスがリサイクルされるときに、デプロイのスタートアップ ロジックがリソース内の既存のファイルと競合することを防止できます。
1. **[Environment variable storing the resource's directory path after deployment]** の値が文字列 **DEPLOYROOT** に設定されていることを確認します。ローカル ストレージ リソース ダイアログは次のようになります。![][ic667943]

ローカル リソースの*名前*として **DEPLOYROOT** を使用し、自動的に生成される環境変数 (この場合は **DEPLOYROOT\_PATH** に設定されます) を変更しないという操作でも、アプリケーションは問題なく動作します。

ローカル ストレージ リソースの作成に関する追加情報については、「[ローカル ストレージのプロパティ][]」を参照してください。

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
[ローカル ストレージのプロパティ]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png

<!---HONumber=AcomDC_1203_2015-->