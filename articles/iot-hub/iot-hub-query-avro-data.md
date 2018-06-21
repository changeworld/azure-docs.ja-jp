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
ms.openlocfilehash: 98a30155c73a937042b4bea6568543fb5152d748
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726680"
---
# <a name="query-avro-data-using-azure-data-lake-analytics"></a>Azure Data Lake Analytics を使用して Avro データのクエリを実行する

この記事では、Azure IoT Hub から Azure サービスにメッセージを効率的にルーティングするために Avro データに対してクエリを実行する方法について説明します。 ブログ投稿のお知らせ「[Azure IoT Hub メッセージのルーティング: メッセージ本文でルーティングできるようになりました]」(Azure IoT Hub メッセージのルーティング: メッセージ本文でルーティングできるようになりました) に従い、IoT Hub ではプロパティまたはメッセージ本文のいずれでもルーティングがサポートされます。 「[メッセージ本文でのルーティング][Routing on message bodies]」もご覧ください。 

Azure IoT Hub が BLOB ストレージにメッセージをルーティングするときに、IoT Hub ではメッセージ本文とメッセージ プロパティの両方を持つコンテンツが Avro 形式で書き込まれるという問題がありました。 IoT Hub では Avro データ形式の BLOB ストレージへのデータの書き込みのみがサポートされますが、この形式は他のエンドポイントでは使用されません。 「[Azure Storage コンテナーを使うとき][When using Azure storage containers]」をご覧ください。 Avro 形式は、データ/メッセージの保存に適していますが、データのクエリが困難です。 比較すると、JSON または CSV 形式はデータのクエリがはるかに簡単です。

これを解決するために、データの転送とスケーリングの両方にビッグ データ パターンの多くを使用して、非リレーショナル ビッグ データのニーズと形式に対応できます。 パターンの 1 つとして、"クエリごとの支払い" パターンである Azure Data Lake Analytics (ADLA) があります。 この記事ではこれに注目します。 Hadoop やその他のソリューションでクエリを簡単に実行できますが、多くの場合、ADLA の方がこの "クエリごとの支払い" アプローチに適しています。 U-SQL には Avro 用の "エクストラクター" があります。 「[U-SQL Avro の例]」(U-SQL Avro の例) をご覧ください。

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Avro データのクエリと CSV ファイルへのエクスポート
このセクションでは、Azure Blob Storage での Avro データのクエリの実行と CSV ファイルへのエクスポートの手順について説明しますが、他のリポジトリまたはデータ ストアにデータを簡単に配置できます。

1. メッセージ本文のプロパティを使用して Azure Blob Storage エンドポイントにデータをルーティングし、メッセージを選択するように Azure IoT Hub を設定します。

    ![手順 1a. の画面キャプチャ][img-query-avro-data-1a]

    ![手順 1b. の画面キャプチャ][img-query-avro-data-1b]

2. 製品ドキュメントで参照されているように、プロパティまたはメッセージ本文にデバイスのエンコード、コンテンツ タイプ、および必要なデータが含まれていることを確認します。 Device Explorer で表示すると (下記参照)、これらの属性が正しく設定されていることを確認できます。

    ![手順 2. の画面キャプチャ][img-query-avro-data-2]

3. Azure Data Lake Store (ADLS) と Azure Data Lake Analytics インスタンスを設定します。 Azure IoT Hub は、Azure Data Lake Store にルーティングしませんが、ADLA はこれを必要とします。

    ![手順 3. の画面キャプチャ][img-query-avro-data-3]

4. ADLA で、Azure Blob Storage を追加のストア (Azure IoT Hub がデータをルーティングするのと同じ Blob Storage) として構成します。

    ![手順 4. の画面キャプチャ][img-query-avro-data-4]
 
5. 「[U-SQL Avro の例]」(U-SQL Avro の例) で説明されているように、必要な DLL が 4 つあります。  ADLS 内の場所にこれらのファイルをアップロードします。

    ![手順 5. の画面キャプチャ][img-query-avro-data-5] 

6. Visual Studio で、U-SQL プロジェクトを作成します
 
    ![手順 6. の画面キャプチャ][img-query-avro-data-6]

7. 次のスクリプトの内容をコピーし、新しく作成されたファイルに貼り付けます。 3 つの強調表示されたセクション (ADLA アカウント、関連する DLL のパス、ストレージ アカウントの正しいパス) を変更します。
    
    ![手順 7a. の画面キャプチャ][img-query-avro-data-7a]

    CSV に単純な出力を行う実際の U-SQL スクリプト:
    
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

    次に示すスクリプトを実行すると、限定された 10 個の分析単位で 177 ファイルを処理し、CSV ファイルに出力を要約するのに ADLA は 5 分を要しました。
    
    ![手順 7b. の画面キャプチャ][img-query-avro-data-7b]

    出力を表示すると、Avro コンテンツが CSV ファイルに変換されていることを確認できます。 JSON を解析する場合は、手順 8 に進みます。
    
    ![手順 7c. の画面キャプチャ][img-query-avro-data-7c]

    
8. ほとんどの IoT メッセージは JSON 形式です。  次の行を追加すると、メッセージを JSON に解析できるので、WHERE 句を追加して必要なデータのみを出力することができます。

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

9. 出力を表示すると、select コマンド内の各項目の列が表示されています。 
    
    ![手順 8. の画面キャプチャ][img-query-avro-data-8]

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

[lnk-iot-sa-land]: ../iot-accelerators/index.md
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
