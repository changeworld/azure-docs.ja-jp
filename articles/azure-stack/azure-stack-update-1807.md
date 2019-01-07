---
title: Azure Stack 1807 更新プログラム | Microsoft Docs
description: 既知の問題、更新プログラムをダウンロードする場所など、Azure Stack 統合システムの 1807 更新プログラムの更新内容について説明します。
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
ms.date: 10/07/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 7f87f93ebc739d75c796859c7091d4cf62a820a0
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714986"
---
# <a name="azure-stack-1807-update"></a>Azure Stack 1807 更新プログラム

*適用先:Azure Stack 統合システム*

この記事では、1807 更新プログラム パッケージの内容について説明します。 このバージョンの Azure Stack の既知の問題、機能強化、修正された内容のほか、更新プログラムをダウンロードする場所について取り上げます。 既知の問題は、更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]  
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス

Azure Stack 1807 更新プログラムのビルド番号は **1.1807.0.76** です。  

### <a name="new-features"></a>新機能

この更新プログラムには、Azure Stack に対する次の機能強化が含まれています。

<!-- 1658937 | ASDK, IS --> 
- **定義済みのスケジュールでバックアップを開始** - アプライアンスとしての Azure Stack に、インフラストラクチャ バックアップを自動で定期的にトリガーする機能が追加され、人の介入が不要になりました。 また、定義されている保有期間を超えたバックアップについては、外部共有が自動的にクリーンアップされます。 詳細については、「[PowerShell で Azure Stack のバックアップを有効にする](azure-stack-backup-enable-backup-powershell.md)」をご覧ください。

<!-- 2496385 | ASDK, IS -->  
- **合計バックアップ時間にデータ転送時間が追加されました。** 詳細については、「[PowerShell で Azure Stack のバックアップを有効にする](azure-stack-backup-enable-backup-powershell.md)」をご覧ください。

<!-- 1702130 | ASDK, IS -->  
- **外部バックアップ容量に、外部共有の正確な容量が表示されるようになりました。** (従来は 10 GB にハードコーディングされていました。)詳細については、「[PowerShell で Azure Stack のバックアップを有効にする](azure-stack-backup-enable-backup-powershell.md)」をご覧ください。

<!-- 2508488 |  IS   -->
- **容量を拡張する**には、[新たにスケール ユニット ノードを追加](azure-stack-add-scale-node.md)します。

<!-- 2753130 |  IS, ASDK   -->  
- **Azure Resource Manager テンプレートで条件要素がサポートされるようになりました** - 条件を使用して Azure Resource Manger テンプレートでリソースをデプロイできるようになりました。 パラメーター値の有無の評価などの条件に基づいてリソースをデプロイするようにテンプレートを設計することができます。 テンプレートを条件として使用する方法については、Azure ドキュメントの「[リソースを条件付きでデプロイする](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy)」および「[Azure Resource Manager テンプレートの変数セクション](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables)」を参照してください。 

   テンプレートを使用して、[複数のサブスクリプションまたはリソース グループにリソースをデプロイする](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment)こともできます。  

<!--2753073 | IS, ASDK -->  
- **Microsoft.Network API リソース バージョンのサポートが更新され**、Azure Stack ネットワーク リソースで API バージョン 2015-06-15 から 2017-10-01 がサポートされるようになりました。  リソース バージョン 2017-10-01 から 2015-06-15 までのサポートは、このリリースには含まれていません。  機能の相違点については、「[Azure Stack ネットワークに関する考慮事項](user/azure-stack-network-differences.md)」を参照してください。

<!-- 2272116 | IS, ASDK   -->  
- **Azure Stack に、外部向け Azure Stack インフラストラクチャ エンドポイント (portal、adminportal、management、adminmanagement) に対する DNS 逆引き参照のサポートが追加されました**。 これにより、Azure Stack 外部エンドポイント名を IP アドレスから解決できます。

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack で、既存の VM にネットワーク インターフェイスをさらに追加できるようになりました。**  この機能は、ポータル、PowerShell、CLI を使用して利用できます。 詳細については、Azure ドキュメントの「[仮想マシンのネットワーク インターフェイスの追加と削除](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm)」を参照してください。 

<!-- 2222444 | IS, ASDK   -->  
- **ネットワーク使用量メーターの正確性と回復性が向上しました**。  ネットワーク使用量メーターがより正確になり、中断されたサブスクリプション、停止期間、競合状態が考慮されるようになりました。

<!-- 2753080 | IS -->  
- **更新プログラムが利用可能な場合の通知。** 接続された Azure Stack のデプロイでは、セキュリティで保護されたエンドポイントを定期的にチェックし、クラウド用の更新プログラムが入手可能かどうかを判断するようになりました。 この通知は、新しい更新を手動で確認してインポートした場合と同様に、更新タイルに表示されます。 [Azure Stack](azure-stack-updates.md) の更新管理に関する詳細をご覧ください。

<!-- 2297790 | IS, ASDK -->  
- **Azure Stack の Syslog クライアントの機能強化 (プレビュー機能)**。 このクライアントを使用すると、Azure Stack インフラストラクチャに関連する監査とログを、Azure Stack 外部の Syslog サーバーまたはセキュリティ情報イベント管理 (SIEM) ソフトウェアに転送できます。 Syslog クライアントで、プレーン テキストまたは TLS 1.2 暗号化 (後者が既定の構成) による TCP プロトコルがサポートされるようになりました。 サーバーのみの認証または相互認証のいずれかを使用して、TLS 接続を構成できます。

  Syslog クライアントが Syslog サーバーと通信する方法 (プロトコル、暗号化、認証など) を構成するには、*Set-SyslogServer* コマンドレットを使用します。 このコマンドレットは、特権エンドポイント (PEP) から入手できます。

  Syslog クライアント TLS 1.2 の相互認証用のクライアント側証明書を追加するには、PEP で Set-SyslogClient コマンドレットを使用します。

  このプレビューでは、監査と警告の数が大幅に増加します。 

  この機能はまだプレビュー段階であるため、運用環境では使用しないでください。

  詳細については、「[Azure Stack の Syslog 転送](azure-stack-integrate-security.md)」を参照してください。

<!-- ####### | IS, ASDK | --> 
- **Azure Resource Manager にリージョン名が含まれています。** このリリースでは、Azure Resource Manager から取得したオブジェクトに、リージョンの名前属性が追加されるようになります。 既存の PowerShell スクリプトが別のコマンドレットにオブジェクトを直接渡すと、スクリプトによってエラーが発生し、失敗することがあります。 これは、Azure Resource Manager に準拠した動作であり、呼び出し元のクライアントがリージョン属性を削除する必要があります。 Azure Resource Manager の詳細については、「[Azure Resource Manager のドキュメント](https://docs.microsoft.com/azure/azure-resource-manager/)」を参照してください。 verify 8-10 mdb -->

<!-- TBD | IS, ASDK -->  
- **委任されたプロバイダーの機能の変更。** 1807 から Azure リセラー モデルとの整合性を高めるため、委任されたプロバイダー モデルは単純化されました。委任されたプロバイダーは他の委任されたプロバイダーを作成することができなくなって、本質的にモデルが平坦化され、委任されたプロバイダーの機能を単一レベルで利用できるようになりました。 新しいモデルへの切り替えとサブスクリプションの管理を可能にするために、同じディレクトリ テナントに属する新規または既存の委任されたプロバイダー サブスクリプション間でユーザーサブスクリプションを移動できるようになりました。 また、既定プロバイダー サブスクリプションに属しているユーザーサブスクリプションも、同じディレクトリ テナント内にある委任されたプロバイダー サブスクリプションに移動できます。  詳細については、「[Azure Stack でのプランの委任](azure-stack-delegated-provider.md)」を参照してください。

<!-- 2536808 IS ASDK --> 
- Azure Marketplace からダウンロードしたイメージを使用して作成された **VM の VM 作成時間が改善**されました。

<!-- TBD | IS, ASDK -->  
- **Azure Stack Capacity Planner のユーザビリティの強化**。 Azure Stack [Capacity Planner](https://aka.ms/azstackcapacityplanner) では、ソリューション SKU を定義するときに S2D キャッシュと S2D 容量を入力できるよう、シンプルな機能が用意されました。 1000 VM の制限が削除されました。


### <a name="fixed-issues"></a>修正された問題

<!-- TBD | ASDK, IS --> 
- 更新プロセスをより信頼できるものにするために、さまざまな改善が行われました。 さらに、基になるインフラストラクチャに修正が加えられました。これにより、更新中に発生する可能性のあるワークロードのダウンタイムが最小限に抑えられます。

<!--2292271 | ASDK, IS --> 
- 変更したクォータ制限が既存のサブスクリプションに適用されない問題を修正しました。 今後は、ユーザーのサブスクリプションに関連付けられているオファーとプランについて、そこに含まれるネットワーク リソースのクォータ制限を引き上げると、新しいサブスクリプションだけでなく既存のサブスクリプションにも新しい制限が適用されます。

<!-- 448955 | IS ASDK --> 
- UTC+N タイム ゾーンでデプロイされているシステムをアクティビティ ログから検索するクエリが問題なく実行できるようになりました。    

<!-- 2319627 |  ASDK, IS --> 
- バックアップ構成パラメーター (パス/ユーザー名/パスワード/暗号化キー) の事前確認で、バックアップ構成に間違った設定が適用される問題を修正しました。 (以前は、バックアップに間違った設定が適用され、トリガーされた時点でバックアップが失敗していました。)

<!-- 2215948 |  ASDK, IS -->
- 外部共有から手動でバックアップを削除したときにバックアップ リストが最新の情報に更新されるようになりました。

<!-- 2332636 | IS -->  
- このバージョンに更新すると、AD FS を使ってデプロイするときに、既定のプロバイダー サブスクリプションの既定の所有者が、ビルトインの **CloudAdmin** ユーザーにリセットされていましたが、この問題は修正されました。

<!-- 2360715 |  ASDK, IS -->  
- データセンターの統合を設定する際、今後は、Azure のファイル共有場所の AD FS メタデータ ファイルにはアクセスしません。 詳細については、「[フェデレーション メタデータ ファイルを指定して AD FS の統合を設定する](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file)」を参照してください。 

<!-- 2388980 | ASDK, IS --> 
- ネットワーク インターフェイスまたはロード バランサーに割り当てられている既存のパブリック IP アドレスを新しいネットワーク インターフェイスまたは Azure Load Balancer に割り当てることができない問題を修正しました。  

<!-- 2551834 - IS, ASDK --> 
- 管理ポータルまたはユーザー ポータルでストレージ アカウントの *[概要]* を選択すると、*[基本]* ウィンドウに必要なすべての情報が正しく表示されるようになりました。 

<!-- 2551834 - IS, ASDK --> 
- 管理ポータルまたはユーザー ポータルでストレージ アカウントに *[タグ]* を選択すると、情報が正しく表示されるようになりました。

<!-- TBD - IS ASDK --> 
- このバージョンの Azure Stack では、OEM 拡張機能パッケージからドライバーの更新プログラムを適用できないという問題が修正されています。

<!-- 2055809- IS ASDK --> 
- VM の作成が失敗したときに、コンピューティング ブレードから VM を削除できないという問題が修正されました。  

<!--  2643962 IS ASDK -->  
- *メモリ容量不足*に対する誤ったアラートが表示されなくなりました。

- **さまざまな修正** - パフォーマンス、安定性、セキュリティ、Azure Stack で使用されるオペレーティング システムが修正されました。


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>共通脆弱性識別子
Azure Stack では、Windows Server 2016 の Server Core インストールを使用して、主要なインフラストラクチャをホストします。 このリリースでは、Azure Stack のインフラストラクチャ サーバーに次の Windows Server 2016 更新プログラムがインストールされます: 
- [CVE-2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE-2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE-2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE-2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE-2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE-2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE-2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

これらの脆弱性の詳細については、上記のリンクをクリックするか、Microsoft サポート技術情報の記事 [4338814](https://support.microsoft.com/help/4338814) と [4345418](https://support.microsoft.com/help/4345418) を参照してください。



## <a name="before-you-begin"></a>開始する前に

### <a name="prerequisites"></a>前提条件

- Azure Stack 1807 更新プログラムを適用する前に Azure Stack [1805 更新プログラム](azure-stack-update-1805.md)をインストールします。 更新プログラム 1806 は存在しません。  
 
  > [!TIP]  
  > 以下の *RRS* または *Atom* フィードに登録して、Azure Stack の修正プログラムの最新情報を入手してください:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- この更新プログラムのインストールを開始する前に、次のパラメーターを指定して [Test-AzureStack](azure-stack-diagnostic-test.md) を実行して Azure Stack の状態を確認し、見つかったすべての操作上の問題 (すべての警告とエラーを含む) を解決します。 また、アクティブなアラートを確認し、アクションが必要なアラートを解決します。  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題

<!-- 2468613 - IS --> 
- この更新プログラムのインストール中に、"*エラー – FaultType UserAccounts.New のテンプレートが見つかりません*" というタイトルのアラートが表示される場合があります。  これらのアラートは無視してかまいません。 この更新プログラムのインストール後、これらのアラートは自動的に閉じられます。

<!-- 2489559 - IS --> 
- この更新プログラムのインストール中に仮想マシンを作成しようとしないでください。 更新プログラムの管理方法については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md#plan-for-updates)」を参照してください。

<!-- 2830461 - IS --> 
- 更新で注意が必要な特定の状況では、対応するアラートが生成されないことがあります。 それでも正確な状態はポータルに反映され、影響を受けることはありません。

### <a name="post-update-steps"></a>更新後の手順
この更新プログラムをインストールした後、適用可能な修正プログラムがあればインストールします。 詳細については、以下のサポート技術情報と[サービス ポリシー](azure-stack-servicing-policy.md)に関するページを参照してください。 
- [KB 4467061 – Azure Stack 修正プログラム Azure Stack 修正プログラム 1.1807.3.82](https://support.microsoft.com/help/4467061/)

<!-- 2933866 – IS -->この更新プログラムをインストールすると、**失敗した更新プログラムのインストールに関して表示される状態が改善されます。** これには、2 つの新しい状態カテゴリを反映するように変更された、過去の更新プログラムのインストールの失敗に関する情報が含まれる可能性があります。 これらの 2 つの新しい状態カテゴリとは、*PreparationFailed* および *InstallationFailed* です。  

## <a name="known-issues-post-installation"></a>既知の問題 (インストール後)

このビルド バージョンのインストール後について次の既知の問題があります。

### <a name="portal"></a>ポータル

- Azure Stack のテクニカル ドキュメントは、最新のリリースについて説明しています。 リリースごとにポータルが変更されるため、Azure Stack ポータルを使用した場合の動作と、ドキュメントに示されている内容が異なる場合があります。 

- 管理ポータルの[ドロップダウン リストから新しいサポート要求を開く](azure-stack-manage-portals.md#quick-access-to-help-and-support)機能は使用できません。 代わりに、Azure Stack 統合システムでは、リンク [https://aka.ms/newsupportrequest](https://aka.ms/newsupportrequest) をご使用ください。

<!-- 2931230 – IS  ASDK --> 
- アドオン プランとしてユーザー サブスクリプションに追加されたプランは、ユーザー サブスクリプションからプランを削除しても削除できません。 アドオン プランを参照するサブスクリプションも削除されるまで、プランは残ります。 

<!--2760466 – IS  ASDK --> 
- このバージョンを実行する新しい Azure Stack 環境をインストールすると、「*アクティブ化が必要*」を示すアラートが表示されない場合があります。 マーケットプレース シンジケーションを使用するには、[アクティブ化](azure-stack-registration.md)する必要があります。  

<!-- TBD - IS ASDK --> 
- バージョン 1804 で導入された 2 種類の管理サブスクリプションは使用しないでください。 **Metering サブスクリプション**と **Consumption サブスクリプション**のサブスクリプションの種類です。 これらのサブスクリプションの種類は、バージョン 1804 以降の新しい Azure Stack 環境では表示されますが、まだ使用できる状態ではありません。 **既定のプロバイダー** サブスクリプションの種類を引き続き使用する必要があります。

<!-- 2403291 - IS ASDK --> 
- 管理ポータルとユーザー ポータルの下部に水平スクロールバーが表示されない可能性があります。 水平スクロールバーにアクセスできない場合は、ポータルの左上にある階層リンク リストから表示するブレードの名前を選択して、階層リンクを使用してポータル内の前のブレードに移動します。

  ![階層リンク](media/azure-stack-update-1804/breadcrumb.png)

<!-- TBD - IS --> 
- 管理ポータルでコンピューティング リソースやストレージ リソースを表示できない場合があります。 この問題の原因は、更新プログラムのインストール中にエラーが発生し、更新が正常に行われたことが誤って報告されたためです。 この問題が発生した場合は、Microsoft カスタマー サポート サービスにお問い合わせください。

<!-- TBD - IS --> 
- ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを復元するには、ポータルの右上にある歯車アイコンを選択し、**[既定の設定に戻す]** を選択します。

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

<!-- 1264761 - IS ASDK -->  
- 以下の詳細情報の**正常性コントローラー** コンポーネントのアラートが表示されることがあります:  

   アラート #1:
   - [名前]:インフラストラクチャ ロールの異常
   - [重大度]:警告
   - コンポーネント: 正常性コントローラー
   - 説明: 正常性コントローラーのハートビート スキャナーは使用できません。 これは、正常性レポートとメトリックに影響する可能性があります。  

  アラート #2:
   - [名前]:インフラストラクチャ ロールの異常
   - [重大度]:警告
   - コンポーネント: 正常性コントローラー
   - 説明: 正常性コントローラーの障害スキャナーは使用できません。 これは、正常性レポートとメトリックに影響する可能性があります。

  どちらのアラートも無視しても問題ありません。時間が経過すると、自動的に閉じられます。  


<!-- 2812138 | IS --> 
- 以下の詳細情報を含む **Storage** コンポーネントのアラートが表示される場合があります:

   - [名前]:Storage サービスの内部通信エラー  
   - [重大度]:重大  
   - コンポーネント: Storage  
   - 説明: 次のノードに要求を送信するときに Storage サービスの内部通信エラーが発生しました。  

    アラートは無視しても差し支えありませんが、手動で閉じる必要があります。

<!-- 2368581 - IS. ASDK --> 
- Azure Stack オペレーターで、メモリ不足のアラートを受信し、テナント仮想マシンが**ファブリック VM の作成エラー**でデプロイできなかった場合、Azure Stack スタンプに使用できるメモリが不足している可能性があります。 ワークロードに使用できる容量の詳細については、[Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) に関するページを参照してください。


### <a name="compute"></a>Compute

<!-- 2724873 - IS --> 
- スケール ユニットの管理に PowerShell コマンドレットの **Start-AzsScaleUnitNode** または **Stop-AzsScaleunitNode** を使った場合、スケール ユニットを開始または停止しようとすると、1 回目は失敗することがあります。 初回実行時にコマンドレットが失敗した場合は、もう一度コマンドレットを実行してください。 2 回目は操作が正常に完了します。 

<!-- 2494144 - IS, ASDK --> 
- 仮想マシンの展開用に仮想マシンのサイズを選択すると、VM を作成するときに F シリーズの VM のサイズがサイズ セレクターの一部として表示されません。 次の VM サイズは、セレクターには表示されません。*F8s_v2*、*F16s_v2*、*F32s_v2*、および*F64s_v2*。  
  この問題を回避するには、次のいずれかの方法を使用して VM をデプロイします。 どの方法でも、使用する VM のサイズを指定する必要があります。

  - **Azure Resource Manager テンプレート:** テンプレートを使用する際に、テンプレートの *vmSize* を使用する VM サイズと同じに設定します。 たとえば、*F32s_v2* サイズを使用する VM をデプロイするには、次のように入力します。  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) コマンドを使用して、`--size "Standard_F32s_v2"` と同様に VM サイズをパラメーターとして指定できます。

  - **PowerShell:** Powershell では、`-VMSize "Standard_F32s_v2"` と同様に VM サイズを指定するパラメーターとともに [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) を使用することができます。


<!-- TBD - IS ASDK --> 
- 仮想マシン スケール セットのスケーリング設定は、ポータルで使用できません。 回避策として、[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) を使用できます。 PowerShell のバージョンの違いにより、`-VMScaleSetName` パラメーターの代わりに `-Name` を使用する必要があります。

<!-- TBD - IS --> 
- ポータルで **[新規]** > **[コンピューティング]** > **[可用性セット]** に移動して可用性セットを作成した場合、障害ドメインと更新ドメインが 1 の可用性セットのみを作成できます。 回避策として、新しい仮想マシンを作成する場合は、PowerShell、CLI、またはポータル内から可用性セットを作成します。

<!-- TBD - IS ASDK --> 
- Azure Stack ユーザー ポータルで仮想マシンを作成するとき、ポータルでは、DS シリーズ VM にアタッチできるデータ ディスクの数に誤った値が表示されます。 DS シリーズ VM は Azure の構成と同数のデータ ディスクをアタッチできます。

<!-- TBD - IS ASDK --> 
- VM の展開で拡張機能のプロビジョニングに時間がかかりすぎる場合、ユーザーは、プロセスを停止して VM の割り当て解除または削除を試みるのではなく、プロビジョニングをタイムアウトさせる必要があります。  

<!-- 1662991 IS ASDK --> 
- Linux の VM 診断は、Azure Stack でサポートされていません。 VM 診断を有効にして Linux VM を展開すると、展開が失敗します。 診断設定で Linux VM の基本メトリックを有効にした場合も、展開が失敗します。  

<!-- 2724961- IS ASDK --> 
- サブスクリプション設定で **Microsoft.Insight** リソース プロバイダーを登録し、ゲスト OS 診断を有効にした Windows VM を作成すると、VM の概要ページにメトリック データが表示されません。 

   VM の CPU 使用率グラフなどのメトリック データを表示するには、**[メトリック]** ブレードに移動して、サポートされているすべての Windows VM ゲスト メトリックを表示します。

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


### <a name="sql-and-mysql"></a>SQL および MySQL



<!-- No Number - IS, ASDK -->  
- SQL と MySQL リソース プロバイダーの SKU を作成する場合、**[ファミリ]** 名では、スペースやピリオドなどの特殊文字はサポートされていません。 

<!-- TBD - IS --> 
- SQL または MySQL をホストするサーバー上に項目を作成できるのは、リソース プロバイダーのみです。 リソース プロバイダー以外がホスト サーバー上に項目を作成すると、不一致状態になる可能性があります。  

<!-- TBD - IS -->
> [!NOTE]   
> このバージョンの Azure Stack に更新した後も、以前にデプロイした SQL および MySQL リソース プロバイダーを引き続き使用できます。  新しいリリースが公開されたら、SQL と MySQL を更新することをお勧めします。 Azure Stack と同様に、SQL と MySQL リソース プロバイダーに順番に更新プログラムを適用します。 たとえば、バージョン 1804 を使用している場合、最初にバージョン 1805 を適用してから 1807 に更新します。  
>  
> この更新プログラムをインストールしても、ユーザーによる現在の SQL または MySQL リソース プロバイダーの使用には影響しません。 
> 使用しているリソース プロバイダーのバージョンに関係なく、データベース内のユーザー データは変更されず、アクセス可能な状態が維持されます。  

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。

<!-- 2489178 - IS ASDK --> 
- インフラストラクチャ (worker、管理、フロントエンド ロール) をスケールアウトするには、コンピューティングのリリース ノートの説明に従って PowerShell を使用する必要があります。

<!-- TBD - IS ASDK --> 
- 現在、App Service は、*既定のプロバイダー サブスクリプション*にのみデプロイできます。 


### <a name="usage"></a>使用法  
<!-- TBD - IS ASDK --> 
- パブリック IP アドレス使用量メーターのデータは、レコードが作成された日時を示す *TimeDate* スタンプではなく、各レコードに対して同じ *EventDateTime* 値を示します。 現在、このデータを使用して、パブリック IP アドレスの使用を正確に算出することはできません。


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>更新プログラムをダウンロードする
Azure Stack 1807 更新プログラム パッケージは、[ここから](https://aka.ms/azurestackupdatedownload)ダウンロードできます。


## <a name="next-steps"></a>次の手順
- Azure Stack 統合システムのサービス ポリシーについて、およびサポートを受けられる状態にシステムを維持するために必要な作業について確認するには、「[Azure Stack サービス ポリシー](azure-stack-servicing-policy.md)」を参照してください。  
- 特権エンドポイント (PEP) を使用して更新プログラムを監視および再開するには、「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](azure-stack-monitor-update.md)」をご覧ください。  
- Azure Stack での更新プログラム管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」を参照してください。  
- Azure Stack に更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」を参照してください。  
