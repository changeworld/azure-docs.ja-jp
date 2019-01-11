---
title: Azure Stack 1809 更新プログラム | Microsoft Docs
description: 既知の問題、更新プログラムをダウンロードする場所など、Azure Stack 統合システムの 1809 更新プログラムの更新内容について説明します。
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
ms.openlocfilehash: f3994c2be50939a837256224030e5284cc6f385b
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754052"
---
# <a name="azure-stack-1809-update"></a>Azure Stack 1809 更新プログラム

*適用対象: Azure Stack 統合システム*

この記事では、1809 更新プログラム パッケージの内容について説明します。 更新プログラム パッケージには、このバージョンの Azure Stack に対する機能強化、修正、既知の問題が含まれています。 また、更新プログラムをダウンロードできるリンクも掲載されています。 既知の問題は、更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]  
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス

Azure Stack 1809 更新プログラムのビルド番号は **1.1809.0.90** です。  

### <a name="new-features"></a>新機能

この更新プログラムには、Azure Stack に対する次の機能強化が含まれています。

- このリリースでは、Azure Stack 統合システムは 4 ～ 16 ノードの構成をサポートします。 [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) を使用すると、Azure Stack の容量と構成の計画に役立ちます。

- <!--  2712869   | IS  ASDK -->  **Azure Stack syslog クライアント (一般提供)** このクライアントを使用すると、Azure Stack インフラストラクチャに関連する監査ログ、アラート、およびセキュリティログを、Azure Stack の外部にある Syslog サーバーまたはセキュリティ情報イベント管理 (SIEM) ソフトウェアに転送できます。 Syslog のクライアントは、syslog サーバーがリッスンするポートを指定できるようになりました。

   このリリースでは、syslog クライアントは、一般利用可能であり、運用環境のために使用できます。

   詳細については、「[Azure Stack の Syslog 転送](azure-stack-integrate-security.md)」を参照してください。

- 再登録しなくても、リソース グループ間で Azure 上の[登録リソースを移動](azure-stack-registration.md#move-a-registration-resource)できるようになりました。 また、クラウド ソリューション プロバイダー (CSP) でも、新しいサブスクリプションと古いサブスクリプションが両方とも同じ CSP パートナー ID にマッピングされている限り、サブスクリプション間で登録リソースを移動することができます。 これは、既存の顧客テナントのマッピングには影響しません。 

### <a name="fixed-issues"></a>修正された問題

<!-- TBD - IS ASDK -->
- ポータルで、空き/使用済み容量を報告するメモリのグラフが正確になりました。 作成できる VM の数をより確実に予測できるようになりました。

<!-- TBD - IS ASDK --> 
- Azure Stack ユーザー ポータルで仮想マシンを作成して、ポータルで、DS シリーズ VM にアタッチできるデータ ディスクの数が誤って表示されていた問題が修正されました。 DS シリーズ VM は Azure の構成と同数のデータ ディスクをアタッチできます。

- 次のマネージド ディスクの問題は 1809 で修正され、1808 [Azure Stack 修正プログラム 1.1808.9.117](https://support.microsoft.com/help/4481066/) でも修正されています。 

   <!--  2966665 – IS, ASDK --> 
   - SSD データ ディスクをプレミアム サイズのマネージド ディスク仮想マシン (DS、DSv2、Fs、Fs_V2) にアタッチするときに次のエラーで失敗するという問題を修正しました。*仮想マシン 'vmname' のディスクを更新できませんでした。エラー: ストレージ アカウントの種類 'Premium_LRS' は VM サイズ (Standard_DS/Ds_V2/FS/Fs_v2)でサポートされていないため、要求された操作は実行できません。* 
   
   - **createOption** を使用してマネージド ディスク VM を作成しています: **アタッチ**は次のエラーで失敗します: *長時間実行操作が状態 '失敗' で失敗しました。Additional Info:'An internal execution error occurred.' (追加情報:'内部実行エラーが発生しました。')*
   ErrorCode:InternalExecutionError ErrorMessage: 内部実行エラーが発生しました。
   
   この問題は修正されました。

- <!-- 2702741 -  IS, ASDK --> 動的割り当てメソッドを使用してデプロイされているパブリック IP の固定された問題は、停止-割り当て解除が発行された後も保持される保証はありませんでした。 これらは保存されるようになりました。

- <!-- 3078022 - IS, ASDK --> VM が 1808 の前に停止‐割り当て解除された場合、これは 1808 の更新後に再割り当てできませんでした。  この問題は 1809 で修正されています。 ここの状態にあり開始できなかったインスタンスは、この修正によって 1809 で開始できます。 また、修正プログラムにより、この問題は再発しないようになります。

### <a name="changes"></a>変更点

<!-- 2635202 - IS, ASDK -->
- インフラストラクチャ バックアップ サービスは、[パブリック インフラストラクチャ ネットワーク](https://docs.microsoft.com/azure/azure-stack/azure-stack-network#public-infrastructure-network)から[パブリック VIP ネットワーク](https://docs.microsoft.com/azure/azure-stack/azure-stack-network#public-vip-network)に移動します。 お客様は、サービスがパブリック VIP ネットワークからバックアップ ストレージの場所にアクセスできることを確認する必要があります。  

> [!IMPORTANT]  
> パブリック VIP ネットワークからファイル サーバーへの接続を許可しないファイアウォールがある場合、この変更は、インフラストラクチャのバックアップが "エラー 53 ネットワーク パスが見つかりませんでした" で失敗する原因となります。 これは、妥当な回避策がない破壊的変更です。 お客様からのフィードバックに基づき、マイクロソフトでは、修正プログラム内でこの変更を元に戻します。 1809 に使用できる修正プログラムについて詳しくは、[「更新後の手順」セクション](#post-update-steps)をご覧ください。 修正プログラムが利用可能になったら、パブリック VIP ネットワークによるインフラストラクチャ リソースへのアクセスがネットワーク ポリシーによって許可されない場合にのみ、1809 への更新後に修正プログラムを適用してください。 1811 では、この変更はすべてのシステムに適用されます。 1809 に修正プログラムを適用した場合、他に必要な操作はありません。  

### <a name="common-vulnerabilities-and-exposures"></a>共通脆弱性識別子

この更新では、次のセキュリティ更新プログラムがインストールされます。  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

これらの脆弱性の詳細については、上記のリンクをクリックするか、Microsoft サポート技術情報の記事 [4457131](https://support.microsoft.com/help/4457131) と [4462917](https://support.microsoft.com/help/4462917) を参照してください。

### <a name="prerequisites"></a>前提条件

- 1809 を適用する前に、1808 の最新の Azure Stack 修正プログラムをインストールしてください。 詳細については、[KB 4481066 – Azure Stack 修正プログラム Azure Stack 修正プログラム 1.1808.9.117](https://support.microsoft.com/help/4481066/) を参照してください。

  > [!TIP]  
  > 以下の *RRS* または *Atom* フィードに登録して、Azure Stack の修正プログラムの最新情報を入手してください:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- この更新プログラムのインストールを開始する前に、次のパラメーターを指定して [Test-AzureStack](azure-stack-diagnostic-test.md) を実行して Azure Stack の状態を確認し、見つかったすべての操作上の問題 (すべての警告とエラーを含む) を解決します。 また、アクティブなアラートを確認し、アクションが必要なアラートを解決します。  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題

- 1809 更新の後で [Test-AzureStack](azure-stack-diagnostic-test.md) を実行すると、Baseboard Management Controller (BMC) からの警告メッセージが表示されます。 この警告は無視してかまいません。

- <!-- 2468613 - IS --> この更新プログラムのインストール中に、"*エラー – FaultType UserAccounts.New のテンプレートが見つかりません*" というタイトルのアラートが表示される場合があります。  これらのアラートは無視してかまいません。 この更新プログラムのインストール後、これらのアラートは自動的に閉じられます。

- <!-- 2489559 - IS --> この更新プログラムのインストール中に仮想マシンを作成しようとしないでください。 更新プログラムの管理方法については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md#plan-for-updates)」を参照してください。

- <!-- 3139614 | IS --> Azure Stack の更新を OEM から適用した場合は、Azure Stack 管理ポータルに「**利用可能な更新プログラムがあります**」の通知が表示されないことがあります。 Microsoft Update をインストールするには、こちらの「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」に記載されている手順を使用して、手動でダウンロードおよびインポートしてください。

### <a name="post-update-steps"></a>更新後の手順

> [!Important]  
> Azure Stack デプロイを、次の更新プログラム パッケージによって有効化さる拡張機能ホストに合わせて準備してください。 次のガイダンスを使用して、システムを準備します: 「[Azure Stack の拡張機能ホストを準備する](azure-stack-extension-host-prepare.md)」。

この更新プログラムをインストールした後、適用可能な修正プログラムがあればインストールします。 詳細については、以下のサポート技術情報と[サービス ポリシー](azure-stack-servicing-policy.md)に関するページを参照してください。  
- [KB 4481548 – Azure Stack 修正プログラム Azure Stack 修正プログラム 1.1809.12.114](https://support.microsoft.com/help/4481548/)  

## <a name="known-issues-post-installation"></a>既知の問題 (インストール後)

このビルド バージョンのインストール後について次の既知の問題があります。

### <a name="portal"></a>ポータル

- Azure Stack のテクニカル ドキュメントは、最新のリリースについて説明しています。 リリースごとにポータルが変更されるため、Azure Stack ポータルを使用した場合の動作と、ドキュメントに示されている内容が異なる場合があります。

<!-- 2930718 - IS ASDK --> 
- 管理ポータルで、ユーザーのサブスクリプションの詳細にアクセスした後、ブレードを閉じて、**[最近]** をクリックした場合、ユーザーのサブスクリプション名が表示されません。

<!-- 3060156 - IS ASDK --> 
- 管理ポータルとユーザー ポータルの両方で、ポータルの設定をクリックした後、**[すべての設定とプライベート ダッシュボードを削除]** を選んでも、想定どおりに機能しません。 エラー通知が表示されます。 

<!-- 2930799 - IS ASDK --> 
- 管理ポータルとユーザー ポータルの両方で、**[すべてのサービス]** の下に、資産 **[DDoS protection プラン]** が表示されますが、これは正しくありません。 これは Azure Stack でご利用になれません。 これを作成しようとすると、ポータルが Marketplace アイテムを作成できなかったというエラーが表示されます。 

<!-- 2930820 - IS ASDK --> 
- 管理ポータルとユーザー ポータルの両方で、"Docker" を検索すると、返される項目が正しくありません。 これは Azure Stack でご利用になれません。 これを作成しようとすると、エラーを示すブレードが表示されます。 

<!-- 2967387 – IS, ASDK --> 
- Azure Stack の管理ポータルまたはユーザー ポータルへのサインインに使用したアカウントが、**[識別されないユーザー]** と表示されます。 このメッセージは、アカウントに*名*と*姓*がどちらも指定されていない場合に表示されます。 この問題を回避するには、ユーザー アカウントを編集して、名または姓のどちらかを指定してください。 その後、いったんサインアウトし、ポータルにもう一度サインインする必要があります。  

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

  この問題は最新の [1809 修正プログラム リリース](https://support.microsoft.com/help/4481548/)で修正されているので、問題が発生している場合は、この修正プログラムをインストールしてください。 

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

<!-- 2368581 - IS, ASDK --> 
- Azure Stack オペレーターで、メモリ不足のアラートを受信し、テナント仮想マシンが**ファブリック VM の作成エラー**でデプロイできなかった場合、Azure Stack スタンプに使用できるメモリが不足している可能性があります。 ワークロードに使用できる容量の詳細については、[Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) に関するページを参照してください。

### <a name="compute"></a>Compute

<!-- 3235634 – IS, ASDK -->
- **v2** サフィックスを含むサイズ (**Standard_A2_v2** など) で VM をデプロイするには、サフィックスを **Standard_A2_v2** (小文字の v) と指定してください。 **Standard_A2_V2** (大文字の V) は使用しないでください。 これは、グローバル Azure で動作し、Azure Stack では不整合になります。

<!-- 3099544 – IS, ASDK --> 
- Azure Stack ポータルを使用して新しい仮想マシン (VM) を作成し、VM サイズを選択するときに、[米国ドル/月] 列に **[利用不可]** のメッセージが表示されます。 VM 価格の列の表示は、Azure Stack ではサポートされておらず、この列は表示されるべきではありません。

<!-- 2869209 – IS, ASDK --> 
- [**Add-AzsPlatformImage** コマンドレット](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0)を使用する場合は、ディスクのアップロード先のストレージ アカウント URI として **-OsUri** パラメーターを使用する必要があります。 ディスクのローカル パスを使用した場合、次のエラーが出てコマンドレットが失敗します: *長時間実行処理が状態 '失敗' で失敗しました*。 

<!--  2795678 – IS, ASDK --> 
- ポータルを使用して Premium VM サイズ (DS、Ds_v2、FS、FSv2) の仮想マシン (VM) を作成すると、VM は Standard ストレージ アカウントで作成されます。 Standard ストレージ アカウントで作成しても、機能、IOPS、課金に影響はありません。 

   次の警告は無視してかまいません: *Premium ディスクをサポートしているサイズで Standard ディスクを使用することが選択されました。これはオペレーティング システムのパフォーマンスに影響を与える可能性があるため、お勧めできません。代わりに Premium ストレージ (SSD) の使用を検討してください。*

<!-- 2967447 - IS, ASDK --> 
- 仮想マシン スケール セット (VMSS) の作成エクスペリエンスでは、デプロイのオプションとして CentOS-based 7.2 が提供されます。 このイメージは Azure Stack では使用できないため、デプロイ用に別の OS を選択するか、またはデプロイする前にオペレーターが Marketplace からダウンロードしておいた別の CentOS イメージを指定する Azure Resource Manager テンプレートをご使用ください。  

<!-- 2724873 - IS --> 
- スケール ユニットの管理に PowerShell コマンドレットの **Start-AzsScaleUnitNode** または **Stop-AzsScaleunitNode** を使った場合、スケール ユニットを開始または停止しようとすると、1 回目は失敗することがあります。 初回実行時にコマンドレットが失敗した場合は、もう一度コマンドレットを実行してください。 2 回目は操作が正常に完了します。 

<!-- TBD - IS ASDK --> 
- VM の展開で拡張機能のプロビジョニングに時間がかかりすぎる場合、ユーザーは、プロセスを停止して VM の割り当て解除または削除を試みるのではなく、プロビジョニングをタイムアウトさせる必要があります。  

<!-- 1662991 IS ASDK --> 
- Linux の VM 診断は、Azure Stack でサポートされていません。 VM 診断を有効にして Linux VM を展開すると、展開が失敗します。 診断設定で Linux VM の基本メトリックを有効にした場合も、展開が失敗します。  

<!-- 2724961- IS ASDK --> 
- サブスクリプション設定で **Microsoft.Insight** リソース プロバイダーを登録し、ゲスト OS 診断を有効にした Windows VM を作成すると、VM の概要ページの CPU 使用率グラフにメトリック データが表示されません。

   VM の CPU 使用率グラフなどのメトリック データを表示するには、[メトリック] ウィンドウに移動して、サポートされているすべての Windows VM ゲスト メトリックを表示します。

<!-- 3507629 - IS, ASDK --> 
- マネージド ディスクは 2 つの新しい[コンピューティング クォータの種類](azure-stack-quota-types.md#compute-quota-types)を作成して、プロビジョニングできるマネージド ディスクの最大容量を制限します。 既定では、2048 GiB がマネージド ディスク クォータの種類ごとに割り当てられます。 ただし、次の問題が発生する可能性があります。

   - 更新プログラム 1808 より前に作成されたクォータでは、マネージド ディスクのクォータは 2048 GiB が割り当てられているものの、管理者ポータルでは値 0 が表示されます。 値は実際のニーズに基づいて増減でき、新しく設定したクォータ値が 2048 GiB の既定値をオーバーライドします。
   - クォータ値を 0 に更新することは、2048 GiB の既定値と同じことになります。 この問題を回避するには、クォータ値を 1 に設定します。

<!-- TBD - IS ASDK --> 
- 更新プログラム 1809 の適用後、Managed Disks を使用した VM をデプロイするときに、次の問題が発生する可能性があります。

   - 1808 更新の前に作成したサブスクリプションの場合、マネージド ディスクを使用した VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、サブスクリプションごとに次の手順に従ってください。
      1. テナント ポータルで、**[サブスクリプション]** に移動して、サブスクリプションを検索します。 **[リソース プロバイダー]** をクリックし、**[Microsoft.Compute]** をクリックした後、**[再登録]** をクリックします。
      2. 同じサブスクリプションで、**[アクセス制御 (IAM)]** に移動し、**[Azure Stack – マネージド ディスク]** がリストに含まれていることを確認します。
   2. マルチテナント環境を構成した場合、ゲスト ディレクトリに関連付けられているサブスクリプションで VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、[この記事](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)にある手順に従って、各ゲスト ディレクトリを構成します。

- SSH の認可を有効にして作成した Ubuntu 18.04 VM では、SSH キーを使用してログインすることはできません。 回避策として、プロビジョニング後に Linux 拡張機能用の VM アクセスを使用して SSH キーを実装するか、パスワード ベースの認証を使用してください。

### <a name="networking"></a>ネットワーク  

<!-- 1766332 - IS ASDK --> 
- **[ネットワーク]** で、**[Create VPN Gateway]\(VPN ゲートウェイの作成\)** をクリックして VPN 接続を設定する場合、VPN の種類として **[ポリシー ベース]** が表示されます。 このオプションを選択しないでください。 Azure Stack では **[ルート ベース]** オプションのみがサポートされています。

<!-- 1902460 - IS ASDK --> 
- Azure Stack では、IP アドレスごとに 1 つの "*ローカル ネットワーク ゲートウェイ*" がサポートされます。 これは、テナントのすべてのサブスクリプションに当てはまります。 最初のローカル ネットワーク ゲートウェイ接続を作成した後に、続いて同じ IP アドレスでローカル ネットワーク ゲートウェイ リソースを作成しようとすると、ブロックされます。

<!-- 16309153 - IS ASDK --> 
- "*自動*" の DNS サーバー設定を使用して作成した仮想ネットワークで、カスタム DNS サーバーに変更すると失敗します。 更新した設定は、その Vnet 内の VM にプッシュされません。

<!-- 2529607 - IS ASDK --> 
- Azure Stack の "*シークレット ローテーション*" 中は、2 ～ 5 分間、Public IP Addresses に到達できない期間があります。

<!-- 2664148 - IS ASDK --> 
-   テナントが S2S VPN トンネルを使用して仮想マシンにアクセスするシナリオでは、ゲートウェイが既に作成された後でオンプレミスのサブネットがローカル ネットワーク ゲートウェイに追加された場合、接続しようとしても失敗するシナリオが発生する可能性があります。 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。


### <a name="usage"></a>使用法  

<!-- TBD - IS ASDK --> 
- パブリック IP アドレス使用量メーターのデータは、レコードが作成された日時を示す *TimeDate* スタンプではなく、各レコードに対して同じ *EventDateTime* 値を示します。 現在、このデータを使用して、パブリック IP アドレスの使用を正確に算出することはできません。


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>更新プログラムをダウンロードする

Azure Stack 1809 更新プログラム パッケージは、[ここから](https://aka.ms/azurestackupdatedownload)ダウンロードできます。
  

## <a name="next-steps"></a>次の手順

- Azure Stack 統合システムのサービス ポリシーについて、およびサポートを受けられる状態にシステムを維持するために必要な作業について確認するには、「[Azure Stack サービス ポリシー](azure-stack-servicing-policy.md)」を参照してください。  
- 特権エンドポイント (PEP) を使用して更新プログラムを監視および再開するには、「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](azure-stack-monitor-update.md)」をご覧ください。  
- Azure Stack での更新プログラム管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」を参照してください。  
- Azure Stack に更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」を参照してください。  
