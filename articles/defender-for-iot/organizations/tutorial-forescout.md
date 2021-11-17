---
title: Forescout を Microsoft Defender for IoT と統合する
description: このチュートリアルでは、Microsoft Defender for IoT と Forescout を統合する方法について説明します。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: c395c799b8f6dca602b20cb330f89630f9fb591e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325238"
---
# <a name="tutorial-integrate-forescout-with-microsoft-defender-for-iot"></a>チュートリアル: Forescout を Microsoft Defender for IoT と統合する

> [!Note]
> CyberX について記載があった場合、これは Microsoft Defender for IoT を指しています。

このチュートリアルは、Forescout と Microsoft Defender for IoT を統合する方法について学ぶのに役立ちます。

Microsoft Defender for IoT は、ICS および IoT サイバーセキュリティ プラットフォームを提供します。 Defender for IoT は、ICS 対応の脅威分析と機械学習を搭載した唯一のプラットフォームです。 Defender for IoT は、以下を提供します。

- 属性に関するさまざまな詳細情報を含む、デバイス ランドスケープの ICS に関する分析情報を即座に提供。

- ICS に対応した、OT プロトコル、デバイス、アプリケーション、およびそれらの動作に関する組み込まれた深い知識。

- 脆弱性および既知のゼロデイ脅威に対する即座の分析情報。

- 独自の分析により、標的型 ICS 攻撃の最も可能性の高い経路を予測する自動 ICS 脅威モデル化テクノロジ。

Forescout の統合により、産業および重要インフラストラクチャの組織がサイバー脅威を検出、調査、対処するのに必要な時間を短縮することができます。

- Forescout ポリシー アクションをトリガーすることによって、Microsoft Defender for IoT OT デバイス インテリジェンスを使用してセキュリティ サイクルを閉じます。 たとえば、特定のプロトコルが検出された場合、またはファームウェアの詳細が変更された場合に、SOC 管理者にアラートの電子メールを自動的に送信できます。

- 監視、インシデント管理、デバイス制御を監視する他の *Forescout eyeExtended* モジュールと Defender for IoT の情報を関連付けることができます。

Defender for IoT と Forescout プラットフォームを統合することで、IoT と OT のランドスケープで、一元的な可視性、監視、制御を実現することができます。 このようにブリッジされたプラットフォームを使用することで、ICS デバイスやサイロ化されたワークフローに対する、自動でのデバイスの可視化と管理が可能になります。 この統合により、SOC アナリストは、産業環境に展開されている OT プロトコルを、複数レベルで可視化することができます。 ファームウェア、デバイスの種類、オペレーティング システム、および独自の Microsoft Defender for IoT テクノロジに基づくリスク分析スコアなどの項目に関する情報を確認できるようになります。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> - アクセス トークンを生成する
> - Forescout プラットフォームを構成する
> - 通信を検証する
> - Forescout でデバイス属性を表示する
> - Forescout で Microsoft Defender for IoT ポリシーを作成する

Azure アカウントをまだお持ちではない場合は、[今すぐ Azure 無料アカウントを作成](https://azure.microsoft.com/free/)できます。

## <a name="prerequisites"></a>前提条件

- Microsoft Defender for IoT バージョン 2.4 以降

- Forescout バージョン 8.0 以降

- Microsoft Defender for IoT プラットフォーム用の Forescout eyeExtend モジュールのライセンス。

## <a name="generate-an-access-token"></a>アクセス トークンを生成する

アクセス トークンにより、Defender for IoT によって検出されたデータに外部システムがアクセスできます。 アクセストークンにより、そのデータを外部の REST API に使用し、また SSL 接続経由で使用できます。 アクセス トークンは、Microsoft Defender for IoT REST API にアクセスするために生成することができます。

Defender for IoT から Forescout への通信を確保するには、Defender for IoT でアクセス トークンを生成する必要があります。

**アクセス トークンを生成するには**:

1. Forescout からクエリされる Defender for IoT センサーにサインインします。

1. **[全般]** セクションから **[システム設定]**  >  **[アクセス トークン]** を選択します。

1. **[新しいトークンの生成]** を選択します。

    :::image type="content" source="media/tutorial-forescout/generate-access-tokens-screen.png" alt-text="アクセス トークン生成画面のスクリーンショット。":::

1. **[新しいアクセス トークン]** ダイアログ ボックスに、トークンの説明を入力します。

   :::image type="content" source="media/tutorial-forescout/new-forescout-token.png" alt-text="新しいアクセス トークン":::

1. **[次へ]** を選択します。 これでダイアログ ボックスにトークンが表示されます。

   > [!NOTE]
   > トークンは安全な場所に記録します。 これは、Forescout プラットフォームを構成するときに必要になります。

1. **[完了]** を選択します。

   :::image type="content" source="media/tutorial-forescout/forescout-access-token-added-successfully.png" alt-text="トークンの追加を完了する":::

## <a name="configure-the-forescout-platform"></a>Forescout プラットフォームを構成する

Defender for IoT センサーと通信するように Forescout プラットフォームを構成できます。

**Forescout プラットフォームを構成するには**:

1. Forescout プラットフォームで **CyberX 用 Forescout eyeExtend モジュール** を検索し、インストールします。

1. CounterACT コンソールにサインインします。

1. [ツール] メニューの **[オプション]** をクリックします。

1. **[Modules]\(モジュール\)**  >  **[CyberX Platform]\(CyberX プラットフォーム\)** に移動します。

   :::image type="content" source="media/tutorial-forescout/settings-for-module.png" alt-text="Microsoft Defender for IoT モジュールの設定":::

1. [Server Address]\(サーバー アドレス\) フィールドに、Forescout アプライアンスによってクエリされる、Defender for IoT センサーの IP アドレスを入力します。

1. [Access Token]\(アクセス トークン\) フィールドに、先ほど生成したアクセス トークンを入力します。

1. **[適用]** を選択します。

### <a name="change-sensors-in-forescout"></a>Forescout のセンサーを変更する

Forescout プラットフォームと別のセンサーが通信するためには、Forescout 内の構成を変更する必要があります。

**Forescout のセンサーを変更するには**:

1. 該当する Defender for IoT センサーに新しいアクセス トークンを作成します。

1. **[Forescout Modules]\(Forescout モジュール\)**  >  **[CyberX Platform]\(CyberX プラットフォーム\)** に移動します。

1. 両方のフィールドに表示されている情報を削除します。

1. 新しい Defender for IoT センサーにサインインし、[新しいアクセス トークンを生成](#generate-an-access-token)します。

1. [Server Address]\(サーバー アドレス\) フィールドに、Forescout アプライアンスによってクエリされる、Defender for IoT センサーの新しい IP アドレスを入力します。

1. [Access Token]\(アクセス トークン\) フィールドに、新しいアクセス トークンを入力します。

1. **[適用]** を選択します。

## <a name="verify-communication"></a>通信を検証する

接続が構成されたら、2 つのプラットフォームが通信していることを確認する必要があります。

**2 つのプラットフォームが通信中であることを確認するには**:

1. Defender for IoT センサーにサインインします。

1. **[システム設定]**  >  **[アクセス トークン]** に移動します。

センサーと Forescout アプライアンス間の接続が機能していない場合には、[使用済み]\(Used\) フィールドに警告が表示されます。 **[N/A]** が表示されている場合は、接続が機能していません。 **[Used]\(使用済み\)** が表示されている場合は、このトークンを持つ外部呼び出しが最後に受信された日時を示します。

:::image type="content" source="media/tutorial-forescout/forescout-access-token-added-successfully.png" alt-text="トークンが受信されたことを確認する":::

## <a name="view-device-attributes-in-forescout"></a>Forescout でデバイス属性を表示する

Defender for IoT と Forescout を統合することで、Defender for IoT によって検出されたさまざまなデバイスの属性を Forescout アプリケーションで表示できます。

次の表に、Forescout アプリケーションで表示できるすべての属性を示します。

| 項目 | 説明 |
|--|--|
| **Microsoft Defender for IoT によって承認** | ネットワークの学習期間中に、Defender for IoT によってネットワーク上で検出されたデバイス。 |
| **ファームウェア** | デバイスのファームウェアの詳細。 たとえば、モデルやバージョンの詳細などです。 |
| **名前** | デバイスの名前。 |
| **オペレーティング システム** | デバイスのオペレーティング システム。 |
| **Type** | デバイスの種類。 たとえば、PLC、Historian (ヒストリアン)、Engineering Station (エンジニアリング ステーション) などです。 |
| **ベンダー名** | デバイスのベンダー。 たとえば、Rockwell Automation などです。 |
| **リスク レベル** | Defender for IoT によって計算されたリスク レベル。 |
| **プロトコル** | デバイスによって生成されたトラフィックで検出されたプロトコル。 |

**デバイスの属性を表示するには**:

1. Forescout プラットフォームにサインインし、 **[Asset Inventory]\(アセット インベントリ\)** に移動します。

   :::image type="content" source="media/tutorial-forescout/device-firmware-attributes-in-forescout.png" alt-text="ファームウェアの属性を表示します。":::

1. **[CyberX Platform]\(CyberX プラットフォーム\)** を選択します。

   :::image type="content" source="media/tutorial-forescout/vendor-attributes-in-forescout.png" alt-text="ベンダーの属性を表示します。":::

### <a name="view-more-details"></a>詳細を表示

デバイスの属性を表示すると、Forescout コンプライアンスやポリシー情報など、各デバイスの詳細情報を確認できます。

**追加情報を表示するには**:

1. Forescout プラットフォームにサインインし、 **[Asset Inventory]\(アセット インベントリ\)** に移動します。

1. **[CyberX Platform]\(CyberX プラットフォーム\)** を選択します。

1. [Device Inventory Hosts]\(デバイス インベントリ ホスト\) セクションからデバイスを右クリックします。 その他の情報が表示されている [Host Details]\(ホストの詳細\) ダイアログ ボックスが開きます。

## <a name="create-microsoft-defender-for-iot-policies-in-forescout"></a>Forescout で Microsoft Defender for IoT ポリシーを作成する

Forescout ポリシーを使用して、Defender for IoT によって検出されたデバイスの制御と管理を自動化することができます。 たとえば、次のように入力します。

- 特定のファームウェア バージョンが検出されたときに SOC 管理者に自動的に電子メールを送信します。

- 特定の Defender for IoT 検出デバイスを Forescout グループに追加して、他の SIEM 統合などのインシデントおよびセキュリティ ワークフローでさらに処理できるようにします。

Defender for IoT の条件プロパティを使用して、Forescout でカスタム ポリシーを作成できます。

**Defender for IoT のプロパティにアクセスするには**:

1. **[Policy Conditions]\(ポリシー条件\)**  >  **[Properties Tree]\(プロパティ ツリー\)** に移動します。

1. [Properties Tree]\(プロパティ ツリー\) の [CyberX Platform]\(CyberX プラットフォーム\) フォルダーを展開します。 次の Defender for IoT のプロパティを使用できます。

:::image type="content" source="media/tutorial-forescout/forescout-property-tree.png" alt-text="Properties":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップするリソースがありません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Forescout 統合の使用を開始する方法を学習しました。 次に、[Palo Alto の統合](./tutorial-palo-alto.md)について学習します。

> [!div class="nextstepaction"]
> [次のステップのボタン](./tutorial-palo-alto.md)
