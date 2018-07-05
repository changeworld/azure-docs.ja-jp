---
title: Azure Data Lake Analytics を使用して Avro データのクエリを実行する | Microsoft Docs
description: メッセージ本文のプロパティを使用してデバイス テレメトリを BLOB ストレージにルーティングし、BLOB ストレージに書き込まれた Avro 形式のデータに対してクエリを実行します。
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: c56b567498047ee996018675134c252ec1de7e0c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081370"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Azure Data Lake Analytics を使用して Avro データのクエリを実行する

この記事では、Azure IoT Hub から Azure サービスにメッセージを効率的にルーティングするために Avro データに対してクエリを実行する方法について説明します。 ブログ記事「[Azure IoT Hub メッセージのルーティング: メッセージ本文でルーティングできるようになりました]」(Azure IoT Hub メッセージのルーティング: メッセージ本文でルーティングできるようになりました) でお知らせしたように、IoT Hub ではプロパティまたはメッセージ本文のいずれでもルーティングがサポートされます。 詳細については、「[メッセージ本文でのルーティング][Routing on message bodies]」を参照してください。 

Azure IoT Hub が Azure Blob Storage にメッセージをルーティングするときに、IoT Hub ではメッセージ本文のプロパティとメッセージのプロパティの両方を持つコンテンツが Avro 形式で書き込まれるという問題がありました。 IoT Hub では Avro データ形式の BLOB ストレージへのデータの書き込みのみがサポートされますが、この形式は他のエンドポイントでは使用されません。 詳細については、「[Azure Storage コンテナーを使うとき][When using Azure storage containers]」を参照してください。 Avro はデータとメッセージの保存には最適な形式ですが、データのクエリ用途には使うのは容易ではありません。 比較すると、JSON または CSV 形式はデータのクエリがはるかに簡単です。

非リレーショナル ビッグデータのニーズと形式に対応し、この課題を克服するために、データの変換とスケーリングのいずれにおいても、ビッグデータ パターンの多くを使用することができます。 そのようなパターンの 1 つとして、"クエリごとの支払い" である Azure Data Lake Analytics があり、この記事では、この点について重点的に取り上げています。 クエリは Hadoop やその他のソリューションで簡単に実行できますが、多くの場合、Data Lake Analytics の方がこの "クエリごとの支払い" アプローチに適しています。 

U-SQL には Avro 用の "エクストラクター" があります。 詳細については、[U-SQL Avro の例]を参照してください。

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Avro データのクエリと CSV ファイルへのエクスポート
このセクションでは、Avro データを照会して Azure Blob Storage 内の CSV ファイルにエクスポートしますが、他のリポジトリやデータ ストアに対してもデータを簡単に配置することができます。

1. メッセージ本文のプロパティを使用して Azure Blob Storage エンドポイントにデータをルーティングし、メッセージを選択するように Azure IoT Hub を設定します。

    ![[カスタム エンドポイント] セクション][img-query-avro-data-1a]

    ![[ルート] コマンド][img-query-avro-data-1b]

2. 製品ドキュメントで参照されているように、プロパティまたはメッセージ本文にデバイスのエンコード、コンテンツ タイプ、および必要なデータが含まれていることを確認します。 これらの属性を Device Explorer で見ると (下図参照)、それらが正しく設定されていることを確認できます。

    ![イベント ハブのデータ ペイン][img-query-avro-data-2]

3. Azure Data Lake Store インスタンスと Data Lake Analytics インスタンスを設定します。 Data Lake Analytics インスタンスは、Data Lake Store インスタンスへのルーティングを必要としますが、Azure IoT Hub では、それが行われません。

    ![Data Lake Store インスタンスと Data Lake Analytics インスタンス][img-query-avro-data-3]

4. Data Lake Analytics で、Azure Blob Storage を追加のストア (Azure IoT Hub がデータをルーティングするのと同じ Blob Storage) として構成します。

    ![[データ ソース] ウィンドウ][img-query-avro-data-4]
 
5. [U-SQL Avro の例]にもあるように、4 つの DLL ファイルが必要となります。 これらのファイルを Data Lake Store インスタンス内の場所にアップロードします。

    ![アップロードされた 4 つの DLL ファイル][img-query-avro-data-5] 

6. Visual Studio で、U-SQL プロジェクトを作成します。
 
    ![U-SQL プロジェクトの作成][img-query-avro-data-6]

7. 次のスクリプトの内容を、新しく作成されたファイルに貼り付けます。 3 つの強調表示されたセクション (Data Lake Analytics アカウント、関連する DLL ファイルのパス、ストレージ アカウントの正しいパス) を変更します。
    
    ![修正する 3 つのセクション][img-query-avro-data-7a]

    CSV ファイルに単純な出力を行う実際の U-SQL スクリプト:
    
    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
        ""type"":""record"",
        ""name"":""Message"",
        ""namespace"":""Microsoft.Azure.Devices"",
        ""fields"":[{
        ""name"":""EnqueuedTimeUtc"",
        ""type"":""string""
        },
        {
        ""name"":""Properties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""SystemProperties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""Body"",
        ""type"":[""null"",""bytes""]
        }
        ]
        }");

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    Data Lake Analytics が次のスクリプトを実行するのにかかった時間は 5分です。その際、分析ユニットは 10 個に制限され、177 個のファイルが処理されました。 その結果は、次の画像に表示されている CSV ファイル出力に示されます。
    
    ![CSV ファイルへの出力の結果][img-query-avro-data-7b]

    ![CSV ファイルに変換された出力][img-query-avro-data-7c]

    JSON を解析するには、手順 8. に進んでください。
    
8. ほとんどの IoT メッセージは JSON ファイル形式です。 次の行を追加すると、メッセージを解析して JSON ファイルに出力できます。WHERE 句を追加して必要なデータのみを出力することが可能です。

    ```sql
       @jsonify = SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    出力結果には、`SELECT` コマンドの項目ごとに 1 つの列が表示されます。 
    
    ![各項目に対応した列を示す出力][img-query-avro-data-8]

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure IoT Hub から Azure サービスにメッセージを効率的にルーティングするために Avro データに対してクエリを実行する方法について説明しました。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT リモート監視ソリューション アクセラレータ][lnk-iot-sa-land]に関するページをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド]をご覧ください。

IoT Hub でのメッセージのルーティングの詳細については、[IoT Hub でのメッセージの送受信][lnk-devguide-messaging]に関するページを参照してください。

<!-- Images -->
[img-query-avro-data-1a]: ./media/iot-hub-query-avro-data/query-avro-data-1a.png
[img-query-avro-data-1b]: ./media/iot-hub-query-avro-data/query-avro-data-1b.png
[img-query-avro-data-2]: ./media/iot-hub-query-avro-data/query-avro-data-2.png
[img-query-avro-data-3]: ./media/iot-hub-query-avro-data/query-avro-data-3.png
[img-query-avro-data-4]: ./media/iot-hub-query-avro-data/query-avro-data-4.png
[img-query-avro-data-5]: ./media/iot-hub-query-avro-data/query-avro-data-5.png
[img-query-avro-data-6]: ./media/iot-hub-query-avro-data/query-avro-data-6.png
[img-query-avro-data-7a]: ./media/iot-hub-query-avro-data/query-avro-data-7a.png
[img-query-avro-data-7b]: ./media/iot-hub-query-avro-data/query-avro-data-7b.png
[img-query-avro-data-7c]: ./media/iot-hub-query-avro-data/query-avro-data-7c.png
[img-query-avro-data-8]: ./media/iot-hub-query-avro-data/query-avro-data-8.png

<!-- Links -->
[Azure IoT Hub メッセージのルーティング: メッセージ本文でルーティングできるようになりました]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[U-SQL Avro の例]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
