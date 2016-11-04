### <a name="compression-support"></a>圧縮のサポート
大量のデータセットを処理すると、I/O およびネットワークにボトルネックが生じる可能性があります。 そのため、データを圧縮して保存すると、ネットワークでのデータ転送速度が上昇してディスク領域を節約できるだけでなく、ビッグ データの処理性能を大幅に高めることができます。 現時点では、圧縮は Azure BLOB やオンプレミスのファイル システムなど、ファイルベースのデータ ストアでサポートされています。  

> [!NOTE]
> **AvroFormat**、**OrcFormat**、および **ParquetFormat** のデータの圧縮設定はサポートされていません。 
> 
> 

データセットの圧縮を指定するには、次の例のように、データセットの JSON で **compression** プロパティを使用します。   

    {  
        "name": "AzureBlobDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureBlob",  
            "linkedServiceName": "StorageLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  

**compression** セクションには次の 2 つのプロパティがあります。  

* **type:** 圧縮コーデックです。**GZIP**、**Deflate**、または **BZIP2** を指定できます。  
* **level:** 圧縮率です。**Optimal** または **Fastest** を指定できます。 
  
  * **Fastest:** 圧縮操作は可能な限り短時間で完了しますが、生成ファイルが最適に圧縮されない場合があります。 
  * **Optimal**: 圧縮操作では最適に圧縮されますが、操作完了までの時間が増加する場合があります。 
    
    詳細については、 [圧縮レベル](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) に関するトピックをご覧ください。 

前述のサンプル データセットをコピー アクティビティの出力として使用し、コピー アクティビティで出力データを GZIP コーデックによって最適な圧縮率で圧縮してから、Azure BLOB Storage の pagecounts.csv.gz という名前のファイルに圧縮データを書き込む場合を考えます。   

入力データセットの JSON で compression プロパティを指定すると、パイプラインでソースから圧縮データを読み取ることができ、出力データセットの JSON で compression プロパティを指定すると、コピー アクティビティにより出力先に圧縮データを書き込むことができます。 いくつかのサンプル シナリオを次に示します。 

* Azure BLOB から GZIP 圧縮データを読み取り、展開して、生成されたデータを Azure SQL Database に書き込みます。 この場合、Azure BLOB 入力データセットを、compression JSON プロパティを使用して定義します。 
* オンプレミスのファイル システムのプレーンテキスト ファイルからデータを読み取り、GZip 形式で圧縮して、圧縮データを Azure BLOB に書き込みます。 この場合、Azure BLOB 出力データセットを、compression JSON プロパティを使用して定義します。  
* Azure BLOB から GZIP 圧縮データを読み取って展開し、BZIP2 で圧縮して、生成されたデータを Azure BLOB に書き込みます。 この場合、Azure BLOB 入力データセットは圧縮タイプを GZIP に設定して定義し、Azure BLOB 出力データセットは圧縮タイプを BZIP2 に設定して定義します。   

<!--HONumber=Oct16_HO2-->


