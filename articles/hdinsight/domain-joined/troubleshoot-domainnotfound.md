---
title: Azure HDInsight でクラスターの作成が DomainNotFound エラーで失敗する
description: Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 79cd3b687dc8a2eb838146a6dc8a40b184a55fe6
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285568"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>シナリオ:Azure HDInsight でクラスターの作成が DomainNotFound エラーで失敗する

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

HDI Secure (Enterprise セキュリティ パッケージ) クラスターの作成が `DomainNotFound` エラー メッセージで失敗する。

## <a name="cause"></a>原因

不正確な DNS 設定。

## <a name="resolution"></a>解像度

ドメインに参加しているクラスターがデプロイされると、HDI は AAD DS で (クラスターごとに) 内部ユーザー名とパスワードを作成し、すべてのクラスター ノードをこのドメインに参加させます。 ドメイン参加は、Samba ツールを使用して行われます。 次の前提条件が満たされていることを確認してください。

* ドメイン名は DNS によって解決される必要があります。
* ドメイン コントローラーの IP アドレスは、クラスターがデプロイされている仮想ネットワークの DNS 設定で設定する必要があります。
* 仮想ネットワークが AAD DS の仮想ネットワークとピアリングされている場合、これは手動で行う必要があります。
* DNS フォワーダーを使用している場合、ドメイン名は仮想ネットワーク内で正しく解決される必要があります。
* セキュリティ ポリシー (NSG) によってドメイン参加がブロックされないようにしてください。

### <a name="additional-debugging-steps"></a>追加のデバッグ手順

* 同じサブネットに Windows VM をデプロイし、ユーザー名とパスワードを使用してマシンをドメインに参加させます (これはコントロール パネル UI で行うことができます)。または、

* 同じサブネットに ubuntu VM をデプロイし、マシンをドメインに参加させます
  * マシンに SSH 接続します
  * sudo su
  * ユーザー名とパスワードを使用してスクリプトを実行します
  * スクリプトによって ping が実行され、必要な構成ファイルが作成されてからドメインが作成されます。 成功した場合、DNS 設定は適切です。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]