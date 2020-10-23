---
title: 業界標準モデルの変換
titleSuffix: Azure Digital Twins
description: 業界標準 (RDF/OWL) モデルを DTDL に変換するためのパターンについて理解する
author: baanders
ms.author: baanders
ms.date: 9/28/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: bf39bd0c787ed64d573cc2a257442b4d3bb8ab7d
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015116"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>業界標準モデルを Azure Digital Twins 用の DTDL に変換する

Azure Digital Twins のモデルは、JSON-LD ベースの [**Digital Twins Definition language (DTDL)** ](concepts-models.md) で表現されます。 Azure Digital Twins の外部に RDF や OWL などの業界標準に基づく既存のモデルがある場合、Azure Digital Twins でそれらを使用するには、**それらを DTDL に変換**する必要があります。 DTDL のバージョンは、その後、Azure Digital Twins 内で、モデルの信頼できるソースとなります。

この記事では、RDF ベースの業界モデルやカスタム モデルを DTDL に変換するためのパターンについて説明します。 次の情報が含まれます。
* さまざまな標準やモデルの種類に適用できる、**戦略レベルのガイダンス**
* RDF 固有のコンバーター用の[**サンプル コード**](#sample-converter-application)

## <a name="industry-models"></a>業界モデル  

業界モデルを使用すると、Azure Digital Twins モデルを設計するときに、豊富な機能を持つ開始点になります。 業界モデルの利用は、標準化と情報共有にも役立ちます。 

一般的な業界モデルの一部として、以下のものがあります。  

| 業界 | モデル |
| --- | --- | 
| ビルや施設の管理 | [RealEstateCore](https://www.realestatecore.io/)<br>[BRICK スキーマ](https://brickschema.org/ontology/1.1/)<br>[Building Topology Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/)<br>[セマンティック センサー ネットワーク](https://www.w3.org/TR/vocab-ssn/)<br>[buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| スマート シティ | [ETSI NGSI-LD](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[Smart Applications REFerence (SAREF)](https://saref.etsi.org/)<br>[Fiware](https://www.fiware.org/)<br>[Open & Agile Smart Cities (OASC)](https://oascities.org/) |
| エネルギー グリッド | [CIM](https://cimug.ucaiug.org/)/[IEC 61968](https://en.wikipedia.org/wiki/IEC_61968)<br>エネルギーと商品取引用の [ADRM モデル](http://www.adrm.com/) | 

必要に応じて、DTDL を使用して、業界モデルのカスタマイズや拡張を行ったり、ゼロから独自のカスタム モデルを開発したりすることもできます。 

## <a name="create-and-edit-models"></a>モデルを作成して編集する

モデリングにおける最初の手順は、モデルを作成することです。 自社用の業界標準モデルを作成し、編集を完了してからそれを DTDL に変換することも、モデルを早期に DTDL に変換し、DTDL ドキュメントとしてモデルの編集を続けることもできます。

### <a name="with-industry-standards"></a>業界標準の利用

ほとんどの業界モデル (**オントロジー**とも呼ばれます) は、[OWL](https://www.w3.org/OWL/[)、[RDF](https://www.w3.org/2001/sw/wiki/RDF)、[RDFS](https://www.w3.org/2001/sw/wiki/RDFS) などのセマンティック Web 標準に基づいています。 

場合によっては、OWL ベースのモデル ツールを使用してモデルを作成または編集することが推奨されることがあります。 以下を含めて、任意の数のモデル作成ツールを使用して、OWL ベースのモデルを設計できます。
* [WebProtégé](https://protege.stanford.edu/products.php#web-protege) と [Protégé Desktop](https://protege.stanford.edu/products.php#desktop-protege) は、一般的な例です。 複数の形式の業界モデルをインポートし、モデルの編集や拡張を行い、モデルをエクスポートすることができます。 
* [WebVOWL](http://www.visualdataweb.de/webvowl/) は、モデルを視覚化するための、別の一般的なツールです。 

DTDL ではない何らかの業界標準を使用してモデルを作成した場合は、後でそれを DTDL に変換し、それを Azure Digital Twins にアップロードする必要があります。 

#### <a name="common-model-file-formats"></a>共通モデル ファイルの形式 

RDF、OWL、および RDFS は、セマンティック Web の基本的構成要素です。 

**RDF** には、**トリプル (3 要素)** の形式で物事を記述する概念構造が用意されています。 トリプルは、**主語**、**述語**、**目的語**という 3 つの部分で構成されます。 目的語は URI で作成できます。 

以下に、RDF トリプルの例をいくつか示します。

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

これらの例はすべて有効な RDF ですが、最後のステートメントはセマンティックの点で無効です。 この状態において、OWL の仕様が役割を果たします。 **OWL** では、有効なオントロジーを用意するために、RDF で何を記述できるかが定義されます。

次に、OWL を利用する例を示します。 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

**RDFS** には、クラスの定義と記述に役立つ追加のボキャブラリ セマンティクスが用意されています。 たとえば、そのようなクラスの 1 つは `rdfs:subClassOf` です。

```
<Equipment> <subClassOf> <Asset>
```

モデルは、以下を含む多くのファイル形式で、保存、インポート、およびエクスポートできます。  
* RDF/XML 
* Turtle (TTL) 
* OWL/XML 

### <a name="with-dtdl"></a>DTDL の利用

Azure Digital Twins では、モデリングのために JSON-LD ベースの  **Digital Twin Definition Language (DTDL)** を使用します。 Azure Digital Twins で使用されるモデルはすべて、当初から DTDL で記述されているか、DTDL に変換される必要があります。

DTDL のモデルを操作するときには、 [Visual Studio Code](https://code.visualstudio.com/) 用に提供されている [**DTDL 拡張機能**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) を使用できます。これにより、DTDL モデルの記述を容易にするための、構文の検証や、その他の機能が提供されます。

Azure Digital Twins モデルとそのコンポーネントの詳細については、[*カスタム モデルの概念*](concepts-models.md)に関するページと、[*カスタム モデルを管理する方法*](how-to-manage-model.md)に関するページを参照してください。

## <a name="convert-models-to-dtdl"></a>モデルを DTDL に変換する

Azure Digital Twins でモデルを使用するには、それが DTDL 形式である必要があります。 このセクションでは、Azure Digital Twins で使用できるように、RDF ベースのモデルを DTDL に変換する方法について説明します。

RDF ベースのモデルを DTDL に変換するときに使用できるサードパーティ製のライブラリがいくつかあります。 これらのライブラリの一部では、モデル ファイルをグラフに読み込むことができます。 グラフ内をループ処理して特定の RDFS および OWL コンストラクトを探し、これらを DTDL に変換することができます。   

次の表は、RDFS および OWL コンストラクトをどのように DTDL にマップできるかの例を示しています。 

| RDFS/OWL の概念 | RDFS/OWL コンストラクト | DTDL の概念 | DTDL コンストラクト |
| --- | --- | --- | --- |
| クラス | `owl:Class`<br>IRI サフィックス<br>``rdfs:label``<br>``rdfs:comment`` | インターフェイス | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
|  をサブクラス化する | `owl:Class`<br>IRI サフィックス<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | インターフェイス | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| データ型のプロパティ | `owl:DatatypeProperty`<br>`rdfs:label` または `INode`<br>`rdfs:label`<br>`rdfs:range` | インターフェイスのプロパティ | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| オブジェクトのプロパティ | `owl:ObjectProperty`<br>`rdfs:label` または `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (`rdfs:range` がない場合は省略)<br>`comment`<br>`displayName`<br>

次の C# コード スニペットは、[**dotNetRDF**](https://www.dotnetrdf.org/) ライブラリを使用して、RDF モデル ファイルがどのようにグラフに読み込まれ、DTDL に変換されるかを示しています。 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

## <a name="validate-and-upload-dtdl-models"></a>DTDL モデルを検証してアップロードする

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

モデルの変換と検証を終えたら、**それを Azure Digital Twins インスタンスにアップロード**できます。 このプロセスの詳細については、「[*モデルのアップロード*](how-to-manage-model.md#upload-models)」セクション (*カスタム モデルを管理する方法に関するページ*) を参照してください。

## <a name="sample-converter-application"></a>サンプル コンバーター アプリケーション 

Azure Digital Twins サービスで使用するために、RDF ベースのモデル ファイルを [DTDL (バージョン 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) に変換するサンプル アプリケーションがあります。 このサンプルは、**RdfToDtdlConverter** という名前の .NET Core コマンド ライン アプリケーションです。

サンプルはここから入手できます:[**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)。 

コードをお使いのマシンにダウンロードするには、サンプルのランディング ページのタイトルの下にある *[ZIP のダウンロード]* ボタンをクリックします。 これで、*ZIP* ファイルが *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* という名前でダウンロードされます。その後、このファイルを解凍して探索することができます。

このサンプルを使用して、コンテキストでの変換パターンを確認し、独自の具体的なニーズに応じてモデルの変換を実行する独自のアプリケーションのために、構成要素として用意することができます。

## <a name="next-steps"></a>次のステップ 

デジタル ツイン モデルの設計と管理の詳細については、次の記事を参照してください。
 
* "[*概念: カスタム モデル*](concepts-models.md)"
* [*方法: カスタム モデルを管理する*](how-to-manage-model.md)"
* [*方法: モデルを解析および検証する*](how-to-parse-models.md)