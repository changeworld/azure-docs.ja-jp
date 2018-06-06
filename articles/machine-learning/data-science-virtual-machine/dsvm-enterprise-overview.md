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
ms.openlocfilehash: 6a755ef4d933046377a6a25be76655b44f4bf508
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2018
ms.locfileid: "34361377"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>データ サイエンス仮想マシンに基づくチーム分析と AI 環境 
[データ サイエンス仮想マシン](overview.md) (DSVM) は、AI およびデータ分析用にあらかじめ構築されているソフトウェアで、Azure クラウド上のリッチな環境を提供します。 従来の DSVM は個人の分析デスクトップとして使用されてきましたが、構築済み分析環境のこの共有概念により個々のデータサイエンティストの生産性が向上します。 データサイエンティストと AI 開発者のための分析環境を計画する大規模な分析チームに繰り返し登場するテーマの 1 つが、エンタープライズの IT ポリシーに沿って管理され、データ サイエンス/分析チーム全体のコラボレーションと一貫性も促進するような、共有分析開発および実験インフラストラクチャです。 共有インフラストラクチャにより、分析環境をいっそう有効に活用することもできます。 チーム ベースのデータ サイエンス/分析インフラストラクチャは "分析サンドボックス" と呼ばれることもあり、これによりデータ サイエンティストは、さまざまなデータ資産にアクセスしても運用環境が影響を受けないような安全な方法で、データの理解、実験、仮説の検証、予測モデルの構築を迅速に行うことができます。 

DSVM は Azure インフラストラクチャのレベルで動作するため、IT 管理者は、企業の IT ポリシーに準拠して動作するように DSVM を簡単に構成でき、制御された方法で会社のデータ資産にアクセスするさまざまな共有アーキテクチャを極めて柔軟に実装できます。 

このセクションでは、チーム ベースのデータ サイエンス インフラストラクチャとして DSVM を展開するために使用できるパターンとガイドラインについて説明します。  これらのパターンの構成要素は、Azure IaaS (サービスとしてのインフラストラクチャ) から直接利用され、どのような Azure VM にでも適用できます。 この一連の記事の具体的な目的は、Azure インフラストラクチャのこれらの標準的な機能をデータ サイエンス VM に適用することです。 

エンタープライズ チーム分析環境の主要な構成要素を次に示します。

* [自動的にスケーリングされるデータ サイエンス仮想マシンのプール](dsvm-pools.md)
* [共通の ID およびプール内の任意の DSVM からのワークスペースへのアクセス](dsvm-common-identity.md)
* [データ ソースへのセキュリティで保護されたアクセス](dsvm-secure-access-keys.md)


この一連の記事では、これらの各要素に対するガイダンスと指針を示します。 当然ながら、この一連の記事でまだ直接取り上げられていない大規模なエンタープライズ構成に DSVM を展開するときは、他にもいくつかの考慮事項とニーズがあります。 次に示すのは、そのような他の考慮事項と、エンタープライズ内の DSVM インスタンスに実装するときにすぐに利用できる一般的な Azure ドキュメントの参照です。 

* [ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/azure-network-security)
* [監視](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)と[管理](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [ログ記録と監査](https://docs.microsoft.com/azure/security/azure-log-audit)
* [ロールベースのアクセス制御](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [ポリシーの設定と適用](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [マルウェア対策](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [暗号化](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [データの探索とガバナンス](https://docs.microsoft.com/azure/data-catalog/)

[Azure アーキテクチャ センター](https://docs.microsoft.com/en-us/azure/architecture/)も優れたリソースであり、クラウド ベースの分析インフラストラクチャを構築して管理するための詳細なエンド ツー エンド アーキテクチャとパターンが提供されています。 
