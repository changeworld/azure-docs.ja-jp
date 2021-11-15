---
title: 業界標準のオントロジの採用
titleSuffix: Azure Digital Twins
description: Azure Digital Twins で採用できる既存の業界オントロジについて説明します。
author: baanders
ms.author: baanders
ms.date: 10/27/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 936df82dc8c7c5babcb3b1844f2d53f1396dbd8b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500661"
---
# <a name="adopting-an-industry-ontology"></a>業界オントロジの採用

この記事では、ソリューションを簡略化するために採用できる、一連のさまざまな業界標準オントロジーについて説明します。

Microsoft は、特定分野の専門家と提携してオントロジを公開しています。空白のページから始めるより、オープンソースの Digital Twins Definition Language (DTDL) オントロジから開始した方が簡単な場合があるためです。 これらのオントロジは、広く受け入れられている業界の規則を表しており、さまざまな顧客のユース ケースをサポートします。 

その結果、業界標準から学習するか、それに基づいて構築されるか、またはそれを直接使用する、オープンソースの DTDL ベースの一連のオントロジが提供されます。 これらのオントロジは、ダウンストリームの開発者のニーズを満たすように設計されているため、業界で広く採用され、さらに拡張される可能性があります。

現在、Microsoft はパートナーとの共同作業で[スマート ビルディング](#realestatecore-smart-building-ontology)、[スマート シティ](#smart-cities-ontology)、[エネルギー グリッド](#energy-grid-ontology)のためのオントロジを開発しています。 これらのオントロジにより、これらの業界の標準に基づいたモデリングのための共通基盤が提供され、再発明の必要性を回避できます。 

各オントロジでは、初期のモデル セットに焦点が絞られています。 オントロジの作成者は、初期のユース ケース セットを拡張し、既存のモデルを改良するために強力してくれるユーザーの参加を歓迎します。 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore スマート ビル オントロジ

*オントロジは次のレポジトリから所得してください:* [スマート ビルに関する Digital Twins Definition Language ベースの RealEstateCore オントロジ](https://github.com/Azure/opendigitaltwins-building)。

Microsoft は [RealEstateCore](https://www.realestatecore.io/) と提携し、不動産業界のためにこのオープンソース DTDL オントロジを提供しました。 [RealEstateCore](https://www.realestatecore.io/) は、不動産所有者、ソフトウェア ベンダー、研究機関からなるスウェーデンのコンソーシアムです。

このスマート ビル オントロジにより、( [BRICK スキーマ](https://brickschema.org/ontology/)や  [W3C Building Topology Ontology](https://w3c-lbd-cg.github.io/bot/index.html) などの) 業界標準を使用してスマート ビルをモデリングするための共通基盤が提供され、再発明が回避されます。 また、このオントロジには、その使用方法および適切な拡張方法に関するベスト プラクティスも付属します。 

このオントロジの構造とモデリング規則、使用方法、拡張方法、参加方法について詳しくは、オントロジの GitHub のリポジトリ ([Azure/opendigitaltwins-building](https://github.com/Azure/opendigitaltwins-building)) を参照してください。 

また、[デジタル ツインのスマート ビル オントロジ RealEstateCore が使用可能](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794)になったことに関するブログ投稿と埋め込み動画で、RealEstateCore とのパートナーシップ、およびこのイニシアティブの目標について詳細をご確認いただけます。

## <a name="smart-cities-ontology"></a>スマート シティのオントロジ

*オントロジは次のレポジトリから所得してください:* [スマート シティ用の Digital Twins Definition Language (DTDL) オントロジ](https://github.com/Azure/opendigitaltwins-smartcities)。

Microsoft は、[Open Agile Smart Cities (OASC)](https://oascities.org/) および [Sirus](https://sirus.be/) と共同で、[ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim) を始めとする、スマート シティに関する DTDL ベースのオントロジを提供しています。 ETSI NGSI-LD に加え、Saref4City、CityGML、ISO なども評価しています。

オントロジと、その使用方法、参加方法について詳しくは、このオントロジの GitHub のリポジトリ ([Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities)) を参照してください。 

スマート シティに関するパートナーシップとアプローチの詳細については、[Digital Twins のスマート シティ オントロジ](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585)に関するブログ記事と埋め込み動画も参照してください。

## <a name="energy-grid-ontology"></a>エネルギー グリッド オントロジ

*このオントロジは次のリポジトリから入手してください:* [エネルギー グリッド用の Digital Twins Definition Language (DTDL) オントロジ](https://github.com/Azure/opendigitaltwins-energygrid/)。

このオントロジは、ソリューション プロバイダーがエネルギーのユース ケース (グリッド資産の監視、停電と影響の分析、シミュレーション、予測メンテナンス) のためのデジタル ツイン ソリューションの開発を加速できるように作られました。 また、このオントロジは、エネルギー グリッドのデジタル変革と最新化を可能にする目的で利用できます。 これは、エネルギー グリッド資産管理、電力システム運用モデリング、物理的エネルギー商品市場のグローバル標準である [Common Information Model (CIM)](https://cimug.ucaiug.org/) から適応されています。

オントロジと、その使用方法、参加方法について詳しくは、このオントロジの GitHub のリポジトリ ([Azure/opendigitaltwins-energygrid](https://github.com/Azure/opendigitaltwins-energygrid/)) を参照してください。 

エネルギー グリッドに関するパートナーシップとアプローチの詳細については、[Digital Twins のエネルギー グリッド オントロジ](https://techcommunity.microsoft.com/t5/internet-of-things/energy-grid-ontology-for-digital-twins-is-now-available/ba-p/2325134)に関するブログ記事も参照してください。

## <a name="next-steps"></a>次のステップ

* 仕様を満たすように業界標準のオントロジを拡張する方法の詳細について学習します。[業界のオントロジの拡張](concepts-ontologies-extend.md)に関する記事。

* または、オントロジに基づいてモデルを開発するためのパスを続行します。[モデル開発パスでのオントロジ戦略の使用](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)に関する記事。