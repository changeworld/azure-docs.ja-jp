---
title: Azure Security Center に含まれる Microsoft Defender for Endpoint ライセンスを使用する
description: Microsoft Defender for Endpoint と、Azure Security Center からのデプロイについて説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 08/16/2021
ms.author: memildin
ms.openlocfilehash: 529a88c2af5c47cb329006b58f2439ab95f40ba2
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228747"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Security Center の統合 EDR ソリューションを使用してエンドポイントを保護する: Microsoft Defender for Endpoint

Microsoft Defender for Endpoint は、クラウドで提供される包括的なエンドポイント セキュリティ ソリューションです。 主な機能は次のとおりです。

- リスクベースの脆弱性の管理と評価 
- 攻撃の回避
- 行動ベースおよびクラウドを利用した保護
- エンドポイントでの検出と対応 (EDR)
- 調査と修復の自動化
- マネージド ハンティング サービス

> [!TIP]
> 元々は **Windows Defender ATP** として提供されたこのエンドポイントでの検出と対応 (EDR) 製品は、2019 年に名前が **Microsoft Defender ATP** に変更されました。
>
> Ignite 2020 で、[Microsoft Defender XDR スイート](https://www.microsoft.com/security/business/threat-protection)の提供が開始されました。この EDR コンポーネントの名前は、**Microsoft Defender for Endpoint** に変更されました。


## <a name="availability"></a>可用性

| 側面                                       | 詳細                                                                                                                                                                                                                                                                               |
|----------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| リリース状態:                               | • Defender for Endpoint for Windows との統合 - 一般公開 (GA)<br> • Defender for Endpoint for Linux との統合 - プレビュー                                                                                                                                     |
| 価格:                                     | [Azure Defender for servers](defender-for-servers-introduction.md) が必要                                                                                                                                                                                                           |
| サポートされている環境:                      | :::image type="icon" source="./media/icons/yes-icon.png"::: Windows/Linux を実行している Azure Arc マシン<br>:::image type="icon" source="./media/icons/yes-icon.png":::Linux を実行している Azure VM ([サポートされているバージョン](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux))<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Windows Server 2019、2016、2012 R2、2008 R2 SP1、[Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md)、[Windows 10 Enterprise マルチセッション](../virtual-desktop/windows-10-multisession-faq.yml) (旧称 Enterprise for Virtual Desktops (EVD) を実行している Azure VM<br>:::image type="icon" source="./media/icons/no-icon.png"::: Windows 10 を実行している Azure VM (EVD または WVD 以外)           |
| 必要なロールとアクセス許可:              | • 統合を有効または無効にするには: **セキュリティ管理者** または **所有者**<br>• Defender for Endpoint アラートを Security Center で表示するには: **セキュリティ閲覧者**、**閲覧者**、**リソース グループの共同作成者**、**リソース グループの所有者**、**セキュリティ管理者**、**サブスクリプションの所有者**、または **サブスクリプションの共同作成者** |
| クラウド:                                      | :::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure China 21Vianet                                                         |
|                                              |                                                                                                                                                                                                                                                                                       |

## <a name="benefits-of-integrating-microsoft-defender-for-endpoint-with-security-center"></a>Microsoft Defender for Endpoint を Security Center と統合することの利点

Microsoft Defender for Endpoint では次のものが提供されます。

- **高度な侵害後検出センサー**。 Defender for Endpoint のセンサーにより、ありとあらゆる動作の信号がコンピューターから収集されます。

- **分析に基づくクラウド利用の侵害後検出**。 Defender for Endpoint は、脅威の変化にすばやく適応します。 高度な分析とビッグ データを使用します。 Windows、Azure、Office 全体の信号を使用したインテリジェント セキュリティ グラフによって強化され、これにより未知の脅威が検出されます。 アクションにつながるアラートを提供し、迅速に対応できるようにします。

- **脅威インテリジェンス**。 Defender for Endpoint によって攻撃者のツール、テクニック、およびプロシージャが識別されると、アラートが生成されます。 Microsoft の脅威ハンターおよびセキュリティ チームによって生成され、パートナーによって提供されるインテリジェンスによって強化されたデータを使用します。

Defender for Endpoint を Security Center と統合すると、次の追加機能を利用することができます。

- **自動オンボード**。 Security Center に接続しているすべてのサポート対象マシンで Defender for Endpoint センサーを、Security Center が自動的に有効にします。

- **1 つのウィンドウ**。 Security Center ポータルのページには、Defender for Endpoint のアラートが表示されます。 さらに調査するには、Microsoft Defender for Endpoint 独自のポータル ページを使用します。ここには、アラート プロセス ツリーやインシデント グラフなどの追加情報が表示されます。 最大 6 か月前まで遡って、すべての動作を示す詳細なマシン タイムラインを見ることもできます。

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender for Endpoint 独自の Security Center" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="what-are-the-requirements-for-the-microsoft-defender-for-endpoint-tenant"></a>Microsoft Defender for Endpoint テナントの要件

Security Center を使用してコンピューターを監視すると、Defender for Endpoint テナントが自動的に作成されます。

- **場所:** Defender for Endpoint により収集されたデータは、プロビジョニング時に特定されるテナントの所属地域に保存されます。 偽名フォームの顧客データは、米国の中央のストレージおよび処理システムにも格納される可能性があります。 場所を構成した後は、その場所を変更することはできません。 Microsoft Defender for Endpoint のライセンスを所有していて、データを別の場所に移動する必要がある場合は、[Microsoft サポートに連絡](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)してテナントをリセットしてください。
- **サブスクリプションの移動:** Azure のサブスクリプションを Azure テナント間で移動させた場合、いくつかの準備作業を手動で行って、Security Center から Defender for Endpoint を展開できるようにする必要があります。 詳細については、[Microsoft サポートにお問い合わせください](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。


## <a name="enable-the-microsoft-defender-for-endpoint-integration"></a>Microsoft Defender for Endpoint 統合を有効にする

### <a name="prerequisites"></a>前提条件

お使いのマシンが Defender for Endpoint に必要な要件を満たしていることを確認します。

1. 適切な方法でコンピューターを Azure とインターネットに接続していることを確認します。

    - **Azure 仮想マシン (Windows または Linux)** - デバイス プロキシとインターネット接続の設定を構成する: [Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet) または [Linux](/microsoft-365/security/defender-endpoint/linux-static-proxy-configuration) の説明に従ってネットワーク設定を構成します。

    - **オンプレミスのコンピューター** - 「[Azure Arc 対応サーバーにハイブリッド マシンを接続する](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)」の説明に従って、ターゲット マシンを Azure Arc に接続します。

1. **Azure Defender for servers** を有効にします。 「[クイックスタート: Azure Defender を有効にする](enable-azure-defender.md)」をご覧ください。

1. Azure テナント間でサブスクリプションを移動した場合は、手動による準備手順も必要になります。 詳細については、[Microsoft サポートにお問い合わせください](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。




### <a name="enable-the-integration"></a>統合を有効にする

### <a name="windows"></a>[**Windows**](#tab/windows)

1. Security Center のメニューで、 **[価格と設定]** を選択し、Defender for Endpoint を受け取る Windows マシンでサブスクリプションを選択します。

1. **[Integrations]\(統合\)** を選択します。

1. **[データに Microsoft Defender for Endpoint がアクセスすることを許可する]** を選択し、 **[保存]** を選択します。

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Azure Security Center および Microsoft の EDR ソリューションの Microsoft Defender for Endpoint 間の統合を有効にする":::

    Azure Security Center によって、コンピューターが Microsoft Defender for Endpoint に自動的にオンボードされます。 このオンボードには最大で 24 時間かかることがあります。

### <a name="linux-preview"></a>[**Linux** (プレビュー)](#tab/linux)

プレビュー期間中は、Defender for Endpoint を Linux マシンに次の 2 つの方法のいずれかでデプロイします (既に Windows マシンにデプロイしたかどうかに応じて)。

- [Azure Defender および Microsoft Defender for Endpoint for Windows の既存ユーザー](#existing-users-of-azure-defender-and-microsoft-defender-for-endpoint-for-windows)
- [Microsoft Defender for Endpoint for Windows との統合を有効にしたことがない新規ユーザー](#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)


### <a name="existing-users-of-azure-defender-and-microsoft-defender-for-endpoint-for-windows"></a>Azure Defender および Microsoft Defender for Endpoint for Windows の既存ユーザー

**Defender for Endpoint for Windows** との統合が既に有効になっている場合、Defender for Endpoint を **Linux** マシンにデプロイするタイミングと方法を完全に制御できます。

1. Security Center のメニューで、 **[価格と設定]** を選択し、Defender for Endpoint を受け取る Linux マシンでサブスクリプションを選択します。

1. **[Integrations]\(統合\)** を選択します。 **[Allow Microsoft Defender for Endpoint to access my data]\(Microsoft Defender for Endpoint にデータへのアクセスを許可する\)** チェックボックスが次のようにオンになっている場合、統合は有効になっています。

    :::image type="content" source="./media/security-center-wdatp/integration-enabled.png" alt-text="Azure Security Center と Microsoft の EDR ソリューション、Microsoft Defender for Endpoint との間の統合は有効になっている":::

    > [!NOTE]
    > 選択されていない場合は、[Microsoft Defender for Endpoint for Windows との統合を有効にしたことがない新規ユーザー](#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)の指示に従います。

    このプレビューでは、このチェックボックスの下に新しい **[Linux マシン用の有効化]** ボタンが追加されています。

    :::image type="content" source="./media/security-center-wdatp/deploy-to-linux.png" alt-text="このプレビューでは、Defender for Endpoint for Linux を展開するタイミングを手動で制御するボタンが導入されています":::

1. Linux マシンを統合に追加するには

    1. **[Linux マシン用の有効化]** を選択します。
    1. **[保存]** を選択します。
    1. 確認プロンプトで情報を確認し、続行する場合は **[有効にする]** を選択します。 

    :::image type="content" source="./media/security-center-wdatp/enable-for-linux-result.png" alt-text="Security Center と Microsoft の EDR ソリューション、Microsoft Defender for Endpoint for Linux 間の統合を確認する":::

    Azure Security Center によって、次のことが行われます。

    - Linux マシンを自動的に Defender for Endpoint にオンボーディングする
    - 他の fanotify ベースのソリューションを実行しているすべてのマシンを無視する ([Linux システム要件](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux#system-requirements)に必要な `fanotify` カーネル オプションの詳細を参照)
    - それまでのすべての Defender for Endpoint のインストールを検出し、それらを再構成して Security Center と統合する

    このオンボードには最大で 24 時間かかることがあります。

    > [!NOTE]
    > 次に Azure portal のこのページに戻ると、 **[Linux マシン用の有効化]** ボタンは表示されません。 Linux の統合を無効にするには、 **[Microsoft Defender for Endpoint によるデータへのアクセスを許可する]** チェック ボックスをオフにし、 **[保存]** を選択して、Windows でも無効にする必要があります。


1. Linux マシンへの Defender for Endpoint のインストールを確認するには、次のシェル コマンドをコンピューターで実行します。

    `mdatp health`

    Microsoft Defender for Endpoint がインストールされている場合は、その正常性状態が表示されます。

    `healthy : true`

    `licensed: true`

    また、Azure portal で、`MDE.Linux` という名前の新しい Azure 拡張機能がマシンに表示されます。

### <a name="new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows"></a>Microsoft Defender for Endpoint for Windows との統合を有効にしたことがない新規ユーザー

Windows の統合を有効にしたことがない場合は、 **[Microsoft Defender for Endpoint にデータへのアクセスを許可する]** オプションを選択すると、Security Center は Defender for Endpoint を Windows マシンと Linux マシンの"*両方*"にデプロイできます。

1. Security Center のメニューで、 **[価格と設定]** を選択し、Defender for Endpoint を受け取る Linux マシンでサブスクリプションを選択します。

1. **[Integrations]\(統合\)** を選択します。

1. **[データに Microsoft Defender for Endpoint がアクセスすることを許可する]** を選択し、 **[保存]** を選択します。

    Azure Security Center によって、次のことが行われます。

    - Windows および Linux マシンを自動的に Defender for Endpoint にオンボーディングする
    - 他の fanotify ベースのソリューションを実行しているすべての Linux マシンを無視する ([Linux システム要件](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux#system-requirements)に必要な `fanotify` カーネル オプションの詳細を参照)
    - それまでのすべての Defender for Endpoint のインストールを検出し、それらを再構成して Security Center と統合する

    このオンボードには最大で 24 時間かかることがあります。

1. Linux マシンへの Defender for Endpoint のインストールを確認するには、次のシェル コマンドをコンピューターで実行します。

    `mdatp health`

    Microsoft Defender for Endpoint がインストールされている場合は、その正常性状態が表示されます。

    `healthy : true`

    `licensed: true`

    さらに、Azure portal で、`MDE.Linux` という名前の新しい Azure 拡張機能がマシンに表示されます。
--- 

## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Microsoft Defender for Endpoint ポータルにアクセスする

1. ユーザー アカウントに必要なアクセス許可があることを確認します。 詳細については、「[Microsoft Defender セキュリティ センターにユーザー アクセスを割り当てる](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)」を参照してください。

1. 匿名トラフィックをブロックしているプロキシまたはファイアウォールがあるかどうかを確認します。 Defender for Endpoint センサーはシステム コンテキストから接続するため、匿名トラフィックを許可する必要があります。 Defender for Endpoint ポータルに制限なく確実にアクセスするには、[プロキシ サーバーでのサービス URL へのアクセスの有効化](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)に関する記事の説明に従ってください。

1. [Microsoft Defender セキュリティ センター ポータル](https://securitycenter.windows.com/)を開きます。 ポータルの機能とアイコンの詳細については、「[Microsoft Defender セキュリティ センター ポータルの概要](/windows/security/threat-protection/microsoft-defender-atp/portal-overview)」を参照してください。 






## <a name="send-a-test-alert"></a>テスト アラートを送信する

Defender for Endpoint から無害なテスト アラートを生成するには、エンドポイントの関連するオペレーティング システムのタブを選択します。

### <a name="windows"></a>[**Windows**](#tab/windows)

Windows を実行しているエンドポイント:

1. フォルダー「C:\test-MDATP-test」を作成します。
1. リモート デスクトップを使用してコンピューターにアクセスします。
1. コマンド ライン ウィンドウを開きます。
1. 次のコマンドをコピーし、プロンプトで実行します。 コマンド プロンプト ウィンドウは自動的に閉じます。

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="テスト アラートを生成するコマンドを含むコマンド プロンプト ウィンドウ。":::

    コマンドが成功した場合、Azure Security Center ダッシュボードと Microsoft Defender for Endpoint ポータルに新しいアラートが表示されます。 このアラートは、表示されるまでに数分かかることがあります。
1. Security Center 内でアラートを確認するには、 **[セキュリティ通知]**  >  **[Suspicious Powershell CommandLine]\(疑わしい Powershell コマンド ライン\)** に移動します。
1. 調査ウィンドウで、リンクを選択して Microsoft Defender for Endpoint ポータルに移動します。

    > [!TIP]
    > アラートは、 **[情報]** 重要度を使用してトリガーされます。


### <a name="linux"></a>[**Linux**](#tab/linux)

Linux を実行しているエンドポイント:

1. テスト アラート ツールを https://aka.ms/LinuxDIY からダウンロードします。
1. zip ファイルの内容を抽出し、次のシェル スクリプトを実行します。

    `./mde_linux_edr_diy`

    コマンドが成功した場合、Azure Security Center ダッシュボードと Microsoft Defender for Endpoint ポータルに新しいアラートが表示されます。 このアラートは、表示されるまでに数分かかることがあります。

1. Security Center 内でアラートを確認するには、 **[セキュリティのアラート]**  >  **[Enumeration of files with sensitive data]\(機密データを含むファイルの列挙\)** に移動します。
1. 調査ウィンドウで、リンクを選択して Microsoft Defender for Endpoint ポータルに移動します。

    > [!TIP]
    > アラートは **[低]** 重要度でトリガーされます。

--- 



## <a name="faq---security-centers-integration-with-microsoft-defender-for-endpoint"></a>FAQ - Security Center と Microsoft Defender for Endpoint の連携

- [私のマシンで "MDE.Windows" / "MDE.Linux" という拡張機能が動いていますが、これは何ですか。](#whats-this-mdewindows--mdelinux-extension-running-on-my-machine)
- [Microsoft Defender for Endpoint のライセンス要件はどのようなものですか?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Microsoft Defender for Endpoint のライセンスが既にある場合、Azure Defender の割引を受けることができますか?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [サードパーティ製の EDR ツールから切り替える方法は?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="whats-this-mdewindows--mdelinux-extension-running-on-my-machine"></a>私のマシンで "MDE.Windows" / "MDE.Linux" という拡張機能が動いていますが、これは何ですか。

以前、Microsoft Defender for Endpoint は Log Analytics エージェントにより提供されていました。 [サポートを拡張して Windows Server 2019](release-notes.md#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga) と Linux を含めるようにしたとき、当社は、自動でオンボードを実行する拡張機能も追加しました。 

Security Center では、次のものを実行しているマシンに対し、拡張機能を自動的にデプロイします。

- Windows Server 2019。
- Windows 10 Virtual Desktop (WVD)。
- OS のバージョンが Security Center で認識されない場合は、あらゆるバージョンの Windows Server (例えば、カスタム VM イメージを使用している場合)。 この場合、Microsoft Defender for Endpoint は、引き続き Log Analytics エージェントにより提供されます。
- Linux

> [!IMPORTANT]
> MDE.Windows 拡張機能を削除しても、Microsoft Defender for Endpoint は削除されません。 “オフボード” する方法は「[オフボード Windows サーバー](/microsoft-365/security/defender-endpoint/configure-server-endpoints)」をご覧ください。

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Microsoft Defender for Endpoint のライセンス要件はどのようなものですか?
Defender for Endpoint は、**Azure Defender for server** に追加料金なしで含まれています。 別の方法として、50 台以上のマシンに対して個別に購入することもできます。

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Microsoft Defender for Endpoint のライセンスが既にある場合、Azure Defender の割引を受けることができますか?
Microsoft Defender for Endpoint のライセンスを既に取得している場合は、Azure Defender ライセンスのその部分について料金を支払う必要はありません。

割引を要求するには、[Security Center のサポート チーム](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)にお問い合わせください。 関連するワークスペースの ID、リージョン、およびそのワークスペースのマシンに対して申請した Microsoft Defender for Endpoint ライセンスの数をお伝えください。

割引は承認日から適用されます。期間をさかのぼっては適用されません。

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>サードパーティ製の EDR ツールから切り替える方法は?
Microsoft 以外のエンドポイント ソリューションからの切り替えの詳しい手順については、Microsoft Defender for Endpoint の[移行の概要](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration)に関するドキュメントを参照してください。


## <a name="next-steps"></a>次のステップ

- [Azure Security Center でサポートされている機能とプラットフォーム](security-center-os-coverage.md)
- [推奨事項を Azure リソースの保護に活かす方法について説明します](security-center-recommendations.md)