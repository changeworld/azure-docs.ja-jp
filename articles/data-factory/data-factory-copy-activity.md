<properties 
	pageTitle="Azure Data Factory を使用してデータをコピーする" 
	description="Azure データ ファクトリでコピー操作を使用して、データ ソース間でデータをコピーする方法について説明します。" 
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
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# Azure Data Factory を使用してデータをコピーする (コピー アクティビティ)
## 概要
パイプラインで**コピー アクティビティ**を使用して、ソースからシンク (ターゲット) にデータを一括でコピーできます。コピー アクティビティは、次のシナリオで使用できます。

- **Azure に入力する**。このシナリオでは、次のサブシナリオのために、内部設置型のデータ ソース (例: SQL Server) から Azure データ ストア (例: Azure BLOB、Azure テーブル、Azure SQL データベース) にデータがコピーされます。
	- さらなる処理のために、Azure 上の 1 か所にデータを収集する。
	- 内部設置型または Azure 以外のクラウド プラットフォームから Azure にデータを移行する。
	- コスト効果の高い階層型の保存を実現するために Azure にデータをアーカイブまたはバックアップする。
- **Azure から出力する**。このシナリオでは、次のサブシナリオのために、Azure (例: Azure BLOB、Azure テーブル、Azure SQL データベース) から内部設置型のデータ マートやデータ ウェアハウス (例: SQL Server) にデータがコピーされます。
	- クラウドのデータ ウェアハウスをサポートしていないためにデータを内部設置型に転送する。
	- 既存の内部設置型ソリューションやレポート インフラストラクチャを活用するためにデータを内部設置型に転送する。
	- 階層型の保存を実現するために内部設置型にデータをアーカイブまたはバックアップする。
- **Azure 間でコピーする**。このシナリオでは、Azure の複数のデータ ソースに分散したデータが一元的な Azure データ ストアに集約されます。例: Azure テーブルから Azure BLOB へ、Azure BLOB から Azure テーブルへ、Azure テーブルから Azure SQL へ、Azure BLOB から Azure SQL へ。

詳細については、以下を参照してください。

- ビデオ: [Introducing Azure Data Factory Copy Activity (Azure Data Factory コピーアクティビティの概要)][copy-activity-video]
- コピー アクティビティを使用して Azure BLOB ストレージから Azure SQL データベースにデータをコピーする方法のチュートリアルは、「[Azure Data Factory を使ってみる][adfgetstarted]」を参照してください。 
- コピー アクティビティを使用してオンプレミスの Azure SQL データベースから Azure BLOB ストレージにデータをコピーする方法のチュートリアルは、「[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources]」を参照してください。


## サポートされているソースとシンク
コピー アクティビティでは、次のデータ移行のシナリオがサポートされています。

- Azure BLOB から Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、または IaaS 上の SQL Server へのデータのコピー。
- Azure SQL Database から Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server へのデータのコピー。
- Azure テーブルから Azure BLOB、Azure テーブル、または Azure SQL Database へのデータのコピー。
- オンプレミスの SQL Server または IaaS 上の SQL Server から Azure BLOB または Azure SQL Database へのデータのコピー。
- オンプレミスの Oracle データベースから Azure BLOB へのデータのコピー。
- オンプレミスのファイル システムから Azure BLOB へのデータのコピー。
 

<table border="1">	
	<tr>
		<th><i>ソース/シンク<i></th>
		<th>Azure BLOB</th>
		<th>Azure テーブル</th>
		<th>Azure SQL Database</th>
		<th>内部設置型 SQL Server</th>
		<th>IaaS 上の SQL Server</th>
		<th>Azure DocumentDB</th>
	</tr>	

	<tr>
		<td><b>Azure BLOB</b></td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
	</tr>

	<tr>
		<td><b>Azure テーブル</b></td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
		<td></td>
		<td></td>
		<td>○</td>
	</tr>	

	<tr>
		<td><b>Azure SQL Dtabase</b></td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
	</tr>


	<tr>
		<td><b>オンプレミスの SQL Server</b></td>
		<td>○</td>
		<td></td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>IaaS 上の SQL Server</b></td>
		<td>○</td>
		<td></td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>オンプレミスのファイル システム</b></td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>オンプレミスの Oracle Database</b></td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>オンプレミスのファイル システム</b></td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>オンプレミスの MySQL データベース</b></td>
		<td>○</td>
		<td></td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>オンプレミスの DB2 データベース</b></td>
		<td>○</td>
		<td></td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>オンプレミスの Teradata データベース</b></td>
		<td>○</td>
		<td></td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>オンプレミスの Sybase データベース</b></td>
		<td>○</td>
		<td></td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>オンプレミスの PostgreSQL データベース</b></td>
		<td>○</td>
		<td></td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Azure DocumentDB</b></td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

</table>

詳細については、MSDN ライブラリの「[サポート対象のソースおよびシンク](https://msdn.microsoft.com/library/dn894007.aspx)」のトピックを参照してください。

### サービスとしてのインフラストラクチャ (IaaS) 上の SQL
IaaS 上の SQL Server は、ソースとシンクの両方としてサポートされています。IaaS 上の SQL Server にリンクされたサービスを作成する場合は、Data Management Gateway が必要です。SQL Server とゲートウェイの両方のリソースの競合によるパフォーマンスの低下を回避するため、Data Management Gateway は SQL Server をホストしていない仮想マシンにインストールすることをお勧めします。Data Management Gateway の詳細については、「[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources]」を参照してください。

1.	パブリック DNS 名と静的パブリック ポートを持つ VM: プライベート ポート マッピング
2.	SQL エンドポイントが公開されていないパブリック DNS 名を持つ VM
3.	仮想ネットワーク
	<ol type='a'>
<li>一覧の最後に次のトポロジが含まれている Azure クラウド VPN</li>	
<li>Azure Virtual Network を使用した内部設置型からクラウドへのサイト間 VPN を持つ VM</li>	
</ol>![Data Factory (コピー アクティビティ)][image-data-factory-copy-actvity]

## コピー アクティビティ - コンポーネント
コピー アクティビティは、次のコンポーネントで構成されます。

- **入力テーブル**。テーブルは、スキーマを持つ四角形のデータ セットです。入力テーブルのコンポーネントでは、テーブルの名前、テーブルの種類、入力データが含まれたデータ ソースを参照するリンクされたサービスなどのアクティビティの入力データについて説明します。
- **出力テーブル**。出力テーブルは、テーブルの名前、テーブルの種類、出力データが含まれたデータ ソースを参照するリンクされたサービスなどのアクティビティの出力データについて説明します。
- **変換ルール**。変換ルールは、ソースから入力データを抽出する方法や、出力データをシンクに読み込む方法などを指定します。
 
コピー アクティビティには、1 つの**入力テーブル**と 1 つの**出力テーブル**があります。

## <a name="CopyActivityJSONSchema"></a>コピー アクティビティの JSON
パイプラインは、1 つ以上のアクティビティで構成されます。パイプライン内のアクティビティは、**activities ** セクションで定義されます。パイプラインの JSON は次のとおりです。
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

**activities** セクション内のアクティビティごとに、次の最上位構造があります。**type** プロパティは、**CopyActivity** に設定されます。コピー アクティビティは、1 つの入力テーブルと 1 つの出力テーブルを使用できます。
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

次の表では、アクティビティのセクションで使用されるタグについて説明します。

<table border="1">	
	<tr>
		<th align="left">タグ</th>
		<th align="left">説明</th>
		<th align="left">必須</th>
	</tr>	

	<tr>
		<td>name</td>
		<td>アクティビティの名前です。</td>
		<td>Y</td>
	</tr>	

	<tr>
		<td>description</td>
		<td>アクティビティの用途を説明するテキストです。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>type</td>
		<td>アクティビティの種類を指定します。<br/><br/><b>type</b> は、<b>CopyActivity</b> に設定します。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>アクティビティで使用される入力テーブルです。コピー アクティビティに 1 つの入力テーブルのみを指定します。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>アクティビティで使用される出力テーブルです。コピー アクティビティに 1 つの出力テーブルのみを指定します。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>transformation のプロパティは type に依存します。<b>コピー アクティビティ</b>では、<b>transformation</b> セクション内の<b>source</b> セクションと <b>sink</b> セクションを指定する必要があります。詳細については、この記事の後半で説明します。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>アクティビティの実行時の動作に影響するポリシーです。指定されていない場合は既定値が使用されます。</td>
		<td>N</td>
	</tr>


</table>

JSON プロパティとスクリプトの詳細については、「[JSON Scripting Reference (JSON スクリプト リファレンス)][json-script-reference]」を参照してください。

### ソースおよびシンクの種類
ソースおよびシンクの種類とそれらの種類でサポートされているプロパティの一覧については、MSDN ライブラリの「[サポートされているソースとシンク][msdn-supported-sources-sinks]」を参照してください。

## コピー アクティビティ - 例
この例では、入力テーブルと出力テーブルが定義されます。また、これらのテーブルが、内部設置型の SQL Server データベースから Azure BLOB にデータをコピーするパイプライン内のコピー アクティビティで使用されます。

**前提条件** サンプル JSON スクリプトでは、次の Azure Data Factory アーティファクトが参照されます。

* **ADF** という名前のリソース グループ
* **CopyFactory** という名前の Azure Data Factory
* オンプレミスの SQL Server データベースをポイントする **MyOnPremisesSQLDB** という名前のリンクされたサービス
* Azure BLOB ストレージをポイントする **MyAzureStorage** という名前のリンクされたサービス

### 入力テーブルの JSON
次の JSON スクリプトは、入力テーブルを定義します。この入力テーブルは、リンクされたサービス **MyOnPremisesSQLDB** で定義されたオンプレミスの SQL Server データベースにある SQL テーブル **MyTable** を参照します。**name** は Azure Data Factory テーブルの名前で、**tableName** は SQL Server データベース内の SQL テーブルの名前です。

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

次の Azure PowerShell コマンド サンプルでは、**New-AzureDataFactoryTable** を使用します。このコマンドは、上記のスクリプトが含まれた JSON ファイルを使用して、**CopyFactory** という Azure Data Factory 内にテーブル (**MyOnPremTable**) を作成します。
         
	New-AzureDataFactoryTable -ResourceGroupName ADF –Name MyOnPremTable –DataFactoryName CopyFactory –File <Filepath>\MyOnPremTable.json.

Data Factory コマンドレットの詳細については、[コマンドレット リファレンス][cmdlet-reference]を参照してください。

### 出力テーブルの JSON
次の JSON スクリプトは、**MyDemoBlob** という出力テーブルを定義します。この出力テーブルは、**MyContainer** という BLOB コンテナー内の **MySubFolder** という BLOB フォルダーに含まれている **MyBlob** という Azure BLOB を参照します。
         
	{
   		"name": "MyDemoBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

次の Azure PowerShell コマンド サンプルでは、**New-AzureDataFactoryTable** を使用します。このコマンドは、上記のスクリプトが含まれた JSON ファイルを使用して、**CopyFactory** という Azure Data Factory 内にテーブル (**MyDemoBlob**) を作成します。
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory –File <Filepath>


### パイプライン (コピー アクティビティ) の JSON
この例では、次のプロパティを使用して **CopyActivityPipeline** というパイプラインが定義されます。

- **type** プロパティは、**CopyActivity** に設定されます。
- **MyOnPremTable** が入力として指定されます (**inputs** タグ)。
- **MyAzureBlob** が出力として指定されます (**outputs** タグ)。
- **Transformation** セクションには、**source** と **sink** という 2 つのサブ セクションがあります。ソースの種類は **SqlSource** に設定され、シンクの種類は **BlobSink** に設定されます。**sqlReaderQuery** は、変換 (射影) がソース上で実行されるように定義します。すべてのプロパティの詳細については、「[JSON Scripting Reference (JSON スクリプト リファレンス)][json-script-reference]」を参照してください。

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}


 次の Azure PowerShell コマンド サンプルでは、**New-AzureDataFactoryPipeline** を使用します。このコマンドは、上記のスクリプトが含まれた JSON ファイルを使用して、**CopyFactory** という Azure Data Factory 内にパイプライン (**CopyActivityPipeline**) を作成します。
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF –DataFactoryName CopyFactory –File <Filepath>

> [AZURE.NOTE]コピー アクティビティの使用のその他のサンプルについては、「[Examples for using Copy Activity in Azure Data Factory (Azure Data Factory でのコピー アクティビティの使用のその他のサンプル)][copy-activity-examples]」を参照してください。

## セキュリティ
このセクションでは、コピー アクティビティでデータ ストアへの安全なアクセスを確立するのに役立つ全般的なセキュリティ ガイドラインとベスト プラクティスを紹介します。

HTTPS 接続を提供するデータ ストアの場合、ネットワークでの安全な通信を確保するためにコピー アクティビティで HTTPS 接続を選択します。たとえば、**Azure Storage** の場合、接続文字列で **DefaultEndpointsProtocol=https** を使用します。

**Azure SQL Database** の場合は、暗号化された接続を明示的に要求し、サーバー証明書を信頼しないようにして "man in the middle" 攻撃を回避します。これを行うには、接続文字列で **Encrypt=True** と **TrustServerCertificate=False** を使用します。詳細については、「[SQL Database のセキュリティのガイドラインと制限事項](https://msdn.microsoft.com/library/azure/ff394108.aspx)」を参照してください。

**SQL Server** などの従来のデータベースの場合 (特に、インスタンスが Azure Virtual Machine 内にある場合)、暗号化された接続オプションを有効にします。このためには、接続文字列で **Encrypt=True** および **TrustServerCertificate=False** を指定して、署名された証明書を構成します。詳細については、[データベース エンジンへの暗号化接続の有効化]https://msdn.microsoft.com/library/ms191192(v=sql.110).aspx および「[接続文字列の構文](https://msdn.microsoft.com/library/ms254500.aspx)」を参照してください。

## 高度なシナリオ
- **構造体定義を使用した列のフィルター処理**。テーブルの種類によっては、ソースの列のサブセットを指定できます。指定するには、テーブル定義の **Structure** 定義の列を、基になるデータ ソースの列よりも少なく設定します。
- **変換ルール - 列マッピング**。列マッピングを使用して、ソース テーブルの列をシンク テーブルの列にマップする方法を指定できます。
- **コピー アクティビティによるデータ型の処理**。どのような場合にテーブル定義の Structure セクションで指定するデータ型が受け入れられる、または無視されるかを説明します。
- **SQL シンクのストアド プロシージャの呼び出し**。データの SQL Server または Azure SQL Database へのコピー時に、ユーザーが指定したストアド プロシージャが構成され、呼び出されることがあります。

これらのシナリオの詳細については、記事「[Advanced Scenarios for using the Copy Activity with Azure Data Factory (Azure Data Factory でコピー アクティビティを使用する高度なシナリオ)][copy-activity-advanced]」を参照してください。

## チュートリアル
コピー アクティビティを使用して Azure BLOB ストレージから Azure SQL データベースにデータをコピーする方法のチュートリアルは、「[Azure Data Factory を使ってみる][adfgetstarted]」を参照してください。
 
コピー アクティビティを使用してオンプレミスの Azure SQL データベースから Azure BLOB ストレージにデータをコピーする方法のチュートリアルは、「[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources]」を参照してください。

## 関連項目
- [コピー アクティビティ - 例][copy-activity-examples]
- [ビデオ: Introducing Azure Data Factory Copy Activity (Azure Data Factory コピーアクティビティの概要)][copy-activity-video]
- [MSDN ライブラリのコピー アクティビティに関するトピック][msdn-copy-activity]
- [Advanced Scenarios for using the Copy Activity with Azure Data Factory (Azure Data Factory でコピー アクティビティを使用する高度なシナリオ)][copy-activity-advanced]

[msdn-copy-activity]: https://msdn.microsoft.com/library/dn835035.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-tables-topic]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-supported-sources-sinks]: https://msdn.microsoft.com/library/dn894007.aspx
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx

[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[copy-activity-advanced]: data-factory-copy-activity-advanced.md
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png
 

<!---HONumber=62-->