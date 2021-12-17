---
title: Azure Chaos Studio の一般的な問題のトラブルシューティング
description: Azure Chaos Studio の使用時の一般的な問題のトラブルシューティングについて説明します
author: c-ashton
ms.service: chaos-studio
ms.author: cashton
ms.topic: troubleshooting
ms.date: 11/10/2021
ms.custom: template-troubleshooting, ignite-fall-2021
ms.openlocfilehash: 9d56666f42d837f59ec22031646d46e22f0b298d
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372494"
---
# <a name="troubleshoot-issues-with-azure-chaos-studio"></a>Azure Chaos Studio に関する問題のトラブルシューティング

Chaos Studio の使用時に、何らかの問題が発生する場合があります。 この記事では、よく発生する問題とトラブルシューティングの手順について詳しく説明します。

## <a name="general-troubleshooting-tips"></a>一般的なトラブルシューティングのヒント

次のソースは、Chaos Studio に関する問題のトラブルシューティングに役立ちます。
1. **アクティビティ ログ**: [Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)には、サブスクリプション内のすべての作成、更新、および削除操作の記録があります。これには、ターゲットや機能の有効化、エージェントのインストール、実験の作成や実行などの Chaos Studio 操作が含まれます。 アクティビティ ログに示される障害は、Chaos Studio を使用するために必要なユーザー アクションを完了できなかった可能性があることを示します。 また、ほとんどのサービス直接フォールトでは、Azure Resource Manager 操作を実行することによってフォールトが挿入されるため、アクティビティ ログには、一部のサービス直接フォールトの実験中に挿入されるフォールトの記録も含まれます。
2. **実験の詳細**: 実験の実行の詳細には、個々の実験の実行の状態とエラーが示されます。 実験の詳細で特定のフォールトを開くと、障害が発生したリソースと障害に関するエラー メッセージが表示されます。 実験の詳細にアクセスする方法の詳細については、[こちら](chaos-studio-run-experiment.md#view-experiment-history-and-details)を参照してください。
3. **エージェント ログ**: エージェントベースのフォールトを使用している場合は、仮想マシンに RDP または SSH で接続して、エージェントでフォールトを実行できなかった理由を把握しておく必要があることがあります。 エージェント ログにアクセスするための手順は、オペレーティング システムによって異なります。
    * **Chaos Windows エージェント**: エージェント ログは、ソースの AzureChaosAgent を含むアプリケーション カテゴリの Windows イベント ログにあります。 エージェントにより、このログにフォールト アクティビティと通常の正常性チェック (認証を行い、Chaos Studio エージェント サービスと通信する機能) イベントが追加されます。
    * **Chaos Linux エージェント**: Linux エージェントでは systemd を使用して、エージェント プロセスを Linux サービスとして管理します。 エージェントの systemd ジャーナル (エージェント サービスによってログに記録されるイベント) を表示するには、コマンド `journalctl -u azure-chaos-agent` を実行します。
4. **VM 拡張機能の状態**: エージェントベースのフォールトを使用している場合は、VM 拡張機能がインストールされ、正常であることを確認する必要があることもあります。 Azure portal で仮想マシンに移動し、 **[拡張機能]** または **[拡張機能とアプリケーション]** に移動します。 ChaosAgent 拡張機能をクリックし、次のフィールドを見つけます。
    * **[状態]**  には、[プロビジョニング成功] と表示されるはずです。 その他の状態は、エージェントのインストールに失敗したことを示します。 すべての[システム要件](chaos-studio-limitations.md#limitations)が満たされていることを確認してから、エージェントを再インストールしてみてください。
    * **[ハンドラーの状態]** には、[準備完了] と表示されるはずです。 その他の状態は、エージェントはインストールされているものの、Chaos Studio サービスに接続できないことを示します。 すべての[ネットワーク要件](chaos-studio-limitations.md#limitations)が満たされていることと、ユーザー割り当てマネージド ID が仮想マシンに追加されていることを確認してから、再起動してみてください。

## <a name="issues-onboarding-a-resource"></a>リソースのオンボードに関する問題

### <a name="resources-do-not-show-up-in-the-targets-list-in-the-azure-portal"></a>リソースが Azure portal のターゲット リストに表示されない
Chaos Studio ターゲット リストで有効にするリソースが表示されない場合は、次のいずれかの問題が原因である可能性があります。
* リソースが、[Chaos Studio でサポートされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=chaos-studio)に存在しない。
* リソースが、[Chaos Studio でサポートされているリソースの種類](chaos-studio-fault-providers.md)ではない。
* リソースが、ターゲット リストのフィルターで除外されたサブスクリプションまたはリソース グループに含まれている。 サブスクリプションおよびリソース グループのフィルターを変更して、リソースを表示します。

### <a name="target-andor-capability-enablement-fails-or-doesnt-show-correctly-in-the-target-list"></a>ターゲットまたは機能の有効化に失敗したか、ターゲット リストに正しく表示されない
ターゲットまたは機能を有効にするときにエラーが発生した場合は、次の手順を試してください。
1. オンボードするリソースに対する適切なアクセス許可があることを確認します。 ターゲットまたは機能を有効にするには、リソースのスコープで Microsoft.Chaos/\* アクセス許可が必要です。 共同作成者などの組み込みロールには、すべての Microsoft.Chaos 操作に対するアクセス許可を含む、ワイルドカードの読み取りおよび書き込みアクセス許可があります。
2. ターゲットおよび機能リストが更新されるまで数分待ちます。 Azure portal では Azure Resource Graph を使用して、ターゲットと機能のオンボードに関する情報を収集します。更新が反映されるまで最大 5 分かかることがあります。
3. それでもリソースに [無効] と表示される場合は、以下の手順を試してください。
    1. リソースをもう一度有効にしてみます。
    2. それでもリソースの有効化に失敗する場合は、アクティビティ ログにアクセスして、失敗したターゲットの作成操作を見つけて、詳細なエラー情報を確認してください。
4. リソースには [有効] と表示されているものの、機能のオンボードに失敗した場合は、次の手順を試してください。
    1. ターゲット リストでリソースの **[アクションの管理]** ボタンをクリックします。 確認されなかった機能を確認し、 **[保存]** をクリックします。
    2. それでも機能の有効化に失敗する場合は、アクティビティ ログにアクセスして、失敗したターゲットの作成操作を見つけて、詳細なエラー情報を確認してください。

## <a name="prerequisite-issues"></a>前提条件の問題

一部の問題は、前提条件が満たされていないことが原因で発生します。 

### <a name="agent-based-faults-fail-on-a-virtual-machine"></a>仮想マシンでエージェントベースのフォールトが失敗する
前提条件が満たされていないことに関連するさまざまな理由により、エージェントベースのフォールトが失敗する可能性があります。
* Linux VM の場合、[CPU 負荷](chaos-studio-fault-library.md#cpu-pressure)、[物理メモリ負荷](chaos-studio-fault-library.md#physical-memory-pressure)、[ディスク I/O 負荷](chaos-studio-fault-library.md#disk-io-pressure-linux)、および[任意の stress-ng 負荷](chaos-studio-fault-library.md#arbitrary-stress-ng-stress)のフォールトではすべて、仮想マシンに [stress-ng ユーティリティ](https://wiki.ubuntu.com/Kernel/Reference/stress-ng)をインストールする必要があります。 stress-ng をインストールする方法の詳細については、フォールトの前提条件に関するセクションを参照してください。
* Linux または Windows VM では、エージェントベースのターゲットの有効化の際に指定されたユーザー割り当てマネージド ID も仮想マシンに追加する必要があります。
* Linux または Windows VM では、実験用のシステム割り当てマネージド ID に VM の閲覧者ロールが付与されている必要があります (見かけ上は、仮想マシンの共同作成者などの管理者特権ロールには、Chaos Studio エージェント サービスで仮想マシン上の Microsoft エージェント ターゲット プロキシ リソースを読み取るために必要な \*/読み取り操作が含まれていません)。

### <a name="aks-chaos-mesh-faults-fail"></a>AKS Chaos Mesh フォールトの失敗
前提条件が満たされていないことに関連するさまざまな理由により、AKS Chaos Mesh フォールトが失敗する可能性があります。
* AKS Chaos Mesh フォールトを使用する前に、まず AKS クラスターに Chaos Mesh をインストールする必要があります。 手順については、[AKS に対する Chaos Mesh の障害のチュートリアル](chaos-studio-tutorial-aks-portal.md#set-up-chaos-mesh-on-your-aks-cluster)に関するページを参照してください。
* Chaos Mesh はバージョン 2.0.4 以上である必要があります。 AKS クラスターに接続し、`helm version chaos-mesh` を実行することで、Chaos Mesh のバージョンを取得することができます。
* Chaos Mesh は名前空間 `chaos-testing` と共にインストールする必要があります。 Chaos Mesh のその他の名前空間名はサポートされていません。
* Azure Kubernetes Service クラスター管理者ロールは、カオス実験用のシステム割り当てマネージド ID に割り当てる必要があります。

## <a name="issues-creating-or-designing-an-experiment"></a>実験の作成または設計に関する問題

### <a name="when-adding-a-fault-my-resource-does-not-show-in-the-target-resources-list"></a>フォールトの追加時に、[ターゲット リソース] リストにリソースが表示されない
フォールトの追加時に、[ターゲット リソース] リストにフォールトのターゲットとなるリソースが表示されない場合は、次のいずれかの問題が原因である可能性があります。
* **[サブスクリプション]** フィルターが、ターゲットがデプロイされるサブスクリプションを除外するように設定されている。 サブスクリプション フィルターをクリックし、選択されたサブスクリプションを変更します。
* リソースがまだオンボードされていない。 **[ターゲット]** ビューにアクセスし、ターゲットを有効にします。 これが完了した後、[Add Fault]\(フォールトの追加\) ペインを閉じて再度開き、更新されたターゲット リストを表示する必要があります。
* リソースが、そのフォールトのターゲットの種類に対してまだ有効になっていない。 [フォールト ライブラリ](chaos-studio-fault-library.md)を参照してフォールトに使用されているターゲットの種類を確認してから、 **[ターゲット]** ビューにアクセスして、そのターゲットの種類を有効にします (Microsoft エージェント フォールトの場合はエージェントベース、他のすべてのターゲットの種類の場合はサービス直接)。 これが完了した後、[Add Fault]\(フォールトの追加\) ペインを閉じて再度開き、更新されたターゲット リストを表示する必要があります。
* リソースで、そのフォールトの機能がまだ有効になっていない。 [フォールト ライブラリ](chaos-studio-fault-library.md)を参照してフォールトの機能名を確認し、 **[ターゲット]** ビューにアクセスして、ターゲット リソースの **[アクションの管理]** をクリックします。 実行しようとしているフォールトに対応する機能のチェックボックスをオンにし、 **[保存]** をクリックします。 これが完了した後、[Add Fault]\(フォールトの追加\) ペインを閉じて再度開き、更新されたターゲット リストを表示する必要があります。
* リソースが最近オンボードされたばかりで、Azure Resource Graph にまだ表示されていない。 [ターゲット リソース] リストは Azure Resource Graph から照会されます。新しいターゲットを有効にした後、更新が Azure Resource Graph に反映されるまで最大 5 分かかることがあります。 数分待ってから、[Add Fault]\(フォールトの追加\) ペインを再度開きます。

### <a name="when-creating-an-experiment-i-get-the-error-the-microsoftagent-provider-requires-a-managed-identity"></a>実験の作成時に、`The microsoft:agent provider requires a managed identity` というエラーが表示される

このエラーは、お使いの仮想マシンにエージェントがデプロイされていない場合に発生します。 インストール手順については、[エージェントベースの障害を使用する実験の作成と実行](chaos-studio-tutorial-agent-based-portal.md)に関するページを参照してください。

### <a name="when-creating-an-experiment-i-get-the-error-the-content-media-type-null-is-not-supported-only-applicationjson-is-supported"></a>実験の作成時に、`The content media type '<null>' is not supported. Only 'application/json' is supported.` というエラーが表示される

ARM テンプレートまたは Chaos Studio REST API を使用して実験を作成している場合は、このエラーが発生することがあります。 このエラーは、形式に誤りがある JSON が実験定義にあることを示しています。 Visual Studio Code のような JSON リンターを使用して、中かっこや角かっこ ({} や \[\]) の不一致など、構文エラーがあるかどうかを確認してください。

## <a name="issues-running-an-experiment"></a>実験の実行に関する問題

### <a name="the-execution-status-of-my-experiment-after-starting-is-failed"></a>実験の開始後の実行状態が [失敗] となる

Azure portal の **[実験]** リストで、実験名をクリックして、 **[実験の概要]** を確認します。 **[履歴]** セクションで、失敗した実験の実行の横にある **[詳細]** をクリックして詳細なエラー情報を確認します。

![実験履歴](images/run-experiment-history.png)

### <a name="my-agent-based-fault-failed-with-error-verify-that-the-target-is-correctly-onboarded-and-proper-read-permissions-are-provided-to-the-experiment-msi"></a>エージェントベースのフォールトはエラーで失敗しました: ターゲットが正しくオンボードされており、適切な読み取りアクセス許可が実験用 msi に付与されていることを確認してください。

これは、Azure portal を使用してエージェントをオンボードした場合に発生する可能性があります。この操作には、エージェントベースのターゲットを有効にしても、ユーザー割り当てマネージド ID が仮想マシンまたは仮想マシン スケール セットに割り当てられないという既知の問題があります。

これを解決するには、Azure portal で、仮想マシンまたは仮想マシン スケール セットにアクセスし、 **[ID]** を選択して **[ユーザー割り当て]** タブを開き、ユーザー割り当て ID を仮想マシンに **[追加]** します。 完了後、エージェントが接続するために仮想マシンの再起動が必要な場合があります。
