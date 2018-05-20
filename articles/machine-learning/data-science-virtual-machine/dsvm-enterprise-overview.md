---
title: データ サイエンス仮想マシンに基づくチーム環境の概要 - Azure | Microsoft Docs
description: エンタープライズ チーム環境としてデータ サイエンス VM を展開するためのパターンについて説明します。
keywords: ディープ ラーニング, AI, データ サイエンス ツール, データ サイエンス仮想マシン, 地理空間分析, チーム データ サイエンス プロセス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 911d6484421cc9fddad0530bf8d9ab4f01d48bf8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>データ サイエンス仮想マシンに基づくチーム分析と AI 環境 
[データ サイエンス仮想マシン](overview.md) (DSVM) は、AI およびデータ分析用にあらかじめ構築されているソフトウェアで、Azure クラウド上のリッチな環境を提供します。 従来の DSVM は個人の分析デスクトップとして使用されてきましたが、構築済み分析環境のこの共有概念により個々のデータサイエンティストの生産性が向上します。 データサイエンティストと AI 開発者のための分析環境を計画する大規模な分析チームに繰り返し登場するテーマの 1 つが、エンタープライズの IT ポリシーに沿って管理され、データ サイエンス/分析チーム全体のコラボレーションと一貫性も促進するような、共有分析開発および実験インフラストラクチャです。 共有インフラストラクチャにより、分析環境をいっそう有効に活用することもできます。 チーム ベースのデータ サイエンス/分析インフラストラクチャは "分析サンドボックス" と呼ばれることもあり、これによりデータ サイエンティストは、さまざまなデータ資産にアクセスしても運用環境が影響を受けないような安全な方法で、データの理解、実験、仮説の検証、予測モデルの構築を迅速に行うことができます。 

DSVM は Azure インフラストラクチャのレベルで動作するため、IT 管理者は、企業の IT ポリシーに準拠して動作するように DSVM を簡単に構成でき、制御された方法で会社のデータ資産にアクセスするさまざまな共有アーキテクチャを極めて柔軟に実装できます。 

このセクションでは、チーム ベースのデータ サイエンス インフラストラクチャとして DSVM を展開するために使用できるパターンとガイドラインについて説明します。  これらのパターンの構成要素は、Azure IaaS (サービスとしてのインフラストラクチャ) から直接利用され、どのような Azure VM にでも適用できます。 この一連の記事の具体的な目的は、Azure インフラストラクチャのこれらの標準的な機能をデータ サイエンス VM に適用することです。 

エンタープライズ チーム分析環境の主要な構成要素を次に示します。

* [自動的にスケーリングされるデータ サイエンス仮想マシンのプール](dsvm-pools.md)
* [共通の ID およびプール内の任意の DSVM からのワークスペースへのアクセス](dsvm-common-identity.md)
* [データ ソースへのセキュリティで保護されたアクセス](dsvm-secure-access-keys.md)
* 企業データ セットとオープン データ セットのガバナンスと検出

この一連の記事では、これらの各要素に対するガイダンスと指針を示します。 [Azure アーキテクチャ センター](https://docs.microsoft.com/en-us/azure/architecture/)では、分析インフラストラクチャのためのさらに詳細なエンド ツー エンドのアーキテクチャが提供されています。  
