---
title: Azure Automation で VM の更新プログラムとパッチを管理する
description: この記事では、Update Management を使用して Azure および Azure 以外の VM の更新プログラムとパッチを管理する方法を説明します。
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 08/25/2021
ms.openlocfilehash: 932f5d93c5fa67de486ddb9cabaafd68384f0db8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357471"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>VM の更新プログラムとパッチを管理する

Azure Automation Update Management のソフトウェア更新プログラムには、ソフトウェア更新プログラムを追跡し、Azure およびハイブリッド クラウドのマシンに適用する複雑なタスクの管理に役立つツールとリソースのセットが用意されています。 運用効率を維持し、セキュリティ上の問題を解決し、増加するサイバー セキュリティの脅威によるリスクを軽減するためには、効果的なソフトウェア更新プログラムの管理プロセスが必要です。 ただし、テクノロジは常に変化し、新しいセキュリティ上の脅威が絶え間なく出現するために、効果的なソフトウェア更新プログラムの管理には、一貫した継続的な注意が必要です。

> [!NOTE]
> Update Management では、ファーストパーティの更新プログラムの展開と、それらの事前ダウンロードがサポートされています。 このサポートには、更新プログラムの適用対象のシステムに対する変更が必要になります。 お使いのシステムでこれらの設定を構成する方法については、「[Azure Automation Update Management 用に Windows Update の設定を構成する](configure-wuagent.md)」を参照してください。

VM の更新プログラムを管理する前に、次のいずれかの方法を使用して、それらに対して Update Management を有効にしていることを確認してください。

* [Automation アカウントから Update Management を有効にする](enable-from-automation-account.md)
* [Azure portal を参照して Update Management を有効にする](enable-from-portal.md)
* [Runbook から Update Management を有効にする](enable-from-runbook.md)
* [Azure VM から Update Management を有効にする](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>展開のスコープを制限する

Update Management では、ワークスペース内のスコープ構成を使用して、更新プログラムを受信するコンピューターを対象として指定します。 詳細については、[Update Management の展開スコープの制限](scope-configuration.md)に関するページを参照してください。

## <a name="compliance-assessment"></a>コンプライアンス評価

ソフトウェア更新プログラムをマシンに展開する前に、有効化されたマシンに対する更新プログラムのコンプライアンス評価の結果を確認します。 各ソフトウェア更新プログラムのコンプライアンスの状態は記録され、評価が完了した後に収集されて一括で Azure Monitor ログに転送されます。

Windows マシンでは、コンプライアンス スキャンは既定で 12 時間ごとに実行され、Windows 用の Log Analytics エージェントが再起動されてから 15 分以内に開始されます。 その後、評価データがワークスペースに転送され、**Updates** テーブルが更新されます。 更新プログラムのインストールの前後に、更新プログラムのコンプライアンス スキャンが実行されて、不足している更新プログラムが識別されますが、その結果はテーブル内の評価データを更新するために使用されません。

Update Management を使用して [Windows Update クライアントを構成する](configure-wuagent.md)方法に関する推奨事項を確認して、それが適切に管理されるのを妨げる問題を回避することが重要です。

Linux マシンでは、コンプライアンス スキャンは既定で 1 時間ごとに実行されます。 Linux 用 Log Analytics エージェントを再起動した場合、コンプライアンス スキャンは 15 分以内に開始されます。

コンプライアンスの結果は、評価した各マシンごとに Update Management に表示されます。 管理が有効になった新しいマシンからの更新されたデータがダッシュボードに表示されるまでには、最大で 30 分かかることがあります。

コンプライアンスの結果を表示する方法については、[ソフトウェア更新プログラムの監視](view-update-assessments.md)に関する記事を参照してください。

## <a name="deploy-updates"></a>更新プログラムの展開

コンプライアンスの結果を確認した後のソフトウェア更新プログラムの展開フェーズは、ソフトウェア更新プログラムを展開するプロセスです。 更新プログラムをインストールするには、リリース スケジュールとサービス期間と合致する展開をスケジュールします。 デプロイに含める更新の種類を選択できます。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

更新プログラムの展開をスケジュールする方法については、[ソフトウェア更新プログラムの展開](deploy-updates.md)に関する記事を参照してください。

## <a name="exclude-updates"></a>更新の除外

Red Hat Enterprise Linux など、Linux バリエーションの中には、パッケージを使用して OS レベルのアップグレードが発生することがあります。 これにより、OS バージョン番号の変更を伴う Update Management が実行される可能性があります。 Update Management でパッケージを更新するときに使用される方法は、管理者がローカルで Linux マシンに使用するものと同じであるため、この動作は意図的なものです。

Update Management のデプロイによる OS バージョンの更新を回避するには、**除外** 機能を使用します。

Red Hat Enterprise Linux で、除外するパッケージ名は `redhat-release-server.x86_64` です。

## <a name="linux-update-classifications"></a>Linux 更新プログラムの分類

更新プログラムを Linux マシンにデプロイする場合、更新プログラムの分類を選択できます。 このオプションにより、更新プログラムがフィルター処理され、指定した条件を満たすものに絞られます。 このフィルターは、更新プログラムが展開されるときに、マシンに対してローカルに適用されます。

Update Management では更新プログラムの強化がクラウドで実行されるため、Update Management で一部の更新プログラムに対して、セキュリティへの影響ありというフラグを設定できますが、ローカル マシンにはその情報がありません。 重大な更新プログラムを Linux マシンに適用する場合、そのマシンにセキュリティへの影響ありとマークされていない更新プログラムが存在する可能性があり、そのため、これらの更新プログラムは適用されません。 ただし、Update Management には関連する更新プログラムに関する追加情報があるため、そのマシンは引き続き非対応として報告されることがあります。

更新プログラムの分類ごとの更新プログラムのデプロイは、RTM バージョンの CentOS では動作しません。 CentOS に更新プログラムを正しくデプロイするには、更新プログラムが確実に適用されるように、すべての分類を選択します。 SUSE では、分類として **[他の更新プログラム]** のみを選択すると、その他のセキュリティ更新プログラムが zypper (パッケージ マネージャー) に関連している場合、またはその依存関係がまず必要になる場合に、それらの更新プログラムがインストールされる可能性があります。 この動作は、zypper の制限です。 場合によっては、更新プログラムのデプロイを再実行してから、更新プログラムのログでデプロイを確認する必要があります。

## <a name="review-update-deployments"></a>更新プログラムの展開を確認する

展開が完了したら、プロセスを確認して、マシンまたはターゲット グループごとに更新プログラムの展開が成功したかどうかを確認します。 展開ステータスを監視する方法については、[展開ステータスの確認](deploy-updates.md#check-deployment-status)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* 更新プログラムの展開の結果について通知するアラートを作成する方法については、[Update Management のアラートを作成](configure-alerts.md)に関する記事を参照してください。

* [Azure Monitor ログに対してクエリを実行](query-logs.md)すると、更新プログラムの評価、展開、およびその他の関連する管理タスクを分析することができます。 これには、作業を開始するために役立つ事前定義されたクエリが含まれています。