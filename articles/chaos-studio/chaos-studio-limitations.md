---
title: Azure Chaos Studio の制限事項と既知の問題
description: Azure Chaos Studio を使用する場合の現在の制限事項と既知の問題について説明します。
services: chaos-studio
author: johnkemnetz
ms.topic: overview
ms.date: 11/02/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: b4df4b7f06c999bfc29154fbbed4133c2664dad8
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372456"
---
# <a name="azure-chaos-studio-preview-limitations-and-known-issues"></a>Azure Chaos Studio (プレビュー) の制限事項と既知の問題

Azure Chaos Studio のパブリック プレビュー期間中は、チームが認識し、解決に取り組んでいるいくつかの制限事項と既知の問題があります。

## <a name="limitations"></a>制限事項 

* エージェントベースの障害では、マシンは次の **HTTPS エンドポイント** にアクセスできる必要があります。
    * http://agentcommunicationservice-frontdoor-canary.trafficmanager.net 
    * テレメトリ データを Application Insights に送信する場合、[このドキュメント](../azure-monitor/app/ip-addresses.md)にある IP も必要です。
* Chaos Agent を利用した実験を実行する場合、VM で次のいずれかの **オペレーティング システム** が実行されている必要があります。
    * Windows Server 2019、Windows Server 2016、Windows Server 2012、Windows Server 2012 R2
    * Redhat Enterprise Linux 8.2、SUSE Enterprise Linux 15 SP2、CentOS 8.2、Debian 10 Buster (unzip のインストールが必要)、Oracle Linux 7.8 Ubuntu Server 16.04 LTS、Ubuntu Server 18.04 LTS
* Chaos Agent は、カスタム Linux ディストリビューション、強化された Linux ディストリビューション (FIPS や SELinux など) に対してテストされていません
* Chaos Studio ポータル エクスペリエンスは、次のブラウザーでのみテストされています。
    * **Windows:** Microsoft Edge、Google Chrome、Firefox
    * **MacOS:** Safari、Google Chrome、Firefox

## <a name="known-issues"></a>既知の問題
* Azure portal で **エージェントベースのターゲット エクスペリエンスを有効** にしても、仮想マシンまたは仮想マシン スケール セットにユーザー割り当てマネージド ID は割り当てられません。 これは手動で行う必要があります。そうしないと、実験のエージェントベースのフォールトが [Verify that the target is correctly onboarded and proper read permissions are provided to the experiment msi]\(ターゲットが正しくオンボードされていて、実験の msi に適切な読み取りアクセス許可が提供されていることを確認してください\) というエラーにより失敗します。 この操作は、エージェントベースのターゲットを有効にした後で行うことができますが、再起動が必要になる場合があります。
* Azure portal でターゲットをオンボードするとき、オンボードが完了する前にターゲット ビューから別の場所に移動すると、そのオンボードが失敗する場合があります。
* 実験を作成するとき、 **[確認および作成]** をクリックした後、作成された実験が実験リストに表示されるまで時間がかかり、ユーザーはリストに実験を表示するために更新操作を行う必要があります。
* 実験デザイナーでエージェントベースの障害のターゲット リソースを選ぶとき、選択した障害でサポートされていないオペレーティング システムを備えた仮想マシンまたは仮想マシン スケール セットを選ぶことができます。


## <a name="next-steps"></a>次のステップ
以下のリンクを使用して、Chaos Studio で、アプリケーションの回復性を向上させるカオス実験の作成と実行を開始します。
- [最初の実験を作成して実行する](chaos-studio-tutorial-service-direct-portal.md)
- [カオス エンジニアリングの詳細を学習する](chaos-studio-chaos-engineering-overview.md)
