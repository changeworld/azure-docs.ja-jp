---
title: App Service 環境の使用
description: App Service Environment を使用して、独立したアプリケーションをホストする方法について説明します。
author: madsd
ms.topic: article
ms.date: 07/06/2021
ms.author: madsd
ms.openlocfilehash: 6a01e1c746579ea7d51a0b30fd554d2e2e6bb66b
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519643"
---
# <a name="using-an-app-service-environment"></a>App Service Environment の使用

> [!NOTE]
> これは、Isolated v2 App Service プランで使用される App Service Environment v3 に関する記事です
> 

App Service Environment (ASE) は、お客様が選択した Azure Virtual Network (VNet) 内に直接挿入される Azure App Service のシングル テナント デプロイです。 これは、1 つのお客様のみが使用するシステムです。 ASE にデプロイされたアプリは、ASE サブネットに適用されているネットワーク機能を必要とします。 これらのネットワーク機能の管理下とするために、アプリで有効にする必要がある追加の機能はありません。 

## <a name="create-an-app-in-an-ase"></a>ASE 内にアプリを作成する

ASE でアプリを作成する方法は通常のアプリの作成方法とほとんど同じですが、いくつか違いがあります。 新しい App Service プランを作成する場合は、次の点が異なります。

- アプリのデプロイ先の場所として地理的な場所ではなく ASE を選択します。
- ASE で作成する App Service プランはすべて、Isolated v2 の価格レベルでのみ使用できます。

ASE をご利用でない場合は、「[外部 App Service Environment の作成][MakeASE]」の手順に従って作成できます。
ASE 内にアプリを作成するには:

1. **[リソースの作成]**  >  **[Web + モバイル]**  >  **[Web アプリ]** を選択します。
1. サブスクリプションを選択します。
1. 新しいリソース グループの名前を指定するか、 **[既存のものを使用]** を選択して、ボックスの一覧からいずれかを選択します。
1. アプリの名前を入力します。 ASE 内で App Service プランが既に選択されている場合、アプリのドメイン名に ASE のドメイン名が反映されます。
1. 発行の種類、スタック、オペレーティング システムを選択します。
1. リージョンを選択します。 ここでは、既存の App Service Environment v3 を選択する必要があります。 アプリの作成時に ASEv3 を作成することはできません。![ASE でアプリを作成する][1]
1. お使いの ASE 内の既存の App Service プランを選択するか、新しいものを作成します。 新しいアプリを作成する場合は、App Service プラン用のサイズを選択します。 アプリ用に選択できる SKU は、Isolated v2 価格 SKU のみです。 通常、新しい App Service プランの作成に要する時間は 20 分未満です。
![Isolated v2 価格レベル][2]
1. **Next:監視** を選択します。お使いのアプリで App Insights を有効にする場合は、作成フロー中のここで、それを行うことができます。
1.  **Next:タグ** を選択します。アプリに必要なすべのタグを追加します
1. **[確認および作成]** を選択し、情報が正しいことを確認して、 **[作成]** を選択します。

Windows アプリと Linux アプリは同じ ASE 内に配置できますが、同じ App Service プランに含めることはできません。

## <a name="how-scale-works"></a>スケールのしくみ

すべての App Service アプリは、App Service プランで実行されます。 App Service Environment に App Service プランが存在し、App Service プランにアプリが存在します。 アプリをスケールするときは、App Service プラン、および同じプラン内のすべてのアプリをスケールすることになります。

App Service プランをスケールすると、必要なインフラストラクチャが自動的に追加されます。 インフラストラクチャが追加されるまでの間、スケール操作に時間差が生じます。 App Service プランをスケーリングするときに、同じ OS とサイズの別のスケール操作が実行されている場合、要求されたスケールが開始されるまで数分のわずかな遅延が発生することがあります。 あるサイズと OS に対するスケール操作は、サイズと OS の他の組み合わせのスケーリングには影響しません。 たとえば、Windows I2v2 App Service プランをスケーリングする場合、Windows I2v2 をスケーリングする他の要求は少し遅延する可能性がありますが、Windows I3v2 App Service プランのスケール操作はすぐに開始されます。 通常、スケーリングに要する時間は 20 分未満です。

マルチテナントの App Service では、"共有" リソースのプールですぐに対応できるため、スケーリングが即座に実行されます。 ASE はシングルテナント サービスであるため、共有バッファーはありません。リソースは必要に応じて割り当てられます。

## <a name="app-access"></a>アプリのアクセス

内部 VIP を使用する ASE では、アプリの作成に使用されるドメイン サフィックスは、 *.&lt;asename&gt;.appserviceenvironment.net* です。 実際の ASE の名前が _my-ase_ で、その ASE で _contoso_ という名前のアプリをホストする場合、以下の URL でそこにアクセスします。

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

内部 VIP を使用する ASE でホストされているアプリには、ユーザーが、その ASE と同じ仮想ネットワーク上にいる場合、または何らかの方法でその仮想ネットワークに接続されている場合にのみ、アクセスできます。 また、発行についても、ユーザーが同じ仮想ネットワーク上にいる場合、または何らかの方法でその仮想ネットワークに接続されている場合にのみ行えるように制限されています。 

外部 VIP を使用する ASE では、アプリの作成に使用されるサフィックスは、 *.&lt;asename&gt;.p.azurewebsites.net* です。 実際の ASE の名前が _my-ase_ で、その ASE で _contoso_ という名前のアプリをホストする場合、以下の URL でそこにアクセスします。

- contoso.my-ase.p.azurewebsites.net
- contoso.scm.my-ase.p.azurewebsites.net

ASE の作成方法については、「[App Service Environment を作成する][MakeASE]」を参照してください。

SCM URL は、Kudu コンソールにアクセスしたり、Web デプロイを使用してアプリを発行したりするために使用されます。 Kudu コンソールの詳細については、「[Azure App Service の Kudu コンソール][Kudu]」を参照してください。 Kudu コンソールを使用すると、デバッグやファイルのアップロード、ファイルの編集など、さまざまな作業を Web UI で行うことができます。

### <a name="dns-configuration"></a>DNS の構成 

ASE が外部 VIP を使用して作成されている場合、アプリは自動的にパブリック DNS に入れられます。 ASE が内部 VIP を使用して作成されている場合、その DNS 構成が必要になる場合があります。 ASE の作成中に Azure DNS プライベート ゾーンが自動的に構成されるように選択した場合、DNS が ASE VNet に構成されます。 DNS を手動で構成するように選択した場合、独自の DNS サーバーを使用するか、Azure DNS プライベート ゾーンを構成する必要があります。 ASE の受信アドレスを検索するには、 **[ASE ポータル] > [IP アドレス]** UI に移動します。 

![IP アドレス UI][6]

独自の DNS サーバーを使用する場合は、以下のレコードを追加する必要があります。

1. &lt;ASE 名&gt;.appserviceenvironment.net 用のゾーンを作成する
1. ASE で使用される受信 IP アドレスに * をポイントする A レコードをそのゾーン内に作成する
1. ASE で使用される受信 IP アドレスに @ をポイントする A レコードをそのゾーン内に作成する
1. &lt;ASE 名&gt;.appserviceenvironment.net に scm という名前のゾーンを作成する
1. ASE で使用される受信 IP アドレスに * をポイントする A レコードを scm ゾーン内に作成する

Azure DNS プライベート ゾーンで DNS を構成するには、次の操作を行ってください。

1. &lt;ASE 名&gt;.appserviceenvironment.net という名前の Azure DNS プライベート ゾーンを作成する
1. 受信 IP アドレスに * をポイントする A レコードをそのゾーン内に作成する
1. 受信 IP アドレスに @ をポイントする A レコードをそのゾーン内に作成する
1. 受信 IP アドレスに *.scm をポイントする A レコードをそのゾーン内に作成する

ASE の既定のドメイン サフィックスの DNS 設定では、それらの名前によってのみアプリにアクセスできるよう制限されません。 ASE では、アプリの検証なしでカスタム ドメイン名を設定できます。 その後、*contoso.net* という名前のゾーンを作成する場合はそれを行って、受信 IP アドレスを指すようにすることができます。 カスタム ドメイン名はアプリ要求に対して機能しますが、scm サイトでは使用できません。 scm サイトは、 *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net* でのみ使用できます。 

## <a name="publishing"></a>発行

マルチテナントの App Service と同様、ASE では次の方法を使用して発行できます。

- Web デプロイ
- 継続的インテグレーション (CI)
- Kudu コンソールでのドラッグ アンド ドロップ
- IDE (Visual Studio、Eclipse、IntelliJ IDEA など)

内部 VIP ASE の使用時に、発行エンドポイントは受信アドレスを介してのみ使用できます。 受信アドレスへのネットワーク アクセスがない場合、その ASE ではアプリの発行は行えません。  IDE に直接発行する場合、IDE にも ASE 上の受信アドレスに対するネットワーク アクセスが必要です。

発行エンドポイントにはインターネットでアクセスできないため、追加の変更を行わなければ、GitHub や Azure DevOps などのインターネット ベースの CI システムは内部 VIP ASE では動作しません。 ASE を含む仮想ネットワークでセルフホステッド リリース エージェントをインストールすることで、Azure DevOps から内部 VIP ASE への発行を有効にすることができます。 

## <a name="storage"></a>ストレージ

ASE には、ASE 内のすべてのアプリ用に 1 TB のストレージがあります。 Isolated 価格 SKU の App Service プランには、250 GB の制限があります。 ASE では、App Service プランごとに 250 GB のストレージが追加されます。上限は 1 TB です。 App Service プランは 4 つ以上持つことができますが、ストレージは 1 TB の上限を超えて追加されることはありません。

## <a name="logging"></a>ログ記録

ASE を Azure Monitor と統合して、ASE に関するログを Azure Storage、Azure Event Hubs、または Log Analytics に送信できます。 現在、次の項目がログに記録されます。

|状況 |Message |
|----------|--------|
|ASE subnet is almost out of space (ASE サブネットの領域が不足しています) | The specified ASE is in a subnet that is almost out of space. (指定された ASE は、空き領域がほとんどないサブネット内にあります。) There are {0} remaining addresses. (残りのアドレスは {0} 個です。) Once these addresses are exhausted, the ASE will not be able to scale (これらのアドレスが枯渇すると、ASE はスケーリングできなくなります)  |
|ASE is approaching total instance limit (ASE で、インスタンスの合計数の上限に近づいています) | The specified ASE is approaching the total instance limit of the ASE. (指定された ASE で、ASE のインスタンスの合計数の上限に近づいています) 現在、最大インスタンス数 200 のうち、{0} 個の App Service プラン インスタンスが含まれています。 |
|ASE is suspended (ASE は中断されています) | The specified ASE is suspended. (指定された ASE は中断されています。) The ASE suspension may be due to an account shortfall or an invalid virtual network configuration. (ASE の中断は、アカウントの不足または無効な仮想ネットワーク構成が原因である可能性があります。) Resolve the root cause and resume the ASE to continue serving traffic (根本原因を解決し、ASE を再開してトラフィックの処理を続行してください) |
|ASE upgrade has started (ASE のアップグレードが開始されました) | A platform upgrade to the specified ASE has begun. (指定された ASE へのプラットフォームのアップグレードが開始されました。) Expect delays in scaling operations (スケーリング操作で遅延が発生することが予想されます) |
|ASE upgrade has completed (ASE のアップグレードが完了しました) | A platform upgrade to the specified ASE has finished (指定された ASE へのプラットフォームのアップグレードが完了しました) |
|App Service プランの作成が開始されました | App Service プラン ({0}) の作成が開始されました。 望ましい状態: {1} I{2}v2 ワーカー。
|Scale operations have completed (スケール操作が完了しました) | App Service プラン ({0}) の作成が完了しました。 現在の状態: {1} I{2}v2 ワーカー。 |
|Scale operations have failed (スケール操作が失敗しました) | App Service プラン ({0}) の作成が失敗しました。 これは、ASE がインスタンスのピーク数で動作しているか、サブネットアドレスが不足していることが原因である可能性があります。 |
|Scale operations have started (スケール操作が開始されました) | An App Service plan ({0}) has begun scaling. (App Service プラン ({0}) でスケーリングが開始されました。) 現在の状態: {1} I(2)v2。 望ましい状態: {3} I{4}v2 ワーカー。|
|Scale operations have completed (スケール操作が完了しました) | An App Service plan ({0}) has finished scaling. (App Service プラン ({0}) でスケーリングが完了しました。) 現在の状態: {1} I{2}v2 ワーカー。 |
|スケール操作が中断されました | スケーリング中に App Service プラン ({0}) が中断されました。 以前の望ましい状態: {1} I{2}v2 ワーカー。 新しい望ましい状態: {3} I{4}v2 ワーカー。 |
|Scale operations have failed (スケール操作が失敗しました) | An App Service plan ({0}) has failed to scale. (App Service プラン ({0}) でスケーリングに失敗しました。) 現在の状態: {1} I{2}v2 ワーカー。 |

ASE でログ記録を有効にするには、次のようにします。

1. ポータルで **[診断設定]** に移動します。
1. **[診断設定の追加]** を選択します。
1. ログ統合の名前を指定します。
1. 目的のログの保存先を選択して構成します。
1. **[AppServiceEnvironmentPlatformLogs]** を選択します。
![ASE 診断ログの設定][4]

Log Analytics と統合している場合は、ASE ポータルから **[ログ]** を選択し、**AppServiceEnvironmentPlatformLogs** に対するクエリを作成することでログを表示できます。 ログは、ASE にトリガーされるイベントがある場合にのみ出力されます。 ASE にそのようなイベントがない場合は、どのようなログも存在しません。 Log Analytics ワークスペースでログの例をすばやく確認するには、ASE で App Service プランを使用してスケール操作を実行します。 その後、**AppServiceEnvironmentPlatformLogs** に対してクエリを実行し、これらのログを確認できます。 

### <a name="creating-an-alert"></a>アラートの作成

ログに対してアラートを作成するには、「[Azure Monitor を使用したログ アラートの作成、表示、管理](../../azure-monitor/alerts/alerts-log.md)」の手順に従います。 概要:

* ASE ポータルで [アラート] ページを開く
* **[新しいアラート ルール]** を選択する
* Log Analytics ワークスペースになるリソースを選択する
* カスタムログ検索で条件を設定し、「AppServiceEnvironmentPlatformLogs |」のようなクエリを使用します。ここで、ResultDescription には「has begun scaling」または任意のものを指定します。 適切なしきい値を設定します。 
* 適宜、アクション グループを追加するか作成します。 アクション グループでは、電子メールや SMS メッセージの送信など、アラートへの応答を定義します
* アラートに名前を付けて保存します。

## <a name="internal-encryption"></a>内部の暗号化

App Service Environment は、内部コンポーネントまたはシステム内の通信を表示できないブラック ボックス システムとして動作します。 より高いスループットを実現するために、内部コンポーネント間の暗号化は既定では有効になっていません。 監視またはアクセスの対象としてトラフィックにアクセスすることはできないため、システムの安全性は確保されています。 それでもデータ パスを端から端まで完全に暗号化する必要があるコンプライアンス要件がある場合は、ASE の **[構成]** UI でこれを有効にできます。

![内部暗号化を有効にする][5]

これで、ASE のフロントエンドとワーカーとの間の内部ネットワーク トラフィックが暗号化され、ページ ファイルが暗号化されるほか、ワーカーのディスクも暗号化されます。 InternalEncryption clusterSetting を有効にすると、システムのパフォーマンスに影響する可能性があります。 InternalEncryption を有効にするように変更を加えると、変更が完全に反映されるまで ASE が不安定な状態になります。 ASE 上に存在するインスタンスの数によっては、変更の反映が完了するまで数時間かかる可能性があります。 ASE の使用中は、この機能を有効にしないことを強くお勧めします。 アクティブに使用されている ASE でこれを有効にする必要がある場合は、操作が完了するまで、トラフィックをバックアップ環境に転送することを強くお勧めします。

## <a name="upgrade-preference"></a>アップグレードの優先順位

複数の ASE を使用している場合は、一部の ASE を他のものよりも先にアップグレードする必要が生じることがあります。 この動作は、ASE ポータルを使用して有効にできます。  **構成** の下に、**アップグレードの優先順位** を設定するオプションがあります。 指定可能な 3 つの値は次のとおりです。

- **なし**: Azure では、特定のバッチなしに ASE がアップグレードされます。 この値は既定値です。
- **Early**:ASE は App Service アップグレードの前半でアップグレードされます。
- **Late**:ASE は App Service アップグレードの後半でアップグレードされます。

目的の値を選択し、 **[保存]** を選択します。  あらゆる ASE の既定値は **[なし]** です。

![ASE 構成ポータル][5]

**upgradePreferences** 機能は、複数の ASE がある場合に非常に便利です。"Early" の ASE が "Late" の ASE よりも先にアップグレードされるためです。 複数の ASE がある場合は、開発およびテストの ASE を "Early" に、実稼働の ASE を "Late" に設定します。

## <a name="delete-an-ase"></a>ASE の削除

ASE を削除するには、次の手順に従います。

1. **[App Service Environment]** ウィンドウの上部にある **[削除]** を選択します。
1. ASE の名前を入力して削除を確定します。 ASE を削除すると、その内部のコンテンツもすべて削除されます。
![ASE の削除][3]
1. **[OK]** を選択します。

<!--Image references-->

[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png
[6]: ./media/using/using-ip-addresses.png

<!--Links-->

[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: ./integrate-with-application-gateway.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md