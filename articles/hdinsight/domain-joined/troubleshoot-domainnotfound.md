---
title: Azure HDInsight でクラスターの作成が DomainNotFound エラーで失敗する
description: Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 953feca9542d73776aee749eff416ba3f2e04921
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079968"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>シナリオ: Azure HDInsight でクラスターの作成が DomainNotFound エラーで失敗する

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

HDI Secure (Enterprise セキュリティ パッケージ) クラスターの作成が `DomainNotFound` エラー メッセージで失敗する。

## <a name="cause"></a>原因

不正確な DNS 設定。

## <a name="resolution"></a>解決方法

ドメインに参加しているクラスターがデプロイされると、HDI は AAD DS で (クラスターごとに) 内部ユーザー名とパスワードを作成し、すべてのクラスター ノードをこのドメインに参加させます。 ドメイン参加は、Samba ツールを使用して行われます。 次の前提条件が満たされていることを確認してください。

* ドメイン名は DNS によって解決される必要があります。
* ドメイン コントローラーの IP アドレスは、クラスターがデプロイされている仮想ネットワークの DNS 設定で設定する必要があります。
* 仮想ネットワークが AAD DS の仮想ネットワークとピアリングされている場合、これは手動で行う必要があります。
* DNS フォワーダーを使用している場合、ドメイン名は仮想ネットワーク内で正しく解決される必要があります。
* セキュリティ ポリシー (NSG) によってドメイン参加がブロックされないようにしてください。

### <a name="additional-debugging-steps"></a>追加のデバッグ手順

* 同じサブネットに Windows VM をデプロイし、ユーザー名とパスワードを使用してマシンをドメインに参加させます (これはコントロール パネル UI で行うことができます)。または、

* 同じサブネットに Ubuntu VM をデプロイし、マシンにドメインを参加させる
  * マシンに SSH 接続します
  * sudo su
  * ユーザー名とパスワードを使用してスクリプトを実行します
  * スクリプトによって ping が実行され、必要な構成ファイルが作成されてからドメインが作成されます。 成功した場合、DNS 設定は適切です。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]