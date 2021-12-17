---
title: Microsoft Defender for IoT の試用版のセットアップ
description: このチュートリアルでは、Microsoft Defender for IoT の試用版サブスクリプションを使用して、仮想マシン上で仮想センサーを Microsoft Defender for IoT にオンボードする方法について説明します。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 91e7d217861be41453669ea47525ed563200f54e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283640"
---
# <a name="tutorial-microsoft-defender-for-iot-trial-setup"></a>チュートリアル: Microsoft Defender for IoT の試用版のセットアップ

このチュートリアルでは、Microsoft Defender for IoT の試用版サブスクリプションを使用して、仮想マシン上で仮想センサーを Microsoft Defender for IoT にオンボードする方法について説明します。 このチュートリアルでは、サインアップして Microsoft Defender for IoT を環境に組み込む前にそれをテストするユーザーに最適なセットアップを示します。

センサーの作成に必要なソフトウェアと共に仮想環境を使用することで、Defender for IoT によって以下を行うことができます。

- パッシブでエージェントレスのネットワーク監視を使用して、IoT と OT ネットワークに影響を与えずに、すべての IoT と OT デバイスの完全なインベントリ、それらの詳細、それらがどのように通信するかを把握します。

- IoT と OT 環境のリスクと脆弱性を特定します。 たとえば、パッチ未適用のデバイス、開いているポート、未認可のアプリケーション、および未認可の接続を特定します。 デバイス構成、PLC コード、ファームウェアに対する変更を特定することもできます。

- 特殊な IoT と OT 対応の脅威インテリジェンスと行動分析によって、異常なアクティビティや未認可のアクティビティを検出します。 また、ゼロデイ マルウェア、ファイルレス マルウェア、現地調達型戦術など、静的な IOC では見逃されていた高度な脅威を検出することもできます。

- 組織全体の鳥瞰図を表示するために Microsoft Sentinel に統合します。 Splunk、IBM QRadar、ServiceNow などのサードパーティ製ツールを含む既存のワークフローと一体化した統合 IoT と OT セキュリティ ガバナンスを実装します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Microsoft Defender for IoT を使用してオンボードする
> * 仮想センサーの ISO をダウンロードする
> * センサー用の仮想マシンを作成する
> * 仮想センサー ソフトウェアをインストールする
> * SPAN ポートを構成する
> * 仮想センサーをオンボードしてアクティブ化する

## <a name="prerequisites"></a>前提条件

- アクセス許可: Azure **サブスクリプション所有者**、または **サブスクリプション共同作成者** レベル。

- スイッチの SPAN ポートに接続されている、少なくとも 1 つの監視対象のデバイス。

- VMware (ESXi 5.5 以降) または Hyper-V ハイパーバイザー (Windows 10 Pro または Enterprise) のいずれかがインストールされ、動作している。

- Azure アカウント。 Azure アカウントをまだお持ちではない場合は、[今すぐ Azure 無料アカウントを作成](https://azure.microsoft.com/free/)できます。

## <a name="onboard-with-microsoft-defender-for-iot"></a>Microsoft Defender for IoT を使用してオンボードする

Microsoft Defender for IoT の使用を開始するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションをお持ちでない場合は、[今すぐ Azure 無料アカウントを作成](https://azure.microsoft.com/free/)できます。

Defender for IoT を評価する場合は、試用版サブスクリプションをご利用いただけます。 試用版は 30 日間有効で、コミットされたデバイスを最大 1000 個サポートします。 試用版を使用すると、仮想センサーをネットワークにデプロイできます。 センサーを使用して、トラフィックの監視、データの分析、アラートの生成、ネットワークのリスクと脆弱性の把握などを行うことができます。 試用版では、仮想のオンプレミス管理コンソールをデプロイして、センサーによって生成された集計情報を表示することもできます。

**サブスクリプションを Microsoft Defender for IoT にオンボードするには**:

1. [Azure Portal](https://portal.azure.com/) に移動します。

1. **[Microsoft Defender for IoT]** を検索して選択します。

1. **[Onboard subscription]\(サブスクリプションのオンボード\)** を選択します。

    :::image type="content" source="media/tutorial-onboarding/onboard-subscription.png" alt-text="[作業の開始] ページから [Onboard subscription]\(サブスクリプションのオンボード\) ボタンを選択するスクリーンショット。":::

1. [Pricing]\(価格\) ページで、 **[Start with a Trial]\(試用版を開始\)** を選択します。

   :::image type="content" source="media/tutorial-onboarding/start-with-trial.png" alt-text="試用版のウィンドウを開くための [Start with a Trial]\(試用版を開始\) ボタンのスクリーンショット。":::

1. [Onboard trial subscription]\(試用版サブスクリプションをオンボードする\) ウィンドウからサブスクリプションを選択し、 **[Evaluate]\(評価\)** を選択します。

1. 評価を確認します。

## <a name="download-the-iso-for-the-virtual-sensor"></a>仮想センサーの ISO をダウンロードする

仮想アプライアンスには、センサーと管理コンソールの両方で必要な最小限の仕様があります。 次の表は、環境に応じてセンサーに必要となる仕様を示しています。

### <a name="virtual-sensor"></a>仮想センサー

| Type | 企業 | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| メモリ | 32 GB | 32 GB | 8 GB |
| ストレージ | 5.6 TB | 1.8 TB | 500 GB |

**仮想センサーの ISO ファイルをダウンロードするには**:

1. [Azure Portal](https://portal.azure.com/) に移動します。

1. **[Microsoft Defender for IoT]** を検索して選択します。

1. [作業の開始] ページで、 **[センサー]** タブを選択します。

1. **[ダウンロード]** を選択します。

    :::image type="content" source="media/tutorial-onboarding/sensor-download.png" alt-text="[センサー] タブのスクリーンショット。[ダウンロード] を選択して、仮想センサーの ISO ファイルをダウンロードします。":::

## <a name="create-a-virtual-machine-for-the-sensor"></a>センサー用の仮想マシンを作成する

仮想センサーでは、VMware と Hyper-V の両方のデプロイ オプションがサポートされます。 インストールを開始する前に、次の項目が満たされていることを確認してください。

- VMware (ESXi 5.5 以降) または Hyper-V ハイパーバイザー (Windows 10 Pro または Enterprise) がインストールされ、動作している。

- 仮想マシンに使用できるハードウェア リソース。

- Microsoft Defender for IoT センサーの ISO インストール ファイル。

- ハイパーバイザーが実行されていることを確認します。

### <a name="create-the-virtual-machine-for-the-sensor-with-esxi"></a>ESXi でセンサーの仮想マシンを作成する

**センサーの仮想マシンを作成するには (ESXi)** :

1. ESXi にサインインし、関連する **データストア** を選択して、 **[Datastore Browser]\(データストア ブラウザー\)** を選択します。

1. イメージを **アップロード** し、 **[閉じる]** を選択します。

1. **[仮想マシン]** に移動してから、 **[VM の作成/登録]** を選択します。

1. **[新しい仮想マシンの作成]** を選択してから、 **[次へ]** を選択します。

1. センサー名を追加し、次のものを選択します。

   - 互換性: **&lt;最新の ESXi バージョン&gt;**

   - ゲスト OS ファミリ:**Linux**

   - ゲスト OS バージョン:**Ubuntu Linux (64 ビット)**

1. **[次へ]** を選択します。

1. 関連するデータストアを選択し、 **[次へ]** を選択します。

1. 必要な[アーキテクチャ](#download-the-iso-for-the-virtual-sensor)に応じて、仮想ハードウェアのパラメーターを変更します。

1. **[CD/DVD Drive 1]\(CD/DVD ドライブ 1\)** については、 **[Datastore ISO file]\(データストア ISO ファイル\)** を選択し、前にアップロードした ISO ファイルを選択します。

1. **[次へ]**  >  **[完了]** の順に選択します。

1. VM の電源をオンにし、コンソールを開きます。

### <a name="create-a-virtual-machine-for-the-sensor-with-hyper-v"></a>Hyper-V でセンサーの仮想マシンを作成する

この手順では、Hyper-V を使用して仮想マシンを作成する方法について説明します。

**Hyper-V で仮想マシンを作成するには**:

1. Hyper-V マネージャーで仮想ディスクを作成します。

1. **[format = VHDX]\(形式 = VHDX\)** を選択します。

1. **[type = Dynamic Expanding]\(種類 = 容量可変\)** を選択します。

1. VHD の名前と場所を入力します。

1. ([アーキテクチャ](#download-the-iso-for-the-virtual-sensor)に応じて) 必要なサイズを入力します。

1. 概要を確認し、 **[完了]** を選択します。

1. **[アクション]** メニューで、新しい仮想マシンを作成します。

1. 仮想マシンの名前を入力します。

1. **[世代の指定]**  >  **[第 1 世代]** を選択します。

1. ([アーキテクチャに応じて](#download-the-iso-for-the-virtual-sensor)) メモリ割り当てを指定し、動的メモリのチェック ボックスをオンにします。

1. サーバーのネットワーク トポロジに応じて、ネットワーク アダプターを構成します。

1. 以前に作成した VHDX を仮想マシンに接続します。

1. 概要を確認し、 **[完了]** を選択します。

1. 新しい仮想マシンを右クリックし、 **[設定]** を選択します。

1. **[ハードウェアの追加]** 選択し、新しいネットワーク アダプターを追加します。

1. センサー管理ネットワークに接続する仮想スイッチを選択します。

1. ([アーキテクチャ](#download-the-iso-for-the-virtual-sensor)に応じて) CPU リソースを割り当てます。

1. 管理コンソールの ISO イメージを仮想 DVD ドライブに接続します。

1. 仮想マシンを開始します。

1. **[アクション]** メニューで、 **[接続]** を選択してソフトウェアのインストールを続行します。

## <a name="install-the-virtual-sensor-software-with-esxi-or-hyper-v"></a>ESXi または Hyper-V を使用して仮想センサーのソフトウェアをインストールする

ESXi または Hyper-V のいずれかを使用して、仮想センサーのソフトウェアをインストールできます。

**仮想センサーでソフトウェアをインストールするには**:

1. 仮想マシン コンソールを開きます

1. VM が ISO イメージから起動し、[言語の選択] 画面が表示されます。 **[English]\(英語\)** を選択します。

1. 必要な[アーキテクチャ](#download-the-iso-for-the-virtual-sensor)を選択します。

1. アプライアンス プロファイルとネットワークのプロパティを定義します。

    | パラメーター | 構成 |
    | ----------| ------------- |
    | **ハードウェア プロファイル** | 必要な[アーキテクチャ](#download-the-iso-for-the-virtual-sensor)に基づきます。 |
    | **管理インターフェイス** | **ens192** |
    | **ネットワーク パラメーター (お客様が指定)** | **管理ネットワークの IP アドレス:** <br/>**サブネット マスク:** <br>**アプライアンスのホスト名:** <br/>**DNS:** <br/>**既定のゲートウェイ:** <br/>**入力インターフェイス:**|
    | **ブリッジ インターフェイス:** | ブリッジ インターフェイスを構成する必要はありません。 このオプションは、特別なユース ケース専用です。 |

1. **Y** を入力して設定を受け入れます。

1. サインイン資格情報が自動的に生成され、表示されます。 ユーザー名とパスワードは、サインインとデバイスの管理に必要であるため、安全な場所にコピーしてください。 ユーザー名とパスワードは再度表示されません。

    - **Support**:ユーザー管理のための管理ユーザー。

    - **CyberX**:アプライアンスにアクセスするための root に相当します。

1. アプライアンスが再起動されます。

1. 以前に構成した IP アドレス (`https://ip_address`) を使用してセンサーにアクセスします。

### <a name="post-installation-validation"></a>インストール後の検証

物理アプライアンスのインストールを検証するには、多くのテストを実行する必要があります。

検証は、**Support** と **CyberX** の両方のユーザーが使用できます。

**検証後ツールにアクセスするには**:

1. センサーにサインインします。

1. 左側のペインから **[システム設定]** を選択します。

1. :::image type="icon" source="media/tutorial-onboarding/system-statistics-icon.png" border="false"::: ボタンを選択します。

    :::image type="content" source="media/tutorial-onboarding/system-health-check-screen.png" alt-text="システム正常性チェックのスクリーンショット。" lightbox="media/tutorial-onboarding/system-health-check-screen-expanded.png":::

インストール後検証では、テストを行って、システムが実行されていること、適切なバージョンを使用していることを確認し、インストール プロセス中に構成されたすべての入力インターフェイスが実行されていることを確認する必要があります。

**システムが実行されていることを確認するには**:

1. **[アプライアンス]** を選択し、それぞれの行項目に [`Running`] が表示され、末尾の行に [`System is up`] と示されていることを確認します。

1. **[バージョン]** を選択し、正しいバージョンが表示されていることを確認します。

1. **[Ifconfig]** を選択してアプライアンスの物理インターフェイスのパラメーターを表示し、それらが正しいことを確認します。

## <a name="configure-a-span-port"></a>SPAN ポートを構成する

仮想スイッチにはミラーリング機能はありません。 ただし、仮想スイッチ環境では無作為検出モードを使用できます。 無作為検出モードは、操作モードであるほか、セキュリティ、監視、および管理の手法でもあります。これは、仮想スイッチまたはポートグループ レベルで定義されています。 既定では、無作為検出モードは無効になっています。 無作為検出モードが有効になっている場合、その仮想スイッチを通過するすべてのネットワーク トラフィックを表示するために、同じポートグループにある仮想マシンのネットワーク インターフェイスによって無作為検出モードが使用されます。 この対処法は、ESXi または Hyper-V で実装できます。

:::image type="content" source="media/tutorial-onboarding/purdue-model.png" alt-text="アーキテクチャ内でセンサーを配置する場所を示すスクリーンショット。":::

### <a name="configure-a-span-port-with-esxi"></a>ESXi で SPAN ポートを構成する

**ESXi で SPAN ポートを構成するには**:

1. vSwitch のプロパティを開きます。

1. **[追加]** を選択します。

1. **[仮想マシン]**  >  **[次へ]** を選択します。

1. ネットワーク ラベル「**SPAN Network**」を挿入し、 **[VLAN ID]**  >  **[すべて]** を選択し、 **[次へ]** を選択します。

1. **[完了]** を選択します。

1. **[SPAN Network]** > * *[編集]* を選択します。

1. **[セキュリティ]** を選択し、 **[Promiscuous Mode]\(無作為検出モード\)** ポリシーが **[Accept]\(承認\)** モードに設定されていることを確認します。

1. **[OK]** を選択し、次に **[閉じる]** を選択して vSwitch のプロパティを閉じます。

1. **[XSense VM]** プロパティを開きます。

1. **[Network Adapter 2]\(ネットワーク アダプター 2\)** で **[SPAN]** ネットワークを選択します。

1. **[OK]** を選択します。

1. センサーに接続し、ミラーリングが動作していることを確認します。

### <a name="configure-a-span-port-with-hyper-v"></a>Hyper-V で SPAN ポートを構成する

開始する前に、以下を行う必要があります。

- 仮想アプライアンスのインスタンスが実行されていないことを確認します。

- 管理ポートではなく、データ ポートで SPAN が有効になっていることを確認します。

- データ ポート の SPAN 構成が IP アドレスを使用して構成されていないことを確認します。

**Hyper-V で SPAN ポートを構成するには**:

1. 仮想スイッチ マネージャーを開きます。

1. [仮想スイッチ] の一覧で、専用のスパン ネットワーク アダプターの種類として **[新しい仮想ネットワーク スイッチ]**  >  **[External]\(外付け\)** の順に選択します。

    :::image type="content" source="media/tutorial-onboarding/new-virtual-network.png" alt-text="仮想スイッチを作成する前に、[新しい仮想ネットワーク スイッチ] と [External]\(外付け\) を選択したスクリーンショット。":::

1. **[仮想スイッチの作成]** を選択します。

1. [接続の種類] で、 **[外部ネットワーク]** を選択します。

1. **[管理オペレーティング システムにこのネットワーク アダプターの共有を許可する]** のチェック ボックスがオンになっていることを確認します。

   :::image type="content" source="media/tutorial-onboarding/external-network.png" alt-text="[外部ネットワーク] と [管理オペレーティング システムにこのネットワーク アダプターの共有を許可する] を選択します。":::

1. **[OK]** を選択します。

#### <a name="attach-a-span-virtual-interface-to-the-virtual-switch"></a>SPAN 仮想インターフェイスを仮想スイッチに接続する

Windows PowerShell または Hyper-V マネージャーを使用して、SPAN 仮想インターフェイスを仮想スイッチに接続できます。

**PowerShell を使用して SPAN 仮想インターフェイスを仮想スイッチに接続するには**:

1. 新しく追加された SPAN 仮想スイッチを選択し、次のコマンドを使用して新しいネットワーク アダプターを追加します。

    ```bash
    ADD-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 -Name Monitor -SwitchName vSwitch_Span
    ```

1. 次のコマンドを使用して、選択したインターフェイスで、スパン先としてポート ミラーリングを有効にします。

    ```bash
    Get-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 | ? Name -eq Monitor | Set-VMNetworkAdapter -PortMirroring Destination
    ```

    | パラメーター | 説明 |
    |--|--|
    | VK-C1000V-LongRunning-650 | CPPM VA 名 |
    |vSwitch_Span |新しく追加された SPAN 仮想スイッチ名 |
    |Monitor |新しく追加されたアダプター名 |

1. **[OK]** を選択します。

これらのコマンドでは、新しく追加されたアダプター ハードウェアの名前が `Monitor` に設定されます。 Hyper-V マネージャーを使用する場合、新しく追加されたアダプター ハードウェアの名前は `Network Adapter` に設定されます。

**Hyper-V マネージャーを使用して SPAN 仮想インターフェイスを仮想スイッチに接続するには**:

1. [ハードウェア] の一覧で、 **[ネットワーク アダプター]** を選択します。

1. [仮想スイッチ] フィールドで、 **[vSwitch_Span]** を選択します。

    :::image type="content" source="media/tutorial-onboarding/vswitch-span.png" alt-text="仮想スイッチの画面で、それに続くオプションを選択するスクリーンショット。":::

1. [ハードウェア] の一覧の [ネットワーク アダプター] ドロップダウン リストで、 **[高度な機能]** を選択します。

1. [ポート ミラーリング] セクションで、新しい仮想インターフェイスのミラーリング モードとして **[Destination]\(保存先\)** を選択します。

    :::image type="content" source="media/tutorial-onboarding/destination.png" alt-text="ミラーリング モードの構成に必要な選択肢のスクリーンショット。":::

1. **[OK]** を選択します。

#### <a name="enable-microsoft-ndis-capture-extensions-for-the-virtual-switch"></a>仮想スイッチの Microsoft NDIS キャプチャ拡張機能を有効にする

新しい仮想スイッチに対して Microsoft NDIS キャプチャ拡張機能を有効にする必要があります。

**新しく追加された仮想スイッチに対して Microsoft NDIS キャプチャ拡張機能を有効にするには**:

1. Hyper-V ホストで仮想スイッチ マネージャーを開きます。

1. [仮想スイッチ] の一覧で、仮想スイッチ名 [`vSwitch_Span`] を展開し、 **[拡張機能]** を選択します。

1. [スイッチ拡張機能] フィールドで、 **[Microsoft NDIS キャプチャ]** を選択します。

    :::image type="content" source="media/tutorial-onboarding/microsoft-ndis.png" alt-text="[スイッチ拡張機能] メニューから選択して、Microsoft NDIS を有効にするスクリーンショット。":::

1. **[OK]** を選択します。

#### <a name="set-the-mirroring-mode-on-the-external-port"></a>外部ポートでミラーリング モードを設定する

ミラーリング モードは、ソースとなる新しい仮想スイッチの外部ポートで設定する必要があります。

外部ソース ポートに到着するすべてのトラフィックを、宛先として構成した仮想ネットワーク アダプターに転送するよう、Hyper-V 仮想スイッチ (vSwitch_Span) を構成する必要があります。

外部仮想スイッチ ポートをソース ミラー モードに設定するには、次の PowerShell コマンドを使用します。

```bash
$ExtPortFeature=Get-VMSystemSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings"
$ExtPortFeature.SettingData.MonitorMode=2
Add-VMSwitchExtensionPortFeature -ExternalPort -SwitchName vSwitch_Span -VMSwitchExtensionFeature $ExtPortFeature
```

| パラメーター | 説明 |
|--|--|
| vSwitch_Span | 新しく追加された SPAN 仮想スイッチ名。 |
| MonitorMode=2 | source |
| MonitorMode=1 | 宛先 |
| MonitorMode=0 | なし |

監視モードの状態を確認するには、次の PowerShell コマンドを使用します。

```bash
Get-VMSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings" -SwitchName vSwitch_Span -ExternalPort | select -ExpandProperty SettingData
```

| パラメーター | 説明 |
|--|--|
| vSwitch_Span | 新しく追加された SPAN 仮想スイッチ名 |
## <a name="onboard-and-activate-the-virtual-sensor"></a>仮想センサーをオンボードしてアクティブ化する

Azure Defender for IoT センサーを使い始める前に、作成した仮想センサーを Azure サブスクリプションにオンボードし、仮想センサーのアクティブ化ファイルをダウンロードしてセンサーをアクティブにする必要があります。

### <a name="onboard-the-virtual-sensor"></a>仮想センサーをオンボードする

**仮想センサーをオンボードするには**:

1. Azure portal で [Defender for IoT の概要](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)ページに移動します。

1. **[センサーのオンボード]** を選択します。

   :::image type="content" source="media/tutorial-onboarding/onboard-a-sensor.png" alt-text="センサーのオンボード プロセスを開始するためにセンサーをオンボードすることを選択したスクリーンショット。":::

1. センサーの名前を入力します。

   センサーの IP アドレスを名前の一部として含めるか、識別しやすい名前を使用することをお勧めします。 この方法でセンサーに名前を付けることで、追跡しやすくなります。

1. ドロップダウン メニューからサブスクリプションを選択します。

    :::image type="content" source="media/tutorial-onboarding/name-subscription.png" alt-text="わかりやすい名前を入力し、センサーをサブスクリプションに接続するスクリーンショット。":::

1. **[クラウド接続]** トグルを使用してセンサーの接続モードを選択します。 このトグルがオンの場合、センサーはクラウド接続になります。 このトグルがオフの場合、センサーはローカル管理になります。

   - **クラウド接続のセンサー**:センサーによって検出された情報は、センサー コンソールに表示されます。 アラート情報は IoT ハブ経由で配信され、Microsoft Sentinel など、他の Azure サービスと共有できます。 また、脅威インテリジェンス パッケージを Defender for IoT からセンサーにプッシュできます。 逆に、センサーがクラウドに接続されていない場合は、脅威インテリジェンス パッケージをダウンロードしてから、エンタープライズ センサーにアップロードする必要があります。 Defender for IoT からセンサーにパッケージをプッシュできるようにするには、 **[脅威インテリジェンスの自動更新]** トグルをオンにします。 詳細については、「[脅威インテリジェンスの調査とパッケージ](how-to-work-with-threat-intelligence-packages.md)」を参照してください。

      クラウド接続のセンサーの場合、オンボード時に定義された名前が、センサー コンソールに表示される名前です。 この名前は、コンソールから直接変更することはできません。 ローカル管理センサーの場合、オンボード時に指定された名前は、Azure に保存されますが、センサー コンソールで更新することができます。

   - **ローカル管理センサー**:センサーによって検出された情報は、センサー コンソールに表示されます。 エアギャップ ネットワークで作業していて、複数のローカル管理センサーによって検出されるすべての情報を一元的に表示したい場合は、オンプレミスの管理コンソールを使用します。

1. センサーを関連付けるサイトを IoT Hub 内で選択します。 IoT Hub は、このセンサーと Microsoft Defender for IoT の間のゲートウェイとして機能します。 表示名とゾーンを定義します。 説明タグを追加することもできます。 表示名、ゾーン、タグは、「[オンボードされたセンサーを表示する](how-to-manage-sensors-on-the-cloud.md#view-onboarded-sensors)」にある説明用のエントリです。

1. **[登録]** を選択します。

### <a name="download-the-sensor-activation-file"></a>センサー アクティブ化ファイルをダウンロードする

センサーの登録が完了すると、センサーのアクティブ化ファイルをダウンロードできるようになります。 センサー アクティブ化ファイルには、センサーの管理モードに関する説明が含まれています。 ダウンロードするアクティブ化ファイルは、デプロイするセンサーごとに一意のものになります。 センサー コンソールに初めてサインインするユーザーは、アクティブ化ファイルをセンサーにアップロードします。

**アクティブ化ファイルをダウンロードするには:**

1. **[センサーのオンボード]** ページで、 **[登録]** を選択します。

1. **[アクティブ化ファイルのダウンロード]** を選択します。

1. 初めてセンサー コンソールにサインインしようとしているユーザーが、ファイルにアクセスできるようにします。

### <a name="sign-in-and-activate-the-sensor"></a>サインインしてセンサーをアクティブ化する

**サインインしてアクティブ化するには、次のようにします。**

1. インストール中に定義された IP を使用して、ブラウザーからセンサー コンソールにアクセスします。

    :::image type="content" source="media/tutorial-onboarding/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Microsoft Defender for IoT センサーのスクリーンショット。":::

1. センサーのインストール中に定義された資格情報を入力します。

1. サインインすると、 **[Activation]\(アクティブ化\)** ダイアログ ボックスが開きます。 **[アップロード]** を選択し、センサーのオンボード中にダウンロードしたアクティブ化ファイルにアクセスします。

   :::image type="content" source="media/tutorial-onboarding/activation-upload-screen-with-upload-button.png" alt-text="アクティブ化ファイルをアップロードして、それにアクセスすることを選択したスクリーンショット。":::

1. 使用条件に同意します。

1. **[アクティブ化]** を選びます。 SSL/TLS 証明書のダイアログ ボックスが開きます。

1. 証明書名を定義します。

1. CRT およびキーのファイルをアップロードします。

1. パスフレーズを入力し、必要に応じて PEM ファイルをアップロードします。

1. **[次へ]** を選択します。 検証画面が開きます。 既定で、管理コンソールと接続されたセンサー間の検証が有効になっています。

1. 検証を無効にするには、 **[システム全体の検証を有効にする]** トグルをオフにします。 検証を有効にすることをお勧めします。

1. **[保存]** を選択します。  

CA 署名証明書のアップロード後に、画面を更新する必要がある場合があります。

## <a name="next-steps"></a>次のステップ

[その他のアプライアンス](how-to-install-software.md#about-defender-for-iot-appliances)の設定方法を学びます。

[エージェントレス アーキテクチャ](architecture.md)について確認します。
