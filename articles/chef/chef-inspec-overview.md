---
title: Azure インフラストラクチャのコンプライアンス オートメーションのために InSpec を使用する
description: InSpec を使用して Azure のデプロイの問題を検出する方法について説明します。
keywords: azure、chef、devops、仮想マシン、概要、自動化、inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: a5de2ca04a193f97a2a65a043f744abb8e0ea758
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359223"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Azure インフラストラクチャのコンプライアンス オートメーションのために InSpec を使用する
[InSpec](https://www.chef.io/inspec/) は、ソフトウェア エンジニア、運用、およびセキュリティ エンジニアの間で共有できるセキュリティとコンプライアンスの規則を記述するための、Chef のオープン ソース言語です。 InSpec は、インフラストラクチャの実際の状態と、ユーザーが読みやすく作成しやすい InSpec コードを使って表現した、目標とする状態を比較する動作を行います。 InSpec は、違反を検出し、結果をレポートの形式で表示しますが、修復はユーザーが管理します。

仮想マシン、ネットワーク構成、Azure Active Directory の設定などを含めて、InSpec を使用してサブスクリプション内のリソースとリソース グループの状態を検証することができます。

この記事では、InSpec を使用してセキュリティとコンプライアンスを Azure でより簡単に実現する利点について説明します。

## <a name="make-compliance-easy-to-understand-and-assess"></a>コンプライアンスの理解と評価を簡易化する
スプレッドシートや Word 文書に記述されたコンプライアンス ドキュメントでは、要件の解釈が定まらないままになります。 InSpec を使用すると、バージョン管理された、実行可能な、人間が判読できるコードに要件を変換できます。 コードにより、明確な意図をもって具体的なテストのために何を評価する必要があるかを討議する必要がなくなります。

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Fleet 全体の問題を検出し、その修復の優先順位を付ける
InSpec のエージェントレス検出モードを使用すると、露出レベルを大規模にすばやく評価できます。 影響/重要度スコア付けのための組み込みメタデータにより、修復を絞り込む領域を特定できます。 新しい脆弱性や規制に対応して規則をすばやく記述し、すぐにロールアウトすることができます。

## <a name="satisfy-audits"></a>監査の基準に適合する
InSpec を使用すれば、四半期、年 1 回などのあらかじめ決められた間隔だけでなく、いつでも監査質問に答えることができます。 InSpec のテストを継続的に実施することで、コンプライアンスの体制と履歴を正確に知る監査周期に入ることになるため、監査者の発見に驚かされることがなくなります。

## <a name="next-steps"></a>次の手順

* Azure Cloud Shell で InSpec を試す[![Cloud Shell を起動する](https://shell.azure.com/images/launchcloudshell.png "Cloud Shell を起動する")](https://shell.azure.com)
