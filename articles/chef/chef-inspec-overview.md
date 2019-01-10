---
title: Azure インフラストラクチャのコンプライアンス オートメーションのために InSpec を使用する
description: InSpec を使用して Azure のデプロイの問題を検出する方法について説明します。
keywords: azure、chef、devops、仮想マシン、概要、自動化、inspce
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 9256a4daf6564761553b495e559805a46e4eae32
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050729"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Azure インフラストラクチャのコンプライアンス オートメーションのために InSpec を使用する
[InSpec](https://www.chef.io/inspec/) は、アプリケーションとインフラストラクチャのテストおよび監査のための無料のオープン ソース フレームワークです。 InSpec は、システムの実際の状態と、ユーザーが読みやすく作成しやすい InSpec コードを使って表現した、目標とする状態を比較します。 InSpec は、違反を検出し、結果をレポートの形式で表示しますが、修復はユーザーが管理します。 InSpec を使用して、Azure で実行されている仮想マシンの状態を検証することができます。 また、サブスクリプション内のリソースおよびリソース グループの状態をスキャンおよび検証するために InSpec を使用することができます。

この記事では、InSpec を使用してセキュリティとコンプライアンスを Azure でより簡単に実現する利点について説明します。

## <a name="make-compliance-easy-to-understand-and-assess"></a>コンプライアンスの理解と評価を簡易化する
InSpec を使用すると、バージョン管理された、実行可能な、人間が判読できるコードに要件を変換できます。 これにより、必要に応じて例外を定義したりカスタマイズしたコンポーザブル プロファイルを使ってテストを構成できます。

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Fleet 全体の問題を検出し、その修復の優先順位を付ける
InSpec のエージェントレス検出モードを使用すると、大規模にわたって露出レベルをすばやく評価できます。 影響/重要度スコア付けのための組み込みメタデータにより、修復を絞り込む領域を特定できます。

## <a name="inspect-machines-data-and-new-saas-apis"></a>マシン、データ、および新しい SaaS API を検査する
InSpec クラウド API コンプライアンス機能を使用して、クラウドのコンプライアンスに関する大まかな粒度と細かい粒度の両方のアサーションを作成でき、継続的に報告できます。

## <a name="satisfy-audits"></a>監査の基準に適合する
InSpec を使用すれば、四半期、年 1 回などのあらかじめ決められた間隔だけでなく、いつでも監査質問に答えることができます。 InSpec では、監査周期を入力できるため、コンプライアンスに対する態勢を正確に把握し、監査者から予想外の結果を知らされてあわてることがなくなります。

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>ルールに関するあいまいさと誤解を低減
ドキュメントを使って構成とプロセスを解釈できます。 実行可能コードにより、明確な意図をもって具体的なテストのために何を評価すべきかについて討議する必要がなくなります。

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>急速に変化し続ける脅威とコンプライアンスのランドスケープに対応
InSpec を使用すると、1 日で検出コードを作成して発行し、新しい規制に迅速に対応して新しい規則を作成できます。 これにより、脅威または規制の変更が、緊急事態ではなくなります。

## <a name="next-steps"></a>次の手順
* [Chef を使用して Windows 仮想マシンを Azure 上に作成する](/azure/virtual-machines/windows/chef-automation)