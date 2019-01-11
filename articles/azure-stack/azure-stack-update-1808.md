---
title: Azure Stack 1808 更新プログラム | Microsoft Docs
description: 既知の問題、更新プログラムをダウンロードする場所など、Azure Stack 統合システムの 1808 更新プログラムの更新内容について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 86f4e99401278d13a17f40c4c021060e8bd15f8a
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754545"
---
# <a name="azure-stack-1808-update"></a>Azure Stack 1808 更新プログラム

*適用対象: Azure Stack 統合システム*

この記事では、1808 更新プログラム パッケージの内容について説明します。 更新プログラム パッケージには、このバージョンの Azure Stack に対する機能強化、修正、既知の問題が含まれています。 また、更新プログラムをダウンロードできるリンクも掲載されています。 既知の問題は、更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]  
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス

Azure Stack 1808 更新プログラムのビルド番号は **1.1808.0.97** です。  

### <a name="new-features"></a>新機能

この更新プログラムには、Azure Stack に対する次の機能強化が含まれています。

<!--  2682594   | IS  --> 
- **すべての Azure Stack 環境は、協定世界時 (UTC) のタイム ゾーン形式を使用するようになりました。**  すべてのログ データと関連情報は、UTC 形式で表示されます。 UTC を使用するようにインストールされていない以前のバージョンから更新する場合は、UTC を使用するように環境が更新されます。 

<!-- 2437250  | IS  ASDK --> 
- **Managed Disks がサポートされます。** Azure Stack 仮想マシンと仮想マシン スケール セットで Managed Disks を使用できるようになりました。 詳細については、「[Azure Managed Disks: 違いと考慮事項](/azure/azure-stack/user/azure-stack-managed-disk-considerations)」をご覧ください。

<!-- 2563799  | IS  ASDK --> 
- **Azure Monitor**。 Azure 上の Azure Monitor と同様、Azure Monitor 上の Azure Stack では、ほとんどのサービスに対して標準となるインフラストラクチャのメトリックおよびログを提供します。 詳細については、「[Azure Stack 上の Azure Monitor](/azure/azure-stack/user/azure-stack-metrics-azure-data)」を参照してください。

<!-- 2487932| IS --> 
- **拡張機能ホストのための準備**。 拡張機能ホストを使用することで、必要とされる TCP/IP ポートの数を減らして Azure Stack のセキュリティ保護を支援できます。 1808 の更新では、拡張機能ホストに対応するよう Azure Stack を準備できます。 詳細については、「[Azure Stack 用の拡張機能ホストの準備](/azure/azure-stack/azure-stack-extension-host-prepare)」を参照してください。

<!-- IS --> 
- **仮想マシン スケール セット用のギャラリー アイテムが組み込まれました**。 仮想マシン スケール セットのギャラリー項目がユーザーおよび管理者のポータルで利用可能になりました。ダウンロードする必要はありません。  1808 にアップグレードする場合は、アップグレードの完了時に利用可能になります。  

<!-- IS, ASDK --> 
- **仮想マシン スケール セットのスケーリング**。 ポータルを使用して、[仮想マシン スケール セットをスケールする](azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set)ことができます (VMSS)。    

<!-- 2489570 | IS ASDK--> 
- **カスタムの IPSec/IKE ポリシー構成のサポート**。[Azure Stack 内の VPN ゲートウェイ用です](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways)。

<!-- | IS ASDK--> 
- **Kubernetes Marketplace アイテム**。 Kubernetes クラスターのデプロイに [Kubernetes Marketplace アイテム](azure-stack-solution-template-kubernetes-cluster-add.md)を使用できるようになりました。 ユーザーは Kubernetes アイテムを選択し、いくつかのパラメーターに値を入力すれば、Kubernetes クラスターを Azure Stack にデプロイできます。 このテンプレートの目的は、ユーザーが少ないステップで開発/テスト用の Kubernetes デプロイをセットアップできるように簡素化することです。

<!-- | IS ASDK--> 
- **Blockchain テンプレート**。 Azure Stack で [Ethereum コンソーシアムのデプロイ](user/azure-stack-ethereum.md)を実行できるようになりました。 [Azure Stack クイック スタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)に 3 つの新しいテンプレートが追加されています。 これらを利用すると、ユーザーは、Azure と Ethereum の最小限の知識があれば、マルチメンバー コンソーシアム Ethereum ネットワークをデプロイして構成できます。 このテンプレートの目的は、ユーザーが少ないステップで開発/テスト用のブロックチェーン デプロイをセットアップできるように簡素化することです。

<!-- | IS ASDK--> 
- **API バージョン プロファイル 2017-03-09-profile が 2018-03-01-hybrid に更新されました**。 API プロファイルでは、Azure リソース プロバイダーと Azure REST エンドポイントの API バージョンが指定されます。 プロファイルの詳細については、「[Azure Stack での API バージョンのプロファイルの管理](/azure/azure-stack/user/azure-stack-version-profiles)」を参照してください。

### <a name="fixed-issues"></a>修正された問題

<!-- IS ASDK--> 
- ポータルで可用性セットを作成すると 1 の障害ドメインと更新ドメインがセットに含められるという問題が修正されました。 

<!-- IS ASDK --> 
- 仮想マシン スケール セットのスケーリング設定がポータルで使用できるようになりました。  

<!-- 2494144- IS, ASDK --> 
- デプロイの VM サイズを選択するときに F シリーズの一部の仮想マシン サイズが表示されないという問題が解決されました。 

<!-- IS, ASDK --> 
- 仮想マシンの作成時のパフォーマンスが向上し、基になるストレージの使用がさらに最適化されました。

- **さまざまな修正** - パフォーマンス、安定性、セキュリティ、Azure Stack で使用されるオペレーティング システムが修正されました。


### <a name="changes"></a>変更点
<!-- 1697698  | IS, ASDK --> 
- *クイック スタート チュートリアル*。ユーザー ポータルのダッシュボードにあるこのチュートリアルに、オンラインの Azure Stack ドキュメントにある関連する記事へのリンクが掲載されています。

<!-- 2515955   | IS ,ASDK--> 
- *[その他のサービス]* が *[すべてのサービス]* に置き換えられました (Azure Stack の管理者ポータルとユーザー ポータル)。 *[すべてのサービス]* は、Azure portal の場合と同じように Azure Stack ポータル内を移動するために使用できるようになりました。

<!-- TBD | IS, ASDK --> 
- *[+ 新規]* が *[+ リソースの作成]* に置き換えられました (Azure Stack の管理ポータルとユーザー ポータル)。  *[+ リソースの作成]* は、Azure portal の場合と同じように Azure Stack ポータル内を移動するために使用できるようになりました。  

<!--  TBD – IS, ASDK --> 
- *Basic A* の仮想マシン サイズは、ポータルを介して[仮想マシン スケール セット (VMSS) を作成する](azure-stack-compute-add-scalesets.md)ものとしては廃止されました。 このサイズの VMSS を作成するには、PowerShell またはテンプレートをご使用ください。  

### <a name="common-vulnerabilities-and-exposures"></a>共通脆弱性識別子

この更新では、次の更新プログラムがインストールされます。  

- [CVE-2018-0952](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0952)
- [CVE-2018-8200](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8200)
- [CVE-2018-8204](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8204)
- [CVE-2018-8253](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8253)
- [CVE-2018-8339](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8339)
- [CVE-2018-8340](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8340)
- [CVE-2018-8341](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8341)
- [CVE-2018-8343](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8343)
- [CVE-2018-8344](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8344)
- [CVE-2018-8345](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8345)
- [CVE-2018-8347](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8347)
- [CVE-2018-8348](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8348)
- [CVE-2018-8349](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8349)
- [CVE-2018-8394](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8394)
- [CVE-2018-8398](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8398)
- [CVE-2018-8401](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8401)
- [CVE-2018-8404](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8404)
- [CVE-2018-8405](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8405)
- [CVE-2018-8406](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8406)  

これらの脆弱性の詳細については、上記のリンクをクリックするか、Microsoft サポート技術情報の記事 [4343887](https://support.microsoft.com/help/4343887) をご覧ください。

この更新プログラムには、L1 Terminal Fault (L1TF) と呼ばれる投機的実行サイド チャネルの脆弱性に対する軽減策も含まれています ([Microsoft セキュリティ アドバイザリ ADV180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018) に説明があります)。  

### <a name="prerequisites"></a>前提条件

- Azure Stack 1808 更新プログラムを適用する前に Azure Stack [1807 更新プログラム](azure-stack-update-1807.md)をインストールします。 

- 最新の入手できる[バージョン 1807 の更新プログラムまたは修正プログラム](azure-stack-update-1807.md#post-update-steps)をインストールします。  
  > [!TIP]  
  > 以下の *RRS* または *Atom* フィードに登録して、Azure Stack の修正プログラムの最新情報を入手してください:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- この更新プログラムのインストールを開始する前に、次のパラメーターを指定して [Test-AzureStack](azure-stack-diagnostic-test.md) を実行して Azure Stack の状態を確認し、見つかったすべての操作上の問題 (すべての警告とエラーを含む) を解決します。 また、アクティブなアラートを確認し、アクションが必要なアラートを解決します。  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```   

### <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題

- 1808 更新の後で [Test-AzureStack](azure-stack-diagnostic-test.md) を実行すると、Baseboard Management Controller (BMC) からの警告メッセージが表示されます。 この警告は無視してかまいません。

<!-- 2468613 - IS --> 
- この更新プログラムのインストール中に、"*エラー – FaultType UserAccounts.New のテンプレートが見つかりません*" というタイトルのアラートが表示される場合があります。  これらのアラートは無視してかまいません。 この更新プログラムのインストール後、これらのアラートは自動的に閉じられます。

<!-- 2489559 - IS --> 
- この更新プログラムのインストール中に仮想マシンを作成しようとしないでください。 更新プログラムの管理方法については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md#plan-for-updates)」を参照してください。

<!-- 2830461 - IS --> 
- 更新で注意が必要な特定の状況では、対応するアラートが生成されないことがあります。 それでも正確な状態はポータルに反映され、影響を受けることはありません。

### <a name="post-update-steps"></a>更新後の手順

> [!Important]  
> Azure Stack デプロイを、拡張機能ホスト用に準備します。 次のガイダンスを使用して、システムを準備します: 「[Azure Stack の拡張機能ホストを準備する](azure-stack-extension-host-prepare.md)」。

この更新プログラムをインストールした後、適用可能な修正プログラムがあればインストールします。 詳細については、以下のサポート技術情報と[サービス ポリシー](azure-stack-servicing-policy.md)に関するページを参照してください。 
- [KB 4481066 – Azure Stack 修正プログラム Azure Stack 修正プログラム 1.1808.9.117](https://support.microsoft.com/help/4481066/)


## <a name="known-issues-post-installation"></a>既知の問題 (インストール後)

このビルド バージョンのインストール後について次の既知の問題があります。

### <a name="portal"></a>ポータル

- Azure Stack のテクニカル ドキュメントでは、Azure Stack の最新のリリースについて説明しています。 リリースごとにポータルが変更されるため、Azure Stack ポータルを使用した場合の動作と、ドキュメントに示されている内容が異なる場合があります。 

<!-- TBD - IS ASDK --> 
- ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを回復するには、**[ダッシュボードの編集]** をクリックし、右クリックして、**[既定の状態にリセット]** を選んでください。

<!-- 2930718 - IS ASDK --> 
- 管理ポータルで、ユーザーのサブスクリプションの詳細にアクセスした後、ブレードを閉じて、**[最近]** をクリックした場合、ユーザーのサブスクリプション名が表示されません。

<!-- 3060156 - IS ASDK --> 
- 管理ポータルとユーザー ポータルの両方で、ポータルの設定をクリックした後、**[すべての設定とプライベート ダッシュボードを削除]** を選んでも、想定どおりに機能しません。 エラー通知が表示されます。 

<!-- 2930799 - IS ASDK --> 
- 管理ポータルとユーザー ポータルの両方で、**[すべてのサービス]** の下に、資産 **[DDoS protection プラン]** が表示されますが、これは正しくありません。 実際には、これは Azure Stack でご利用になれません。 これを作成しようとすると、ポータルが Marketplace アイテムを作成できなかったというエラーが表示されます。 

<!-- 2930820 - IS ASDK --> 
- 管理ポータルとユーザー ポータルの両方で、"Docker" を検索すると、返される項目が正しくありません。 実際には、これは Azure Stack でご利用になれません。 これを作成しようとすると、エラーを示すブレードが表示されます。 

<!-- 2967387 – IS, ASDK --> 
- Azure Stack の管理ポータルまたはユーザー ポータルへのサインインに使用したアカウントが、**[識別されないユーザー]** と表示されます。 これは、アカウントに*名*と*姓*がどちらも指定されていない場合に発生します。 この問題を回避するには、ユーザー アカウントを編集して、名または姓のどちらかを指定してください。 その後、いったんサインアウトし、ポータルにもう一度サインインする必要があります。 

<!--  2873083 - IS ASDK --> 
-  ポータルを使用して仮想マシン スケール セット (VMSS) を作成するとき、Internet Explorer を使用すると *[インスタンス サイズ]* ドロップダウンが正しく読み込まれません。 この問題を回避するには、ポータルを使用して VMSS を作成するときに別のブラウザーをご使用ください。  

<!-- 2931230 – IS  ASDK --> 
- アドオン プランとしてユーザー サブスクリプションに追加されたプランは、ユーザー サブスクリプションからプランを削除しても削除できません。 アドオン プランを参照するサブスクリプションも削除されるまで、プランは残ります。 

<!--2760466 – IS  ASDK --> 
- このバージョンを実行する新しい Azure Stack 環境をインストールすると、「*アクティブ化が必要*」を示すアラートが表示されない場合があります。 マーケットプレース シンジケーションを使用するには、[アクティブ化](azure-stack-registration.md)する必要があります。  

<!-- TBD - IS ASDK --> 
- バージョン 1804 で導入された 2 種類の管理サブスクリプションは使用しないでください。 **Metering サブスクリプション**と **Consumption サブスクリプション**のサブスクリプションの種類です。 これらのサブスクリプションの種類は、バージョン 1804 以降の新しい Azure Stack 環境では表示されますが、まだ使用できる状態ではありません。 **既定のプロバイダー** サブスクリプションの種類を引き続き使用する必要があります。

<!-- TBD - IS ASDK --> 
- ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。

<!-- TBD - IS ASDK --> 
- Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 この問題を回避するには、PowerShell を使用してアクセス許可を確認します。


### <a name="health-and-monitoring"></a>正常性と監視

<!-- TBD - IS -->
- Azure Stack システム上で、次のアラートが繰り返し表示されてから消えることがあります。
   - *Infrastructure role instance unavailable\(インフラストラクチャ ロール インスタンスを利用できません\)*
   - *Scale unit node is offline\(スケール ユニットがオフラインです\)*
   
  [Test-AzureStack](azure-stack-diagnostic-test.md) コマンドレットを実行して、インフラストラクチャ ロール インスタンスとスケール ユニット ノードの正常性を確認してください。 [Test-AzureStack](azure-stack-diagnostic-test.md) によって問題が検出されない場合は、これらのアラートを無視することができます。 問題が検出された場合は、管理ポータルまたは PowerShell を使用して、インフラストラクチャ ロール インスタンスまたはノードの開始を試みることができます。

  この問題は最新の [1808 修正プログラム リリース](https://support.microsoft.com/help/4481066/)で修正されているので、問題が発生している場合は、この修正プログラムをインストールしてください。

<!-- 1264761 - IS ASDK --> 
- 以下の詳細情報の**正常性コントローラー** コンポーネントのアラートが表示されることがあります:  

   アラート #1:
   - 名前: インフラストラクチャ ロールの異常
   - 重大度: 警告
   - コンポーネント: 正常性コントローラー
   - 説明: 正常性コントローラーのハートビート スキャナーは使用できません。 これは、正常性レポートとメトリックに影響する可能性があります。  

  アラート #2:
   - 名前: インフラストラクチャ ロールの異常
   - 重大度: 警告
   - コンポーネント: 正常性コントローラー
   - 説明: 正常性コントローラーの障害スキャナーは使用できません。 これは、正常性レポートとメトリックに影響する可能性があります。

  どちらのアラートも無視しても問題ありません。時間が経過すると、自動的に閉じられます。  


<!-- 2812138 | IS --> 
- 以下の詳細情報を含む **Storage** コンポーネントのアラートが表示される場合があります。

   - 名前: Storage サービスの内部通信エラー  
   - 重大度: 重大  
   - コンポーネント: Storage  
   - 説明: 次のノードに要求を送信するときに Storage サービスの内部通信エラーが発生しました。  

    アラートは無視しても差し支えありませんが、手動で閉じる必要があります。

<!-- 2368581 - IS. ASDK --> 
- Azure Stack オペレーターで、メモリ不足のアラートを受信し、テナント仮想マシンが**ファブリック VM の作成エラー**でデプロイできなかった場合、Azure Stack スタンプに使用できるメモリが不足している可能性があります。 ワークロードに使用できる容量の詳細については、[Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) に関するページを参照してください。

### <a name="compute"></a>Compute

<!-- 3164607 – IS, ASDK -->
- デタッチしたディスクを同じ名前と LUN で同じ仮想マシン (VM) に再アタッチすると、**"データ ディスク 'datadisk' は、VM 'vm1' にアタッチできません"** というようなエラーで失敗します。 このエラーは、現在ディスクがデタッチされているか、直前のデタッチ操作が失敗したために発生します。 ディスクが完全にデタッチされるまで待ってから再試行するか、ディスクをもう一度明示的に削除/デタッチしてください。 回避策は、別の名前で再アタッチするか、別の LUN に再アタッチすることです。 

<!-- 3099544 – IS, ASDK --> 
- Azure Stack ポータルを使用して新しい VM を作成し、VM サイズを選択するときに、[米国ドル/月] 列に **"利用不可"** のメッセージが表示されます。 VM 価格の列の表示は、Azure Stack ではサポートされておらず、この列は表示されるべきではありません。

<!-- 3090289 – IS, ASDK --> 
- 更新プログラム 1808 の適用後、Managed Disks を使用した VM をデプロイするときに、次の問題が発生する可能性があります。

   1. 1808 更新の前に作成したサブスクリプションの場合、Managed Disks を使用した VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、サブスクリプションごとに次の手順に従ってください。
      1. テナント ポータルで、**[サブスクリプション]** に移動して、サブスクリプションを検索します。 **[リソース プロバイダー]** をクリックし、**[Microsoft.Compute]** をクリックした後、**[再登録]** をクリックします。
      2. 同じサブスクリプションで、**[アクセス制御 (IAM)]** に移動し、**[Azure Stack – マネージド ディスク]** がリストに含まれていることを確認します。
   2. マルチテナント環境を構成した場合、ゲスト ディレクトリに関連付けられているサブスクリプションで VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、次の手順に従ってください。
      1. [1808 Azure Stack 修正プログラム](https://support.microsoft.com/help/4481066/)を適用します。
      2. [この記事](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)にある手順に従って、各ゲスト ディレクトリを構成します。
      
<!-- 3179561 - IS --> 
- [Azure Stack の使用状況に関する FAQ](azure-stack-usage-related-faq.md#managed-disks) のページで説明しているように、Managed Disks の使用状況は、時間単位で報告されます。 ただし、9 月 27 日以前の Managed Disks の使用状況に対しては、不正確に課金される可能性があるため、Azure Stack の課金では、代わりに月額料を使用しています。 課金の問題が解決するまで、9 月 27 日以降の Managed Disks の課金を一時的に中断しています。 Managed Disks の使用状況に対して不正確な課金があった場合、Microsoft 課金サポートまでお問合せください。
Azure Stack の課金 API から生成された使用状況レポートでは正しい数量が表示され、利用可能になっています。

<!-- 3507629 - IS, ASDK --> 
- マネージド ディスクは 2 つの新しい[コンピューティング クォータの種類](azure-stack-quota-types.md#compute-quota-types)を作成して、プロビジョニングできるマネージド ディスクの最大容量を制限します。 既定では、2048 GiB がマネージド ディスク クォータの種類ごとに割り当てられます。 ただし、次の問題が発生する可能性があります。

   - 更新プログラム 1808 より前に作成されたクォータでは、マネージド ディスクのクォータは 2048 GiB が割り当てられているものの、管理者ポータルでは値 0 が表示されます。 値は実際のニーズに基づいて増減でき、新しく設定したクォータ値が 2048 GiB の既定値をオーバーライドします。
   - クォータ値を 0 に更新することは、2048 GiB の既定値と同じことになります。 この問題を回避するには、クォータ値を 1 に設定します。

<!-- 2869209 – IS, ASDK --> 
- [**Add-AzsPlatformImage** コマンドレット](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0)を使用する場合は、ディスクのアップロード先のストレージ アカウント URI として **-OsUri** パラメーターを使用する必要があります。 ディスクのローカル パスを使用した場合、次のエラーが出てコマンドレットが失敗します: *長時間実行処理が状態 '失敗' で失敗しました*。 

<!--  2966665 – IS, ASDK --> 
- SSD データ ディスクをプレミアム サイズのマネージド ディスク仮想マシン (DS、DSv2、Fs、Fs_V2) にアタッチすると、次のエラーで失敗します:*仮想マシン 'vmname' のディスクを更新できませんでした。エラー: ストレージ アカウントの種類 'Premium_LRS' は VM サイズ (Standard_DS/Ds_V2/FS/Fs_v2)でサポートされていないため、要求された操作は実行できません。*

   この問題を回避するには、*Premium_LRS ディスク*の代わりに *Standard_LRS* データ ディスクをご使用ください。 *Standard_LRS* データ ディスクを使用しても、IOPS または課金コストは変わりません。 

<!--  2795678 – IS, ASDK --> 
- ポータルを使用して Premium VM サイズ (DS、Ds_v2、FS、FSv2) の仮想マシン (VM) を作成すると、VM は Standard ストレージ アカウントで作成されます。 Standard ストレージ アカウントで作成しても、機能、IOPS、課金に影響はありません。 

   次の警告は無視してかまいません: *Premium ディスクをサポートしているサイズで Standard ディスクを使用することが選択されました。これはオペレーティング システムのパフォーマンスに影響を与える可能性があるため、お勧めできません。代わりに Premium ストレージ (SSD) の使用を検討してください。*

<!-- 2967447 - IS, ASDK --> 
- 仮想マシン スケール セット (VMSS) の作成エクスペリエンスでは、デプロイのオプションとして CentOS-based 7.2 が提供されます。 このイメージは Azure Stack では使用できないため、デプロイ用に別の OS を選択するか、またはデプロイする前にオペレーターが Marketplace からダウンロードしておいた別の CentOS イメージを指定する Azure Resource Manager テンプレートをご使用ください。  

<!-- 2724873 - IS --> 
- スケール ユニットの管理に PowerShell コマンドレットの **Start-AzsScaleUnitNode** または **Stop-AzsScaleunitNode** を使った場合、スケール ユニットを開始または停止しようとすると、1 回目は失敗することがあります。 初回実行時にコマンドレットが失敗した場合は、もう一度コマンドレットを実行してください。 2 回目は操作が正常に完了します。 

<!-- TBD - IS ASDK --> 
- Azure Stack ユーザー ポータルで仮想マシンを作成するとき、ポータルでは、DS シリーズ VM にアタッチできるデータ ディスクの数に誤った値が表示されます。 DS シリーズ VM は Azure の構成と同数のデータ ディスクをアタッチできます。

<!-- TBD - IS ASDK --> 
- VM の展開で拡張機能のプロビジョニングに時間がかかりすぎる場合、ユーザーは、プロセスを停止して VM の割り当て解除または削除を試みるのではなく、プロビジョニングをタイムアウトさせる必要があります。  

<!-- 1662991 IS ASDK --> 
- Linux の VM 診断は、Azure Stack でサポートされていません。 VM 診断を有効にして Linux VM を展開すると、展開が失敗します。 診断設定で Linux VM の基本メトリックを有効にした場合も、展開が失敗します。  

<!-- 2724961- IS ASDK --> 
- サブスクリプション設定で **Microsoft.Insight** リソース プロバイダーを登録し、ゲスト OS 診断を有効にした Windows VM を作成すると、VM の概要ページにある CPU 使用率グラフにメトリック データを表示できなくなります。

   VM の CPU 使用率グラフを表示するには、**メトリック** ブレードに移動して、サポートされているすべての Windows VM ゲスト メトリックを表示します。

- SSH の認可を有効にして作成した Ubuntu 18.04 VM では、SSH キーを使用してログインすることはできません。 回避策として、プロビジョニング後に Linux 拡張機能用の VM アクセスを使用して SSH キーを実装するか、パスワード ベースの認証を使用してください。

### <a name="networking"></a>ネットワーク  

<!-- 1766332 - IS ASDK --> 
- **[ネットワーク]** で、**[Create VPN Gateway]\(VPN ゲートウェイの作成\)** をクリックして VPN 接続を設定する場合、VPN の種類として **[ポリシー ベース]** が表示されます。 このオプションを選択しないでください。 Azure Stack では **[ルート ベース]** オプションのみがサポートされています。

<!-- 1902460 - IS ASDK --> 
- Azure Stack では、IP アドレスごとに 1 つの "*ローカル ネットワーク ゲートウェイ*" がサポートされます。 これは、テナントのすべてのサブスクリプションに当てはまります。 最初のローカル ネットワーク ゲートウェイ接続を作成した後に、続いて同じ IP アドレスでローカル ネットワーク ゲートウェイ リソースを作成しようとすると、ブロックされます。

<!-- 16309153 - IS ASDK --> 
- "*自動*" の DNS サーバー設定を使用して作成した仮想ネットワークで、カスタム DNS サーバーに変更すると失敗します。 更新した設定は、その Vnet 内の VM にプッシュされません。

<!-- 2702741 -  IS ASDK --> 
- 動的割り当てメソッドを使用してデプロイされているパブリック IP は、停止 - 割り当て解除が発行された後も保持される保証はありません。

<!-- 2529607 - IS ASDK --> 
- Azure Stack の "*シークレット ローテーション*" 中は、2 ～ 5 分間、Public IP Addresses に到達できない期間があります。

<!-- 2664148 - IS ASDK --> 
- テナントが S2S VPN トンネルを使用して仮想マシンにアクセスするシナリオでは、ゲートウェイが既に作成された後でオンプレミスのサブネットがローカル ネットワーク ゲートウェイに追加された場合、接続しようとしても失敗するシナリオが発生する可能性があります。 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。

<!-- 2489178 - IS ASDK --> 
- インフラストラクチャ (worker、管理、フロントエンド ロール) をスケールアウトするには、コンピューティングのリリース ノートの説明に従って PowerShell を使用する必要があります。



### <a name="usage"></a>使用法  
<!-- TBD - IS ASDK --> 
- パブリック IP アドレス使用量メーターのデータは、レコードが作成された日時を示す *TimeDate* スタンプではなく、各レコードに対して同じ *EventDateTime* 値を示します。 現在、このデータを使用して、パブリック IP アドレスの使用を正確に算出することはできません。


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>更新プログラムをダウンロードする

Azure Stack 1808 更新プログラム パッケージは、[ここから](https://aka.ms/azurestackupdatedownload)ダウンロードできます。 

Azure Stack デプロイでは、接続されたシナリオに限り、セキュリティで保護されたエンドポイントが定期的にチェックされ、お客様のクラウド用の更新プログラムが入手可能かどうかが自動的に通知されます。 詳細については、[Azure Stack の更新プログラムの管理](azure-stack-updates.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
- Azure Stack 統合システムのサービス ポリシーについて、およびサポートを受けられる状態にシステムを維持するために必要な作業について確認するには、「[Azure Stack サービス ポリシー](azure-stack-servicing-policy.md)」を参照してください。  
- 特権エンドポイント (PEP) を使用して更新プログラムを監視および再開するには、「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](azure-stack-monitor-update.md)」をご覧ください。  
- Azure Stack での更新プログラム管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」を参照してください。  
- Azure Stack に更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」を参照してください。  
