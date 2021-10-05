---
title: Microsoft AppSource (Azure Marketplace) での Dynamics 365 Operations オファーの機能検証
description: Microsoft AppSource (Azure Marketplace) での Dynamics 365 Operations オファーの機能検証。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: vamahtan
ms.author: vamahtan
ms.date: 09/27/2021
ms.openlocfilehash: d2e6316485b1ad3676092e6e19dcc4c6b55d5d2b
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080896"
---
# <a name="dynamics-365-for-operations-functional-validation"></a>Dynamics 365 for Operations の機能検証

[パートナー センター](https://go.microsoft.com/fwlink/?linkid=2166002)で Dynamics 365 for Operations オファーを公開するには、次の 2 つの機能検証が必要です。

- 基本的な機能を示す Dynamics 365 環境のデモ ビデオをアップロードします。
- ソリューションの [Lifecycle Services](https://lcs.dynamics.com/) (LCS) 環境を示すスクリーンショットを提出します。

> [!NOTE]
> 以降の再認証の発行では、デモは必要ありません。 詳細については、[AppSource Policy のドキュメント](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops)を参照してください。

## <a name="how-to-validate"></a>検証方法

機能検証には、次の 2 つのオプションがあります。

- 太平洋標準時 (PST) の営業時間中に 30 分間の電話会議を開催して、[LCS](https://lcs.dynamics.com/) 環境とソリューションのデモンストレーションと記録を行います。
- パートナー センターで、[[コマーシャル マーケットプレース]](https://go.microsoft.com/fwlink/?linkid=2165290) にアクセスし、オファーの [Supplemental Content]\(補足コンテンツ\) タブでデモ ビデオ (URL から) と LCS のスクリーンショットをアップロードします。

Microsoft 認定チームはビデオとファイルをレビューしてから、ソリューションを承認するか、次の手順について電子メールで通知します。

> [!NOTE]
> 作成しているソリューションおよびオファーがコネクタのみである場合は、呼び出し中に動作しているコネクタをデモするか、以下の一覧にあるビデオ アップロード オプションのいずれかを使用します。  

### <a name="option-1-30-minute-conference-call"></a>オプション 1: 30 分間の電話会議

最終レビューの電話会議をスケジュールするには、[appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com) にお問い合わせください。プランの名前と午前 8 時から午後 5 時 (太平洋標準時) の間のご希望の時間帯を記載してください。

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>オプション 2:デモ ビデオと LCS スクリーンショットをアップロードする

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. ビデオを記録し、選択したホスティング サイトにそのアドレスをアップロードします。 次のガイドラインに従ってください。

    - Microsoft 認定チームが参照可能であること。
    - 20 分未満。
    - Dynamics 365 環境内のソリューションの最大 3 つのコア機能のハイライトが含まれていること。

    > [!NOTE]
    > ガイドラインを満たしている場合は、既存のマーケティング ビデオを使用することができます。

2. 発行するプランまたはソリューションに一致する [LCS](https://lcs.dynamics.com/) 環境の次のスクリーンショットを取得します。 これらのスクリーンショットは十分に明瞭で、認証チームがテキストを読み取れる必要があります。 スクリーンショットを JPG ファイルとして保存します。 スクリーンショットを提供する代わりに、[appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com) に LCS 環境へのアクセス許可を提供して、弊社がセットアップを確認できるようにすることができます。

    1. **[LCS]**  >  **[ビジネス プロセス モデラー]**  >  **[プロジェクト ライブラリ]** に移動します。 すべての [プロセス] のステップのスクリーンショットを取得します。 次に示すように、 **[ダイアグラム]** 列と **[確認済]** 列を含めます。

       :::image type="content" source="media/dynamics-365-operations/project-library.png" alt-text="プロジェクト ライブラリ ウィンドウを表示します。":::

    2. **[LCS]**  >  **[ソリューション管理]**  >  **[ソリューション パッケージのテスト]** に移動します。 次の例に示すように、パッケージの概要と内容を含むスクリーンショットを取得します。

    | フィールド | Image |
    | --- | --- |
    | パッケージの概要 | [![[パッケージの概要] ウィンドウを示すスクリーンショット。](media/dynamics-365-operations/package-overview-45.png)](media/dynamics-365-operations/package-overview.png#lightbox) |
    | <ul><li>ソリューションの承認者</li></ul> | [![[パッケージの概要] 画面](media/dynamics-365-operations/solution-approvers-45.png)](media/dynamics-365-operations/solution-approvers.png#lightbox) |
    | パッケージの内容<ul><li>モデル</li><li>ソフトウェア配置可能パッケージ</li></ul> | [![[パッケージの内容] 画面 1](media/dynamics-365-operations/package-contents-1-45.png)](media/dynamics-365-operations/package-contents-1.png#lightbox) |
    | <ul><li>GER コンフィギュレーション</li><li>データベース バックアップ</li></ul><br>**[GER コンフィギュレーション]** セクションには、コンポーネントは必要ありません。 | [![[パッケージの内容] 画面 2](media/dynamics-365-operations/package-contents-2-45.png)](media/dynamics-365-operations/package-contents-2.png#lightbox) |
    | <ul><li>Power BI レポート モデル</li><li>BPM コンポーネント</li></ul><br>**Power BI** セクションには、コンポーネントは必要ありません。 | [![[パッケージの内容] 画面 3](media/dynamics-365-operations/package-contents-3-45.png)](media/dynamics-365-operations/package-contents-3.png#lightbox) |
    | <ul><li>プロセス データ パッケージ</li><li>ソリューション ライセンス契約とプライバシー ポリシー</li></ul><br>**[GER コンフィギュレーション]** と **[Power BI レポート モデル]** セクションは、必要に応じて Operations オファーに含めることができます。 | [![[パッケージの内容] 画面 4](media/dynamics-365-operations/package-contents-4-45.png)](media/dynamics-365-operations/package-contents-4.png#lightbox) |

    LCS ポータルの各セクションの詳細については、「[LCS ユーザー ガイド](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide)」を参照してください。

3. パートナー センターにアップロードします。

    1. デモ ビデオのアドレスとスクリーン ショットを含むテキスト ドキュメントを作成するか、スクリーンショットを個別の JPG ファイルとして保存します。
    2. [パートナー センター](https://go.microsoft.com/fwlink/?linkid=2165290)のオファーの **[補足コンテンツ]** タブで、テキストと画像を .zip ファイルに追加します。

    [ ![[補足コンテンツ] ページにアップロードされた zip ファイルを示しています。](./media//dynamics-365-operations/supplemental-content-workspaces.png) ](./media//dynamics-365-operations/supplemental-content-workspaces.png#lightbox)

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

1. ビデオを記録し、選択したホスティング サイトにそのアドレスをアップロードします。 次のガイドラインに従ってください。

    - Microsoft 認定チームが参照可能であること。
    - 20 分未満。
    - Dynamics 365 環境内のソリューションの最大 3 つのコア機能のハイライトが含まれていること。

    > [!NOTE]
    > ガイドラインを満たしている場合は、既存のマーケティング ビデオを使用することができます。

2. 発行するプランまたはソリューションに一致する [LCS](https://lcs.dynamics.com/) 環境の次のスクリーンショットを取得します。 これらのスクリーンショットは十分に明瞭で、認証チームがテキストを読み取れる必要があります。 スクリーンショットを JPG ファイルとして保存します。

    1. **[LCS]**  >  **[ビジネス プロセス モデラー]**  >  **[プロジェクト ライブラリ]** に移動します。 すべての [プロセス] のステップのスクリーンショットを取得します。 次に示すように、 **[ダイアグラム]** 列と **[確認済]** 列を含めます。

       :::image type="content" source="media/dynamics-365-operations/project-library.png" alt-text="プロジェクト ライブラリ ウィンドウを表示します。":::

      2. **[LCS]**  >  **[ソリューション管理]**  >  **[ソリューション パッケージのテスト]** に移動します。 次の例に示すように、パッケージの概要と内容を含むスクリーンショットを取得します。

    | フィールド | Image |
    | --- | --- |
    | パッケージの概要 | [![[パッケージの概要] ウィンドウを示すスクリーンショット。](media/dynamics-365-operations/package-overview-45.png)](media/dynamics-365-operations/package-overview.png#lightbox) |
    | <ul><li>ソリューションの承認者</li></ul> | [![[パッケージの概要] 画面](media/dynamics-365-operations/solution-approvers-45.png)](media/dynamics-365-operations/solution-approvers.png#lightbox) |
    | パッケージの内容<ul><li>モデル</li><li>ソフトウェア配置可能パッケージ</li></ul> | [![[パッケージの内容] 画面 1](media/dynamics-365-operations/package-contents-1-45.png)](media/dynamics-365-operations/package-contents-1.png#lightbox) |
    | <ul><li>GER コンフィギュレーション</li><li>データベース バックアップ</li></ul><br>**[GER コンフィギュレーション]** セクションには、コンポーネントは必要ありません。 | [![[パッケージの内容] 画面 2](media/dynamics-365-operations/package-contents-2-45.png)](media/dynamics-365-operations/package-contents-2.png#lightbox) |
    | <ul><li>Power BI レポート モデル</li><li>BPM コンポーネント</li></ul><br>**Power BI** セクションには、コンポーネントは必要ありません。 | [![[パッケージの内容] 画面 3](media/dynamics-365-operations/package-contents-3-45.png)](media/dynamics-365-operations/package-contents-3.png#lightbox) |
    | <ul><li>プロセス データ パッケージ</li><li>ソリューション ライセンス契約とプライバシー ポリシー</li></ul><br>**[GER コンフィギュレーション]** と **[Power BI レポート モデル]** セクションは、必要に応じて Operations オファーに含めることができます。 | [![[パッケージの内容] 画面 4](media/dynamics-365-operations/package-contents-4-45.png)](media/dynamics-365-operations/package-contents-4.png#lightbox) |

    LCS ポータルの各セクションの詳細については、「[LCS ユーザー ガイド](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide)」を参照してください。

3. パートナー センターにアップロードします。

    1. デモ ビデオのアドレスとスクリーン ショットを含むテキスト ドキュメントを作成するか、スクリーンショットを個別の JPG ファイルとして保存します。
    2. [パートナー センター](https://go.microsoft.com/fwlink/?linkid=2165290)のオファーの **[補足コンテンツ]** タブで、テキストと画像を .zip ファイルに追加します。

    [![プロジェクト ライブラリ ウィンドウを表示します](media/dynamics-365-operations/supplemental-content.png)](media/dynamics-365-operations/supplemental-content.png#lightbox)

---

## <a name="next-steps"></a>次のステップ

- オファーの作成を開始するには、「[Microsoft Dynamics 365 オファーを計画する](marketplace-dynamics-365.md)」を参照してください
- オファーの作成が完了したら、次は[確認して公開](dynamics-365-review-publish.md)します
