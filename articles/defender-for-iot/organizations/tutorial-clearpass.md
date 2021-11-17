---
title: ClearPass を Microsoft Defender for IoT と統合する
description: このチュートリアルでは、Microsoft Defender for IoT と ClearPass を統合する方法について説明します。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 536e45171d8769980080d81c59c9f07192addab0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343631"
---
# <a name="tutorial-integrate-clearpass-with-microsoft-defender-for-iot"></a>チュートリアル: ClearPass を Microsoft Defender for IoT と統合する

このチュートリアルは、ClearPass Policy Manager (CPPM) を Microsoft Defender for IoT と統合する方法を学ぶのに役立ちます。

Defender for IoT プラットフォームでは、継続的な ICS 脅威の監視とデバイス検出が提供され、産業用プロトコル、デバイス、アプリケーションに関する深い理解と、ICS 固有の動作異常検出、脅威インテリジェンス、リスク分析、自動化された脅威モデリングが組み合わされています。

Defender for IoT では、OT および ICS エンドポイントが検出および分類され、ClearPass Security Exchange フレームワークとオープン API を使用して ClearPass と直接情報が共有されます。

Defender for IoT では、エンドポイント分類データといくつかのカスタム セキュリティ属性によって、ClearPass Policy Manager エンドポイント データベースが自動的に更新されます。

この統合により、次のことが可能になります。

- Defender for IoT セキュリティ エンジンによって識別された ICS および SCADA セキュリティ脅威の表示。

- Defender for IoT センサーによって検出されたデバイス インベントリ情報の表示。 センサーによって、IT および OT インフラストラクチャ全体で、すべてのネットワーク デバイスとエンドポイントの一元的な可視性が提供されます。 ここから、一元化されたエンドポイントとエッジ セキュリティ ポリシーを ClearPass システムに定義して管理できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> - ClearPass API ユーザーを作成する
> - ClearPass オペレーター プロファイルを作成する
> - ClearPass OAuth API クライアントを作成する
> - ClearPass と統合するように Defender for IoT を構成する
> - ClearPass 転送規則を定義する
> - ClearPass と Defender for IoT 通信を監視する

## <a name="prerequisites"></a>前提条件

### <a name="aruba-clearpass-requirements"></a>Aruba ClearPass の要件

CPPM は、ソフトウェアがプレインストールされているハードウェア アプライアンスで、または次のハイパーバイザー下の仮想マシンとして実行されます。 VMware Player などのクライアント コンピューターで実行されるハイパーバイザーはサポートされていません。

- VMware ESXi 5.5、6.0、6.5、6.6 以上。

- Microsoft Hyper-V Server 2012 R2 または 2016 R2。

- Microsoft Windows Server 2012 R2 または 2016 R2 上の Hyper-V。

- CentOS 7.5 以降での KVM。

### <a name="defender-for-iot-requirements"></a>Defender for IoT の要件

- Defender for IoT バージョン 2.5.1 以上。

- Azure アカウント。 Azure アカウントをまだお持ちではない場合は、[今すぐ Azure 無料アカウントを作成](https://azure.microsoft.com/free/)できます。

## <a name="create-a-clearpass-api-user"></a>ClearPass API ユーザーを作成する

2 つの製品間の通信チャネルの一部として、Defender for IoT では多くの API (TIPS と REST の両方) が使用されます。 TIPS API へのアクセスは、ユーザー名とパスワードの組み合わせの資格情報によって検証されます。 このユーザー ID には、最小レベルのアクセス権が必要です。 スーパー管理者プロファイルは使用せず、次に示すような API 管理者を使用してください。

**ClearPass API ユーザーを作成するには**:

1. 左側のウィンドウで、 **[管理]** 、 >  **[ユーザーと権限]** の順に選択し、 **[追加]** を選択します。

1. **[管理者ユーザーの追加]** ダイアログ ボックスで、次のパラメーターを設定します。

    :::image type="content" source="media/tutorial-clearpass/policy-manager.png" alt-text="管理者ユーザーのダイアログ ボックス ビューのスクリーンショット。":::

    | パラメーター | 説明 |
    |--|--|
    | **UserID** | ユーザー ID を入力します。 |
    | **名前** | ユーザー名を入力します。 |
    | **パスワード** | パスワードを入力します。 |
    | **ユーザーの有効化** | このオプションが有効になっていることを確認します。 |
    | **特権レベル** | **[API 管理者]** を選択します。 |

1. **[追加]** を選択します。

## <a name="create-a-clearpass-operator-profile"></a>ClearPass オペレーター プロファイルを作成する

Defender for IoT では、統合の一部として、REST API が使用されます。 REST API は OAuth フレームワークで認証されます。 Defender for IoT と同期するには、API クライアントを作成する必要があります。

API クライアントの REST API へのアクセスをセキュリティで保護するには、制限付きアクセス オペレーター プロファイルを作成します。

**ClearPass オペレーター プロファイルを作成するには**:

1. **[オペレーター プロファイルの編集]** ウィンドウに移動 します。

1. 次を除き、すべてのオプションを **[アクセス権なし]** に設定します。

| パラメーター | 説明 |
|--|--|
| **API サービス** | **[アクセスを許可]** に設定します |
| **ポリシー マネージャー** | 次のように設定します。 <br />- **Dictionaries**: **Attributes** を **Read, Write, Delete** に設定<br />- **Dictionaries**: **Fingerprintsset** を **Read, Write, Delete** に設定<br />- **Identity**: **Endpoints** を **Read, Write, Delete** に設定 |

:::image type="content" source="media/tutorial-clearpass/api-profile.png" alt-text="オペレーター プロファイルの編集のスクリーンショット。":::

:::image type="content" source="media/tutorial-clearpass/policy.png" alt-text="[ポリシー マネージャー] 画面のオプションのスクリーンショット。":::

## <a name="create-a-clearpass-oauth-api-client"></a>ClearPass OAuth API クライアントを作成する

1. メイン ウィンドウで、 **[管理者]**  >  **[API サービス]**  >  **[API クライアント]** の順に選択します。

1. [API クライアントの作成] タブで、次のパラメーターを設定します。

    - **動作モード**: このパラメーターは、ClearPass への API 呼び出しに使用されます。 **[ClearPass REST API - クライアント]** を選択します。

    - **オペレーター プロファイル**: 前に作成したプロファイルを使用します。

    - **付与タイプ**: **クライアント資格情報 (grant_type = client_credentials)** を設定します。

1. **クライアント シークレット** とクライアント ID を記録してください。 たとえば、「 `defender-rest` 」のように入力します。

    :::image type="content" source="media/tutorial-clearpass/aruba.png" alt-text="API クライアントの作成のスクリーンショット。":::

1. ポリシー マネージャーで、次の手順に進む前に、次の情報の一覧を収集していることを確認してください。

    - CPPM UserID

    - CPPM UserId パスワード

    - C PPM OAuth2 API クライアント ID

    - C PPM OAuth2 API クライアント シークレット

## <a name="configure-defender-for-iot-to-integrate-with-clearpass"></a>ClearPass と統合するように Defender for IoT を構成する

ClearPass でデバイス インベントリを表示できるようにするには、Defender for IoT-ClearPass の同期を設定する必要があります。同期構成が完了すると、Defender for IoT プラットフォームによって、新しいエンドポイントが検出されたときに、ClearPass Policy Manager EndpointDb が更新されます。

**Defender for IoT センサーで ClearPass 同期を構成するには**:

1. Defender for IoT センサーで、左側のパネルから **[システム設定]** を選択します。

1. **[システム設定]** ウィンドウで、:::image type="content" source="media/tutorial-clearpass/clearpass-icon.png" alt-text="左側の ClearPass アイコンのスクリーンショット":::を選択します。

1. 次のパラメーターを設定します。

    :::image type="content" source="media/tutorial-clearpass/settings.png" alt-text="[システム設定] ウィンドウに必要な情報を入力しているスクリーンショット。":::

    - **同期を有効にする**: Defender for IoT と ClearPass の間の同期を有効にします

    - **同期頻度:** 同期頻度を分で定義します。 既定値は 60 分です。 最小値は 5 分です。

    - **ClearPass IP アドレス:** Defender for IoT が同期されている ClearPass システムの IP アドレス。

    - **クライアント ID:** Defender for IoT とデータを同期するために ClearPass で作成されたクライアント ID。

    - **クライアント シークレット:** Defender for IoT とデータを同期するために ClearPass で作成されたクライアント シークレット。

    - **ユーザー名:** ClearPass 管理者ユーザー。

    - **パスワード:** ClearPass 管理者パスワード。

1. **[保存]** を選択します。

## <a name="define-a-clearpass-forwarding-rule"></a>ClearPass 転送規則を定義する

Defender for IoT によって検出されたアラートを Aruba で表示できるようにするには、転送規則を設定する必要があります。 このルールでは、ClearPass に送信される、Defender for IoT セキュリティ エンジンによって識別される ICS および SCADA セキュリティ脅威に関する情報を定義します。

**Defender for IoT センサーで ClearPass 転送規則を定義するには**:

1. Defender for IoT センサーで、左側のウィンドウから、 **[転送]** を選択します。

1. **[転送]** ウィンドウで、 **[転送ルールの作成]** を選択します。

    :::image type="content" source="media/tutorial-clearpass/forwarding.png" alt-text="そのすべてのオプションを含む [転送] ウィンドウのスクリーンショット。":::

1. 名前と規則の重大度を追加し、次に、 **[アクション]** ドロップダウン リストから **[送信先]**  >  **[ClearPass]** を選択します。

    :::image type="content" source="media/tutorial-clearpass/rule.png" alt-text="転送規則の作成のスクリーンショット。":::

1. **[アクション]** ペインで、次のパラメーターを設定します。

    :::image type="content" source="media/tutorial-clearpass/actions.png" alt-text="[アクション] ウィンドウからアクションを選択します。":::

    | パラメーター | 説明 |
    |--|--|
    | **Host** | ClearPass サーバーの IP アドレスを入力します。 |
    | **ポート** | 転送が行われる ClearPass のポートを入力します。 |
    | **構成** | ClearPass システムで Defender for IoT アラートを表示させるには、次のオプションを設定します。 <br />- **無効な関数コードを報告する:** プロトコル違反 - ICS プロトコル仕様に違反している無効なフィールド値 (潜在的な悪用)。<br />- **不正な PLC プログラミング/ファームウェア更新を報告する:** 認可されていない PLC の変更。<br />- **認可されていない PLC 停止を報告する:** PLC の停止 (ダウンタイム)。<br />- **マルウェア関連のアラートを報告する:** 産業用マルウェアの試みのブロック (TRITON、NotPetya など)。<br />- **認可されていないスキャンを報告する**: 認可されていないスキャン (潜在的な偵察)。 |

1. <bpt id="p1">**</bpt>[Submit]\(送信\)<ept id="p1">**</ept> をクリックします。

## <a name="monitor-clearpass-and-defender-for-iot-communication"></a>ClearPass と Defender for IoT 通信を監視する

同期が開始されると、エンドポイント データが Policy Manager EndpointDb に直接設定され、統合構成画面から最終更新時刻を表示できます。

**ClearPass への最終同期時刻を確認するには**:

1. Defender for IoT センサーにサインインします。

1. 左側のパネルから **[システム設定]** を選択します。

1. **[ClearPass]** を選択します。

    :::image type="content" source="media/tutorial-clearpass/last-sync.png" alt-text="前回の同期の時刻と日付を表示するスクリーンショット。":::

同期が動作していない場合、またはエラーが表示される場合は、情報の一部をキャプチャしていない可能性があります。 記録されたデータを再確認し、さらに、 **[ゲスト]**  >  **[管理]**  >  **[サポート]**  >  **[アプリケーション ログ]** から Defender for IoT と ClearPass の間の API 呼び出しを表示できます。

Defender for IoT と ClearPass の間の API ログの例を次に示します。

:::image type="content" source="media/tutorial-clearpass/log.png" alt-text="Defender for IoT と ClearPass の間の API ログのスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップするリソースがありません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ClearPass 統合の使用を開始する方法を学習しました。 CyberArk の学習に進みます。

> [!div class="nextstepaction"]
> [次のステップのボタン](./tutorial-cyberark.md)
