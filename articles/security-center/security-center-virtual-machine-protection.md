---
title: Azure Security Center でのマシンとアプリケーションの保護 | Microsoft Docs
description: このドキュメントでは、仮想マシン、コンピューター、Web アプリ、および App Service 環境を保護するのに役立つ、Security Center の推奨事項について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2018
ms.author: rkarlin
ms.openlocfilehash: 1692e111d48a6e4574b2b114c0de84d9bc9f3203
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299833"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Azure Security Center でのマシンとアプリケーションの保護
Azure セキュリティ センターは、Azure リソースのセキュリティの状態を分析します。 潜在的なセキュリティの脆弱性を識別すると、Security Center は、必要な管理を構成するプロセスを説明する推奨事項を作成します。 推奨事項は、仮想マシン (VM)、コンピューター、アプリケーション、ネットワーク、SQL、および ID とアクセスといった、Azure のリソースの種類に適用されます。

この記事では、マシンとアプリケーションに適用される推奨事項について説明します。

## <a name="monitoring-security-health"></a>セキュリティ ヘルスの監視
リソースのセキュリティ状態は、**[Security Center - 概要]** ダッシュボードで監視できます。 **[リソース]** セクションには、識別された問題の数と、リソースの種類ごとのセキュリティの状態が表示されます。

**[推奨事項]** を選択すると、すべての問題の一覧が表示されます。 レコメンデーションの適用方法の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」を参照してください。

計算とアプリ サービスの推奨事項の完全な一覧については、[推奨事項](security-center-virtual-machine-recommendations.md)に関するページを参照してください。

続行するには、**[リソース]** または Security Center のメイン メニューで **[計算とアプリ]** を選択します。
![Security Center ダッシュボード][1]

## <a name="monitor-compute-and-app-services"></a>計算とアプリ サービスの監視
**[Compute]** には、次の 4 つのタブがあります。

- **[概要]**: Security Center によって特定された監視および推奨事項。
- **[VM およびコンピューター]**: VM およびコンピューターの一覧と、それぞれの現在のセキュリティ状態。
- **[Cloud Services]\(クラウド サービス\)**: Security Center によって監視されている Web および worker ロールの一覧。
- **[App Services (プレビュー)]**: アプリ サービス環境の一覧と、それぞれの現在のセキュリティ状態。
続行するには、**[リソース]** または Security Center のメイン メニューで **[計算とアプリ]** を選択します。

![コンピューティング][2]

各タブには複数のセクションを表示できます。各セクションでは、個別のオプションを選択して、特定の問題に対処するために推奨される手順の詳細を確認できます。

### <a name="monitoring-recommendations"></a>監視に関する推奨事項
このセクションには、自動プロビジョニング用に初期化された VM とコンピューターの総数と、その現在の状態が表示されます。 この例では、**[Monitoring agent health issues]\(エージェントの正常性の問題の監視\)** という推奨事項が 1 つ存在します。 この推奨事項を選択します。

![エージェントの正常性の問題の監視][3]

**[Monitoring agent health issues]\(エージェントの正常性の問題の監視\)** が表示されます。 Security Center で正常に監視できない VM とコンピューターが一覧表示されます。 詳細については、VM またはコンピューターを選択します。 Security Center で監視できない理由が **[監視の状態]** に表示されます。 **[監視の状態]** の値、説明、解決手順を紹介した一覧については、[Security Center トラブルシューティング ガイド](security-center-troubleshooting-guide.md)を参照してください。

### 監視対象外の VM およびコンピューター<a name="unmonitored-vms-and-computers"></a>
VM またはコンピューターは、マシンで Microsoft Monitoring Agent 拡張機能が実行されていない場合、Security Center によって監視されません。 マシンには、既にローカル エージェントがインストールされている場合があります (OMS ダイレクト エージェント、SCOM エージェントなど)。 これらのエージェントがインストールされているマシンは、監視対象外と見なされます。Security Center ではこれらのエージェントが完全にはサポートされないためです。 Security Center に備わっているあらゆる機能の利点を最大限に活かすためには、Microsoft Monitoring Agent 拡張機能が必要です。

監視対象外の VM またはコンピューターには、あらかじめインストールされているローカル エージェントに加えて、この拡張機能をインストールすることができます。 両方のエージェントを同じように構成し、同じワークスペースに接続してください。 そうすることで、Security Center が Microsoft Monitoring Agent 拡張機能と対話してデータを収集できる状態になります。 Microsoft Monitoring Agent 拡張機能をインストールする手順については、[VM 拡張機能の有効化](../log-analytics/log-analytics-quick-collect-azurevm.md)に関するページを参照してください。

自動プロビジョニングの対象として初期化された VM およびコンピューターを Security Center で正常に監視できない理由について詳しくは、「[Monitoring agent health issues (エージェントの正常性の問題の監視)](security-center-troubleshooting-guide.md#mon-agent)」を参照してください。

### <a name="recommendations"></a>Recommendations
このセクションには、Security Center が監視する各 VM とコンピューター、Web および worker ロール、Azure App Service Web Apps、および Azure App Service Environment に対する推奨事項のセットがあります。 最初の列には、推奨事項が一覧表示されます。 2 列目には、その推奨事項の影響を受けるリソースの合計数が表示されます。 3 番目の列は、以下のスクリーンショットに示したように、問題の重大度を表します。

![Recommendations][4]

各推奨事項にはいくつかのアクションが示されており、選択して実行できます。 たとえば、**[システムの更新プログラムがありません]** を選択すると、パッチが適用されていない VM とコンピューター、および不足している更新プログラムの重大度が、以下のスクリーンショットのように一覧表示されます。

![システムの更新の適用][5]

**[システムの更新プログラムを適用する]** には、Windows と Linux についてそれぞれ、重大な更新プログラムの概要がグラフ形式で表示されます。 2 つ目の領域には、次の情報を含んだ表が表示されます。

- **[名前]**: 不足している更新プログラムの名前。
- **[VM とコンピューターの数]**: この更新プログラムが適用されていない VM とコンピューターの合計台数です。
- **[更新プログラムの重要度]**: 特定の推奨事項の重大度を示します。

    - **[Critical]\(重大\)**: 重要なリソース (アプリケーション、仮想マシン、ネットワーク セキュリティ グループ) に脆弱性が存在しており、注意が必要。
    - **[重要]**: プロセスを完了または脆弱性を排除するために重大ではないまたは追加の手順が必要。
    - **[中]**: 対処する必要はあるものの、直ちに注意する必要はない脆弱性が存在する  (既定では、重要度の低い推奨事項は表示されないが、重要度の低い推奨事項にフィルターを適用すると表示できる)。


- **[状態]**: 推奨事項の現在の状態。

    - **[オープン]**: 推奨事項への対処がまだ行われていない。
    - **[処理中]**: 現在、リソースへの推奨事項の適用を進めており、ユーザーのアクションは不要。
    - **解決**: 推奨事項が既に完了済み  (問題が解決されていると、エントリが灰色表示になります)。

推奨事項の詳細を表示するには、不足している更新プログラムの名前を一覧内でクリックします。

![推奨事項の詳細][6]

> [!NOTE]
> ここに示されるセキュリティの推奨事項は、**[推奨事項]** タイルに表示されているものと同じです。 推奨事項の解決方法の詳細については、[Azure Security Center でのセキュリティに関する推奨事項の実装](security-center-recommendations.md)に関するページを参照してください。
>
>

### <a name="vms-and-computers"></a>VM およびコンピューター
[VM およびコンピューター] セクションには、すべての VM とコンピューターを対象とした推奨事項の概要が表示されます。 各列は、以下のスクリーンショットに示したように、推奨事項の 1 つのセットを表します。

![VM とコンピューターの推奨事項][7]

この一覧には、次の 4 種類のアイコンが使用されます。

![Azure 以外のコンピューター][8] Azure 以外のコンピューター。

![Azure Resource Manager VM][9] Azure Resource Manager VM。

![Azure クラシック VM][10] Azure クラシック VM。

![ワークスペースから確認できる VM][11] 表示されているサブスクリプションに属しているワークスペースからのみ確認できる VM。 たとえば、このサブスクリプションのワークスペースの管理下にある他のサブスクリプションの VM や、SCOM ダイレクト エージェントがインストールされた VM のうちリソース ID のないものが該当します。

各推奨事項の下に表示されるアイコンにより、注意が必要な VM とコンピューター、その推奨事項の種類をすばやく把握できます。 この画面に表示されるオプションは、[フィルター] オプションを使用して選択することもできます。

![filter][12]

前の例では、1 つの VM にエンドポイント保護に関する重要な推奨事項があります。 さらに情報を得るには、その VM を選択します。

![重要な推奨事項][13]

ここには VM またはコンピューターのセキュリティに関する詳細が表示されます。 一番下には、推奨されるアクションと、各問題の重大度が表示されます。

### <a name="cloud-services"></a>クラウド サービス
クラウド サービスについては、オペレーティング システムのバージョンが最新でないときに、次のスクリーンショットに示したような推奨事項が作成されます。

![クラウド サービス][14]

シナリオで推奨事項が発生した場合 (前の例には該当しません)、推奨事項の手順に従ってオペレーティング システムのバージョンを更新する必要があります。 更新プログラムが利用できる場合はアラートが表示されます (問題の重大度により、赤またはオレンジになります)。 WebRole1 (IIS に自動的にデプロイされた Web アプリで Windows Server を実行) または WorkerRole1 (IIS に自動的にデプロイされた Web アプリで Windows Server を実行) の行でこのアラートを選択すると、次のスクリーンショットに示すとおり、推奨事項に関する詳細が表示されます。

![WorkerRole1][15]

この推奨事項に関するより正確な説明を確認する場合は、**[説明]** 列の **[OS バージョンの更新]** をクリックしてください。

![OS バージョンの更新][16]

### <a name="app-services-preview"></a>App Service (プレビュー)

> [!NOTE]
> App Service の監視はプレビュー段階であり、Security Center の Standard レベルでのみ利用できます。 Security Center の価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。
>
>

**[App Service]** には、App Service 環境の一覧と、Security Center によって実行された評価に基づく正常性についての概要が表示されます。

![App Services][17]

この一覧には、次の 3 種類のアイコンが使用されます。

![App Services 環境][18] App Services 環境。

![Web アプリケーション][19] Web アプリケーション。

![関数アプリケーション][24] 関数アプリケーション。

1. Web アプリケーションを選択します。 3 つのタブがある概要ビューが開きます。

  - **推奨事項**: Security Center によって実行され、失敗した評価に基づいて表示されます。
  - **評価に合格しました**: Security Center によって実行され、合格した評価の一覧。
  - **利用できない評価**: エラーのため、または推奨事項が特定のアプリ サービスに関連していないために実行に失敗した評価の一覧

  **[推奨事項]** には、選択した Web アプリケーションの推奨事項と各推奨事項の重大度の一覧が表示されます。

  ![[概要] ビュー][20]

2. 推奨事項を選択すると、推奨事項の説明、正常でないリソース、正常なリソース、およびスキャンされていないリソースの一覧が表示されます。

  ![推奨事項の説明][21]

  **[評価に合格しました]** には、合格した評価の一覧が表示されます。  これらの評価の重大度は常に緑色です。

  ![評価に合格しました][22]

3. 一覧から合格した評価を選択すると、評価の説明、正常でないリソース、正常なリソース、およびスキャンされていないリソースの一覧が表示されます。 正常でないリソース用のタブがありますが、評価に合格しているため、その一覧は常に空です。

    ![正常なリソース][23]



## <a name="next-steps"></a>次の手順
その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。


* [仮想マシンに関する Azure Security Center の推奨事項を理解する](security-center-virtual-machine-recommendations.md)
* [Azure Security Center での ID とアクセスの監視](security-center-identity-access.md)
* [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)
* [Azure Security Center での Azure SQL サービスの保護](security-center-sql-service-recommendations.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
