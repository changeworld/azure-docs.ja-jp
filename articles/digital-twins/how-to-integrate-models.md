---
title: 業界標準モデルを統合する
titleSuffix: Azure Digital Twins
description: 特別な DTDL オントロジーを使用するか、既存のオントロジーを変換して、業界標準モデルを Azure Digital Twins 用の DTDL に統合する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1866e9b233a1379474c061779ada09fd6d119107
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338666"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>業界標準モデルを Azure Digital Twins 用の DTDL に統合する

業界標準に基づくモデルや、RDF または OWL などの標準的なオントロジー表現を使用するモデルを使用すると、Azure Digital Twins モデルを設計する場合に充実した出発点を利用できます。 業界モデルの利用は、標準化と情報共有にも役立ちます。

Azure Digital Twins で使用するモデルは、JSON-LD ベースの [**Digital Twins Definition Language (DTDL)** ](concepts-models.md) で表現する必要があります。 そのため、この記事では、既存の業界概念を DTDL セマンティクスと統合して、Azure Digital Twins で使用できるように、業界標準モデルを DTDL で表現する方法について説明します。 DTDL モデルは、その後、Azure Digital Twins 内で、モデルの信頼できるソースとして使用されます。

業界標準モデルを DTDL に統合するには、次の 3 つのパスがあります。
* **導入**: 広く採用されている業界標準に基づいて構築されたオープンソースの DTDL オントロジーを使用して、ソリューションを開始できます。 
* **変換**: 既存のモデルが既にある場合は、それらを DTDL に変換する必要があります。
* **作成**: 独自のカスタム DTDL モデルをいつでも一から開発できます。[*カスタム モデルを管理する方法*](how-to-manage-model.md)に関するページを参照してください。

## <a name="adopt-an-open-source-dtdl-ontology"></a>オープンソースの DTDL オントロジーを導入する

多くの場合、空のページから開始するよりも、オープンソースの DTDL オントロジーから始める方が簡単です。 

たとえば、スマート ビルディング ソリューションでは、オープンソースの [**DTDL ベースの RealEstateCore オントロジー**](https://github.com/Azure/opendigitaltwins-building)を活用できます。これにより、業界標準を活用して再発明を防ぎながらスマート ビルディングをモデル化するための共通の基盤が提供されます。 

これらのオープンソース DTDL オントロジーには、モデルを使用および適切に拡張する方法についてもベスト プラクティスが用意されています。 

## <a name="convert-existing-models-to-dtdl"></a>既存のモデルを DTDL に変換する

ほとんどの業界モデル (**オントロジー** とも呼ばれます) は、[OWL](https://www.w3.org/OWL/)、[RDF](https://www.w3.org/2001/sw/wiki/RDF)、[RDFS](https://www.w3.org/2001/sw/wiki/RDFS) などのセマンティック Web 標準に基づいています。 

Azure Digital Twins でモデルを使用するには、それが DTDL 形式である必要があります。 このセクションでは、RDF ベースのモデルを DTDL に変換して Azure Digital Twins で使用できるようにするための **変換パターン** の形式で、一般的な設計ガイダンスを説明します。 

また、[RDF コンバーターの **サンプル コンバーター コード**](#sample-converter-application)も含まれています。これは [Brick](https://brickschema.org/ontology/) スキーマで検証済みであり、ビルド業界の他のスキーマ向けに拡張することができます。

### <a name="conversion-pattern"></a>変換パターン

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

### <a name="sample-converter-application"></a>サンプル コンバーター アプリケーション 

Azure Digital Twins サービスで使用するために、RDF ベースのモデル ファイルを [DTDL (バージョン 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) に変換するサンプル アプリケーションがあります。 これは、[Brick](https://brickschema.org/ontology/) スキーマで検証済みであり、ビルド業界の他のスキーマ向けに拡張することができます ([Building Topology Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/)、[Semantic Sensor Network](https://www.w3.org/TR/vocab-ssn/)、[buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) など)。

このサンプルは、**RdfToDtdlConverter** という名前の .NET Core コマンド ライン アプリケーションです。

サンプルはここから入手できます:[**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)。 

コードをお使いのマシンにダウンロードするには、サンプルのランディング ページのタイトルの下にある *[ZIP のダウンロード]* ボタンをクリックします。 これで、*ZIP* ファイルが *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* という名前でダウンロードされます。その後、このファイルを解凍して探索することができます。

このサンプルを使用して、コンテキストでの変換パターンを確認し、独自の具体的なニーズに応じてモデルの変換を実行する独自のアプリケーションのために、構成要素として用意することができます。

## <a name="validate-and-upload-dtdl-models"></a>DTDL モデルを検証してアップロードする

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

モデルの変換と検証を終えたら、**それを Azure Digital Twins インスタンスにアップロード** できます。 このプロセスの詳細については、「[*モデルのアップロード*](how-to-manage-model.md#upload-models)」セクション (*カスタム モデルを管理する方法に関するページ*) を参照してください。

## <a name="next-steps"></a>次のステップ 

デジタル ツイン モデルの設計と管理の詳細については、次の記事を参照してください。
 
* "[*概念: カスタム モデル*](concepts-models.md)"
* [*方法: カスタム モデルを管理する*](how-to-manage-model.md)"
* [*方法: モデルを解析および検証する*](how-to-parse-models.md)
