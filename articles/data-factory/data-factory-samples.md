<properties 	
	pageTitle="Azure のデータのファクトリのサンプル" 
	description="Azure のデータのファクトリのサービスに付属するサンプルについての詳細を説明します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="spelluru"/>

# Azure のデータのファクトリのサンプル

## Azure ポータルでのサンプル
迅速に展開、レビュー、およびテストできますを使用して、Azure のデータのファクトリ サンプル、 **パイプライン サンプル** ブレード、Azure ポータルにします。

1. データの新しいファクトリを作成するか、既存のデータのファクトリを開きます。参照してください [Azure のデータのファクトリの概要][data-factory-get-started] 手順については、データのファクトリを作成します。
2.  **データ ファクトリ** データのファクトリに対するブレードをクリックして、 **パイプライン サンプル** 並べて表示します。

	![サンプル パイプラインのタイル](./media/data-factory-samples/SamplePipelinesTile.png)

2.  **パイプライン サンプル** ブレードで] をクリックして、 **サンプル** に展開します。
	
	![サンプル パイプライン ブレード](./media/data-factory-samples/SampleTile.png)

3. このサンプルの構成設定を指定します。たとえば、Azure ストレージ アカウント名とアカウント キー、Azure の SQL server 名、データベース、ユーザーの ID とパスワードなどしています.

	![サンプルのブレード](./media/data-factory-samples/SampleBlade.png)

4. 構成設定を指定したが完了したら、クリックして **作成** 作成/展開する、サンプルのパイプラインおよびパイプラインによって使用されるリンクのサービスとテーブルです。
5. 事前にクリックすると、サンプル タイルのデプロイの状態が表示されます、 **パイプライン サンプル** ブレードします。

	![展開の状態](./media/data-factory-samples/DeploymentStatus.png)

6. 表示されている場合、 **展開が成功した** メッセージ サンプルでは、閉じる、タイル、 **パイプラインのサンプル** ブレードします。
5.  **データ ファクトリ** ブレードが表示されます、関連付けられているサービス、データのセット、およびパイプラインが、データのファクトリを追加します。  

	![データの工場出荷時のブレード](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

次の表で使用できる、サンプルの簡単な説明を提供する、 **パイプラインのサンプル** 並べて表示します。

サンプルの名前 | description
----------- | -----------
ゲームの顧客プロファイル | Contoso は、ゲーム機、携帯機器、パーソナル コンピューター (PC) など、複数のプラットフォーム向けにゲームを製作するゲーム会社です。これらのゲームはそれぞれが大量のログを産み出します。Contoso の目標は、収集および使用状況の情報を取得、商品とクロスセルの機会を識別する、新しい魅力的な機能などの開発にこれらのゲームで生成されるログの分析には.ビジネスの向上より優れたエクスペリエンスを顧客に提供します。このサンプルでは、それらに参照データを拡充しサンプルのログ、プロセスを収集し、Contoso は最近を起動したマーケティング キャンペーンの効果を評価するデータを変換します。
 
## GitHub 上のサンプル
 [GitHub の Azure-DataFactory リポジトリ](https://github.com/azure/azure-datafactory) 迅速に Azure のデータのファクトリのサービスとをごとの傾斜増加するのに役立ついくつかのサンプルを格納 (または)、スクリプトを変更して、独自のアプリケーションで使用します。Samples\JSON フォルダーには、一般的なシナリオ用の JSON のスニペットが含まれています。

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=GIT-SubDir--> 