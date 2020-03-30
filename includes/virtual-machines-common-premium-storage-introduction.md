---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "74005584"
---
この記事では、Azure Premium Storage を使用する高パフォーマンスのアプリケーションを構築するためのガイドラインを示します。 このドキュメントで説明する手順は、アプリケーションで使用されているテクノロジに適用できるパフォーマンスのベスト プラクティスと組み合わせて使用できます。 ガイドラインを示すために、このドキュメント全体を通じて、Premium Storage で実行されている SQL Server を例として使用しています。

この記事では、ストレージ層のパフォーマンスのシナリオに対処していますが、アプリケーション層を最適化する必要があります。 たとえば、Azure Premium Storage で SharePoint ファームをホストしている場合は、この記事の SQL Server の例を使用してデータベース サーバーを最適化できます。 さらに、最大限のパフォーマンスを得るために、SharePoint ファームの Web サーバーとアプリケーション サーバーを最適化します。

この記事は、Azure Premium Storage でのアプリケーションのパフォーマンスの最適化に関する次のような一般的な質問に答えるのに役立ちます。

* アプリケーションのパフォーマンスはどのように測定するのか。  
* 予想される高パフォーマンスが得られないのはなぜか。  
* Premium Storage でアプリケーションのパフォーマンスに影響を及ぼすのはどの要素か。  
* 各要素は Premium Storage でアプリケーションのパフォーマンスにどのような影響を及ぼすのか。  
* IOPS、帯域幅、待機時間に最適化するにはどうすればよいか。  

Premium Storage で実行されるワークロードは高パフォーマンスに依存するため、Premium Storage 専用のガイドラインを用意しました。 必要に応じて例も示しています。 これらのガイドラインの一部は、Standard Storage ディスクを使用する IaaS VM で実行されるアプリケーションにも適用できます。