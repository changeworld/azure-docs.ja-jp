---
title: バージョン リリース履歴
titleSuffix: Microsoft Genomics
description: 修正プログラムおよび新しい機能に関する Microsoft Genomics Python クライアントの更新のリリース履歴。
services: genomics
author: vigunase
manager: cgronlun
ms.author: vigunase
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 3023f01b9a9ccbc3a56c509b1edc86885f2af41e
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255692"
---
# <a name="version-release-history"></a>バージョン リリース履歴
Microsoft Genomics チームは、修正プログラムおよび新しい機能のために Microsoft Genomics Python クライアントを更新します。 

## <a name="latest-release"></a>最新リリース
現在の Python クライアントは、バージョン 0.9.0 です。 これは 2019 年 2 月 6 日にリリースされ、GATK 3.5 および GATK4 を使用したワークフローの実行がサポートされています。 gVCF 出力がサポートされ、オプションの引数を使用して出力圧縮を行うことができます。


## <a name="release-history"></a>リリース履歴 
Microsoft Genomics Python クライアントの新しいバージョンは、ほぼ年に 1 回リリースされます。 Microsoft Genomics Python クライアントの新しいバージョンがリリースされると、このページの修正プログラムと機能の一覧が更新されます。 新しいバージョンがリリースされると、以前のバージョンは少なくとも 90 日間、引き続きサポートされます。 以前のバージョンがサポートされなくなった場合は、このページで告知されます。 

### <a name="version-090"></a>バージョン 0.9.0
バージョン 0.9.0 には、出力圧縮のサポートが含まれています。 これは、vcf または gvcf の出力で `-bgzip` に続けて `-tabix` を実行するのと同じです。 詳細については、[よく寄せられる質問](frequently-asked-questions-genomics.yml)をご覧ください。 

### <a name="version-081"></a>バージョン 0.8.1
バージョン 0.8.1 には、軽微なバグの修正が含まれています。  

### <a name="version-080"></a>バージョン 0.8.0
バージョン 0.8.0 には、GATK4 と gVCFs 出力のサポートが含まれています。  

### <a name="version-074"></a>バージョン 0.7.4
バージョン 0.7.4 には、`config.txt` の入力で、アカウント キーの代わりに SAS トークンを受け入れることのサポートが含まれています。 詳細については、[SAS トークン入力のクイック スタート](quickstart-input-sas.md)に関する記事を参照してください。 

### <a name="version-073"></a>バージョン 0.7.3
バージョン 0.7.3 には、軽微なバグの修正が含まれています。

### <a name="version-072"></a>バージョン 0.7.2
バージョン 0.7.2 は初期バージョンです。 2017 年 11 月 1 日にリリースされました。
