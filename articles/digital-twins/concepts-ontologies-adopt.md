---
title: 業界標準のオントロジの採用
titleSuffix: Azure Digital Twins
description: Azure Digital Twins で採用できる既存の業界オントロジについて説明します。
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 71795e9dc439d5f634fc4d777aa6b5cdd66e8f5c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100563384"
---
# <a name="adopting-an-industry-ontology"></a>業界オントロジの採用

空白のページから開始するよりも、オープンソースの DTDL オントロジから開始した方が簡単な場合があるため、Microsoft は、特定分野の専門家と提携してオントロジを発行しています。これらは、広く受け入れられている業界の規則を表し、さまざまな顧客のユース ケースをサポートします。 

結果として、業界標準に基づいて学習または構築された、あるいはそれを直接使用する、一連のオープンソースの DTDL ベースのオントロジが提供されます。 このオントロジは、ダウンストリームの開発者のニーズを満たすように設計されており、業界で広く採用または拡張される可能性があります。

現時点では、Microsoft は、スマート ビルのオントロジを開発するために不動産パートナーと提携しています。このオントロジにより、業界標準に基づいてスマート ビルをモデリングするための共通基盤が提供され、再発明が回避されます。 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore スマート ビル オントロジ

Microsoft は、不動産所有者、ソフトウェア ベンダー、研究機関からなるスウェーデンのコンソーシアムである [RealEstateCore](https://www.realestatecore.io/) と提携しています。この目的は、不動産業界向けのオープンソースの  DTDL オントロジ ([**スマート ビル用の DTDL ベースの RealEstateCore オントロジ**](https://github.com/Azure/opendigitaltwins-building)) を提供することです。

このスマート ビル オントロジにより、( [BRICK スキーマ](https://brickschema.org/ontology/)や  [W3C Building Topology Ontology](https://w3c-lbd-cg.github.io/bot/index.html) などの) 業界標準を使用してスマート ビルをモデリングするための共通基盤が提供され、再発明が回避されます。 また、このオントロジには、その使用方法および適切な拡張方法に関するベスト プラクティスも付属します。 

オントロジの構造とモデリング規則、使用方法、拡張方法、寄与方法について詳しくは、オントロジの [GitHub のリポジトリ](https://github.com/Azure/opendigitaltwins-building)を参照してください。 

また、こちらのブログ投稿と付随する動画で、RealEstateCore とのパートナーシップ、およびこのイニシアティブの目標について詳細を確認できます。"[*デジタル ツインのスマート ビル オントロジ RealEstateCore が使用可能に*](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794)"

## <a name="next-steps"></a>次のステップ

* 仕様に合うように業界標準のオントロジを拡張する方法について学習します。"[*概念: 業界のオントロジの拡張*](concepts-ontologies-extend.md)"。

* または、オントロジに基づいてモデルを開発するためのパスを続行します。"[*モデル開発パスでのオントロジ戦略の使用*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)"