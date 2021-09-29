---
title: Azure Sentinel の高度なマルチステージ攻撃の検出
description: Azure Sentinel の Fusion テクノロジを利用することで、アラートによる疲労を減らし、高度なマルチステージ攻撃の検出に基づいて、すぐに使用可能なインシデント (事象) を作成します。
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2021
ms.author: yelevin
ms.openlocfilehash: 090e82a3efd27629633772b0549ee6d2452e159d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818944"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel の高度なマルチステージ攻撃の検出

> [!IMPORTANT]
> 一部の Fusion 検出 (以下でそのように示されているものを参照) は、現在 **プレビュー** 段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

機械学習を基盤とする Fusion テクノロジを利用することで、Azure Sentinel では、キルチェーンのさまざまな段階で観察される異常な動作と疑わしい行動の組み合わせを特定し、マルチステージ攻撃を自動的に検出することができます。 これらの検出を基に、Azure Sentinel では、Azure Sentinel 以外では検出が困難であろうインシデントが生成されます。 このインシデントは、2 つ以上のアラートまたはアクティビティで構成されています。 設計上、このようなインシデントでは、ボリュームが低、忠実度が高、重大度が高になります。

この検出テクノロジはご利用の環境によってカスタマイズされるため、[誤検知](false-positives.md)率を減らすだけでなく、情報が制限されているか、不足している攻撃も検出できます。



## <a name="configuration-for-advanced-multistage-attack-detection"></a>高度なマルチステージ攻撃の検出の構成

### <a name="enable-fusion-rule"></a>Fusion ルールを有効にする

この検出は、Azure Sentinel では既定で有効になっています。 状態を確認するか、複数のアラートに基づくインシデントの作成に代替ソリューションを利用している場合に無効にするには、次の指示に従います。

1. まだの場合は、[Azure portal](https://portal.azure.com) にサインインして **Azure Seninel** に入ります。

1. Azure Sentinel のナビゲーション メニューから **[分析]** を選択します。

1. **[アクティブなルール]** タブを選択し、ルールの種類が **Fusion** であるものだけを表示するようリストをフィルターにかけ、 **[名称]** 列で **[高度なマルチステージ攻撃の検出]** を見つけます。 **[状態]** 列を見て、この検出が有効になっているか、無効になっているか確認します。

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-text}" lightbox="./media/fusion/selecting-fusion-rule-type.png":::

1. 状態を変更するには、この項目を選択し、 **[高度なマルチステージ攻撃の検出]** プレビュー ペインで **[編集]** を選択します。

1. **[Rule creation wizard]\(規則作成ウィザード\)** ブレードで、状態の変更が自動的に選択されます。 **[次へ:レビュー]** を選択し、 **[保存]** を選択してください。 

 規則の種類 **[Fusion]** には、変更できない規則が 1 つだけ含まれているため、規則テンプレートはこの規則の種類には適用できません。

> [!NOTE]
> 現在、Azure Sentinel では 30 日間の履歴データを使用して機械学習システムをトレーニングしています。 このデータは、機械学習パイプラインを通過するときに、Microsoft のキーを使用して常に暗号化されます。 ただし、Azure Sentinel ワークスペースで CMK を有効にした場合、トレーニング データは[カスタマー マネージド キー (CMK)](customer-managed-keys.md) を使用して暗号化されません。 Fusion をオプトアウトするには、 **[Azure Sentinel]** \> **[Configuration]** \> **[Analytics] \> [アクティブな規則] \> [Advanced Multistage Attack Detection]\(高度なマルチステージ攻撃の検出\)** に移動し、 **[状態]** 列で **[無効にする]** を選択します。

### <a name="configure-scheduled-analytics-rules-for-fusion-detections"></a>Fusion 検出のスケジュールされた分析のルールを構成する

> [!IMPORTANT]
>
> - 分析ルール アラートを使用した Fusion による検出は、現在 **プレビュー版** です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

**Fusion** では、[スケジュールされた分析のルール](detect-threats-custom.md)により生成されるアラートを使用して、マルチステージ攻撃を検出できます。 Azure Sentinel の Fusion の機能を最大限に活用できるよう、次の手順でこれらのルールを構成、有効化することをお勧めします。

1. 次の **スケジュールされた分析のルールのテンプレート** を使用して新しいルールを作成します。これは **[分析]** ブレードの **[ルールのテンプレート]** タブにあります。 テンプレート ギャラリーでルール名をクリックし、プレビュー ペインで **[ルールの作成]** をクリックします。

    - [Cisco - firewall block but success logon to Azure AD](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/SigninFirewallCorrelation.yaml) (Cisco - ファイアウォールによるブロック Azure AD へのログインに成功)
    - [Fortinet - Beacon pattern detected](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/CommonSecurityLog/Fortinet-NetworkBeaconPattern.yaml) (Fortinet - ビーコン パターン検出)
    - [IP with multiple failed Azure AD logins successfully logs in to Palo Alto VPN](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/HostAADCorrelation.yaml) (Azure AD へのログインに何回も失敗した IP が Palo Alto VPN へのログインに成功)
    - [Multiple Password Reset by user](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/MultipleDataSources/MultiplePasswordresetsbyUser.yaml) (ユーザーがパスワードを何回もリセット)
    - [New Admin account activity seen which was not seen historically](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Hunting%20Queries/OfficeActivity/new_adminaccountactivity.yaml) (これまで見られなかった新しい管理者アカウントによる活動)
    - [Rare application consent](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AuditLogs/RareApplicationConsent.yaml) (通常見られないアプリケーションへの同意)
    - [SharePointFileOperation via previously unseen IPs](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/OfficeActivity/SharePoint_Downloads_byNewIP.yaml) (これまで見られなかった IP による SharePointFileOperation)
    - [Suspicious Resource deployment](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AzureActivity/NewResourceGroupsDeployedTo.yaml) (リソースの不審なデプロイ)

    > [!NOTE]
    > Fusion で使用するスケジュールされた分析のルールでは、ML アルゴリズムは、テンプレートにある KQL クエリに対してあいまい一致を適用しません。 テンプレート名を変更しても、Fusion による検出に影響はありません。

1. これらのスケジュール ルールの **エンティティ マッピング** を確認します。 [エンティティ マッピング構成に関するセクション](map-data-fields-to-entities.md)を参考にして、クエリの結果から Azure Sentinel で認識しているエンティティに、パラメーターをマッピングします。 Fusion は、エンティティ (*ユーザー アカウント*、*IP アドレス* など) に基づいてアラートの関連付けを行うので、ML アルゴリズムでは、エンティティ情報がなければアラートの一致を検出できません。

1. 分析ルールの詳細で **タクティクス** を確認します。 Fusion ML アルゴリズムでは、MITRE ATT&CK タクティクス情報を使用してマルチステージ攻撃を検出します。分析ルールにラベルとして付けたタクティクスは、生成されるインシデントに表示されます。 受け取る警告にタクティクスの情報がない場合、Fusion の計算に影響が出ることがあります。

1. **警告のしきい値** を必要に応じて調整します。 Fusion では、スケジュールされた分析のルールに従って出される警告に基づいてインシデントを生成します。 Fusion インシデントの数を減らしたい場合は、警告のしきい値を必要に応じて調整します。 特定の分析ルールに基づくインシデントを一切受け取りたくない場合は、そのルールを無効にすることもできます。

## <a name="attack-detection-scenarios"></a>攻撃の検出シナリオ

次のセクションに、Azure Sentinel が Fusion テクノロジを使用して検索する、相関関係シナリオの種類を脅威分類別にグループ化して示します。

前述のように、Fusion は、高度なマルチステージ攻撃を検出するためにさまざまな製品からの複数のセキュリティ アラートを関連付けるため、成功した Fusion 検出は、Azure Sentinel の **[インシデント]** ページに **Fusion インシデント** として表示され、 **[ログ]** の **[セキュリティ アラート]** テーブルには **アラート** として表示されません。

これらの Fusion による攻撃検出シナリオを有効にするには、一覧にあるデータ ソースを関連付けられている Azure Sentinel データ コネクタを使用して取り込む必要があります。

> [!NOTE]
> これらのシナリオの一部は **プレビュー** 段階です。 そのように明記されています。

## <a name="compute-resource-abuse"></a>Compute リソース プールの不正使用

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>疑わしい Azure Active Directory サインインの後の複数の VM 作成アクティビティ
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、影響 

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、リソース ハイジャック (T1496)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、1 つのセッションで異常な数の VM が作成されたことを示しています。 この種類のアラートは、高い信頼度で、Fusion インシデントの説明に示されているアカウントが侵害され、暗号化マイニング操作の実行など、不正な使用を目的として新しい VM を作成するために使用されたことを示しています。 複数の VM 作成アクティビティ アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。

- **特殊な場所へのあり得ない移動の後の、複数の VM 作成アクティビティ**

- **知らない場所からサインインされた後の、複数の VM 作成アクティビティ**

- **感染したデバイスからサインインされた後の、複数の VM 作成アクティビティ**

- **送信元のわからない IP アドレスからサインインされた後の、複数の VM 作成アクティビティ**

- **資格情報が漏洩したユーザーからサインインされた後の、複数の VM 作成アクティビティ**

## <a name="credential-access"></a>資格情報へのアクセス
(新しい脅威の分類)

### <a name="new-multiple-passwords-reset-by-user-following-suspicious-sign-in"></a>新しい情報!  次の不審なサインインに続いて、ユーザーによりパスワードが複数回リセットされるケース
このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、資格情報アクセス

**MITRE ATT&CK techniques:** Valid Account (有効なアカウント) (T1078)、Brute Force (ブルート フォース攻撃) (T1110)

**データ コネクタのソース:** Azure Sentinel (スケジュールされた分析のルール)、Azure Active Directory Identity Protection

**Description:** この Fusion インシデントは、Azure AD アカウントへの不審なサインインに続いて、ユーザーが何回もパスワードをリセットしたことを示します。 これは、複数のシステムとリソースにアクセスするために、アカウントがハッキングされ、そのパスワードが何度もリセットされたことを示唆しています。このアカウントは、Fusion インシデントの説明文に記載されます。 アカウントの操作 (パスワードのリセットを含む) は、不正利用するためのサインイン情報と特定環境でのアクセス権を維持するのに役立つ場合があります。 Azure AD への不審なサインインに対する警告で、複数回のパスワード リセットに対する警告を伴うものを、次に挙げます。

- **通常と異なる場所へのあり得ない移動と、それに続く複数回のパスワード リセット**

- **なじみの薄い場所からのサインインと、それに続く複数回のパスワードリセット**

- **感染したデバイスからのサインインと、それに続く複数回のパスワード リセット**

- **匿名 IP アドレスによるサインインと、それに続く複数回のパスワード リセット**

- **サインイン情報が漏洩したユーザーによるサインインと、それに続く複数回のパスワード リセット**

### <a name="new-suspicious-sign-in-coinciding-with-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins"></a>新しい情報!  Azure AD へのサインインに複数回失敗した IP アドレスによる Palo Alto VPN へのサインインの成功と同時に行われる、不審なサインイン
このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、資格情報アクセス

**MITRE ATT&CK techniques:** Valid Account (有効なアカウント) (T1078)、Brute Force (ブルート フォース攻撃) (T1110)

**データ コネクタのソース:** Azure Sentinel (スケジュールされた分析のルール)、Azure Active Directory Identity Protection

**説明:** この Fusion インシデントは、ほぼ同じ時間に Azure AD へのサインインに複数回失敗した IP アドレスの使用者が、Palo Alto VPN を利用したサインインに成功するのと同時に、Azure AD アカウントへの不審なサインインがあったことを示します。 これはマルチステージ攻撃があった証拠にはなりませんが、これら 2 つの重要性の低い警告の組み合わせは重要なインシデントであるとみなされます。組織のネットワークに対し、最初の悪意のあるアクセスが行われたことを示唆しています。 もう 1 つの解釈として、これは、Azure AD アカウントへのアクセスを得るために、攻撃者がブルート フォース攻撃を仕掛けたことを示していることも考えられます。 Azure AD への不審なサインインに対する警告で、“Azure AD へのサインインに複数回失敗した IP アドレスの使用者が Palo Alto VPN へのログインに成功したイベント” に対する警告を伴うものを、次に挙げます。

- **通常と異なる場所へのあり得ない移動が行われるのと同時に、Azure AD へのログインに複数回失敗した IP アドレスの使用者が Palo Alto VPN へのログインに成功**

- **なじみの薄い場所からのサインインが行われるのと同時に、Azure AD へのログインに複数回失敗した IP アドレスの使用者が Palo Alto VPN へのログインに成功**

- **感染したデバイスからのサインインが行われるのと同時に、Azure AD へのログインに複数回失敗した IP アドレスの使用者が Palo Alto VPN へのログインに成功**

- **匿名 IP アドレスによるサインインが行われるのと同時に、Azure AD へのログインに複数回失敗した IP アドレスの使用者が Palo Alto VPN へのログインに成功**

- **サインイン情報が漏洩したユーザーによるサインインが行われるのと同時に、Azure AD へのログインに複数回失敗した IP アドレスの使用者が Palo Alto VPN へのログインに成功**

## <a name="credential-harvesting"></a>サインイン情報の不正取得
(新しい脅威の分類)
### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>疑わしいサインインの後の悪意のある資格情報盗難ツールの実行

**MITRE ATT&CK の方針:** 初期アクセス、資格情報アクセス

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、OS 資格情報のダンプ (T1003)

**データ コネクタのソース:** Azure Active Directory Identity Protection、エンドポイント用 Microsoft Defender

**説明:** この種類の Fusion インシデントは、疑わしい Azure AD サインインの後に既知の資格情報盗難ツールが実行されたことを示します。 これは、キー、プレーンテキスト形式のパスワード、パスワードのハッシュなどをシステムから不正取得する **Mimikatz** などのツールを使用するために、ユーザー アカウントがハッキングされ、利用されたことを、ほぼ間違いなく示しています。このアカウントは、警告の説明文に記載されます。 収集した資格情報を使用して、攻撃者は機密データにアクセスしたり、特権を昇格させたり、ネットワークを横方向に移動したりする可能性があります。 悪意のある資格情報盗難ツール アラートと組み合わされた、疑わしい Azure AD サインイン アラートの順列は次のとおりです。

- **特殊な場所へのあり得ない移動が、悪意のある資格情報盗難ツールの実行につながる**

- **未知の場所からのサインイン イベントが、悪意のある資格情報盗難ツールの実行につながる**

- **感染したデバイスからのサインイン イベントが、悪意のある資格情報盗難ツールの実行につながる**

- **匿名 IP アドレスからのサインイン イベントが、悪意のある資格情報盗難ツールの実行につながる**

- **資格情報が漏洩したユーザーからのサインイン イベントが、悪意のある資格情報盗難ツールの実行につながる**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>疑わしいサインインの後の資格情報盗難アクティビティの疑い

**MITRE ATT&CK の方針:** 初期アクセス、資格情報アクセス

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、パスワード ストアの資格情報 (T1555)、OS 資格情報のダンプ (T1003)

**データ コネクタのソース:** Azure Active Directory Identity Protection、エンドポイント用 Microsoft Defender

**説明:** この種類の Fusion インシデントは、資格情報盗難のパターンに関連付けられたアクティビティが、疑わしい Azure AD サインインの後に発生したことを示します。 これは、キー、プレーンテキスト形式のパスワード、パスワードのハッシュなどを盗むために、ユーザー アカウントがハッキングされ、利用されたことを、ほぼ間違いなく示しています。このアカウントは、警告の説明文に記載されます。 盗んだ資格情報を使用して、攻撃者は機密データにアクセスしたり、特権を昇格させたり、ネットワークを横方向に移動したりする可能性があります。 資格情報盗難アクティビティ アラートと組み合わされた、疑わしい Azure AD サインイン アラートの順列は次のとおりです。

- **特殊な場所へのあり得ない移動の後、資格情報盗難アクティビティが疑われる**

- **未知の場所からのサインイン イベントの後、資格情報盗難アクティビティが疑われる**

- **感染したデバイスからのサインイン イベントの後、資格情報盗難アクティビティが疑われる**

- **匿名 IP アドレスからのサインイン イベントの後、資格情報盗難アクティビティが疑われる**

- **資格情報が漏洩したユーザーからのサインイン イベントの後、資格情報盗難アクティビティが疑われる**

## <a name="crypto-mining"></a>暗号通貨マイニング
(新しい脅威の分類)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>疑わしいサインインの後の暗号化マイニング アクティビティ

**MITRE ATT&CK の方針:** 初期アクセス、資格情報アクセス

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、リソース ハイジャック (T1496)

**データ コネクタのソース:** Azure Active Directory Identity Protection、Azure Defender (Azure Security Center)

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインに関連付けられた暗号化マイニング アクティビティを示します。 これは、当該環境のリソースを乗っ取って暗号通貨のマイニングに使用するために、ユーザー アカウントがハッキングされ、利用されたことを、ほぼ間違いなく示しています。このアカウントは、警告の説明文に記載されます。 これにより、コンピューティング能力が損なわれたり、クラウドの使用料が予測よりも大幅に高くなるおそれがあります。 暗号化マイニング アクティビティ アラートと組み合わされた、疑わしい Azure AD サインイン アラートの順列は次のとおりです。  

- **特殊な場所へのあり得ない移動が、暗号化マイニング アクティビティにつながる**

- **未知の場所からのサインイン イベントが、暗号化マイニング アクティビティにつながる**

- **感染したデバイスからのサインイン イベントが、暗号化マイニング アクティビティにつながる**

- **匿名 IP アドレスからのサインイン イベントが、暗号化マイニング アクティビティにつながる**

- **資格情報が漏洩したユーザーからのサインイン イベントが、暗号化マイニング アクティビティにつながる**

## <a name="data-destruction"></a>データの破壊

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の大量のファイル削除

**MITRE ATT&CK の方針:** 初期アクセス、影響

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、データの破棄 (T1485)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、異常な数の固有なファイルが削除されたことを示しています。 これは、悪意ある目的でデータを破壊するために、アカウントがハッキングれ、利用されたことを示唆しています。このアカウントは、Fusion インシデントの説明文に記載されます。 大量のファイル削除アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、大量のファイルの削除**

- **知らない場所からサインインされた後の、大量のファイルの削除された**

- **感染したデバイスからサインインされた後の、大量のファイルの削除**

- **送信元のわからない IP アドレスからサインインされた後の、大量のファイルの削除**

- **資格情報が漏洩したユーザーからサインインされた後の、大量のファイルの削除**

### <a name="new-mass-file-deletion-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance"></a>新しい情報!  Cisco ファイアウォール アプライアンスにブロックされている IP アドレスによる Azure AD へのサインインと、それに続くファイルの大規模な削除
このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、影響

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、データの破棄 (T1485)

**データ コネクター のソース:** Azure Sentinel (スケジュールされた分析のルール)、Microsoft Cloud App Security

**説明**: この Fusion インシデントは、Cisco ファイアウォール アプライアンスにブロックされている IP アドレスによって Azure AD へのサインインが行われた後で、その組織にしかないファイルが異常に多く削除されたことを示しています。 これは、悪意ある目的でデータを破壊するために、アカウントがハッキングされ、利用されたことを示しています。このアカウントは、Fusion インシデントの説明文に記載されます。 ファイアウォールにブロックされている IP アドレス が Azure AD へのログインに成功しているのは疑わしい点があり、ユーザー アカウントのサインイン情報が漏洩した可能性を示しています。

### <a name="new-mass-file-deletion-following-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins"></a>新しい情報!  Azure AD へのサインインに複数回失敗した IP アドレスによる Palo Alto VPN へのサインインと、それに続くファイルの大規模な削除
このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK tactics:** Initial Access (最初のアクセス)、Credential Access (サインイン情報へのアクセス)、Impact (影響)

**MITRE ATT&CK techniques:** Valid Account (有効なアカウント) (T1078)、Brute Force (ブルート フォース攻撃) (T1110)、Data Destruction (データ破壊) (T1485)

**データ コネクター のソース:** Azure Sentinel (スケジュールされた分析のルール)、Microsoft Cloud App Security

**説明:** この Fusion インシデントは、ほぼ同じ時間に Azure AD へのサインインに複数回失敗した IP アドレスによって Palo Alto VPN へのサインインに成功したユーザーが、その組織にしかないファイルを異常に多く削除したことを示しています。 これは、ユーザー アカウントがブルート フォース攻撃によってハッキングされ、悪意ある目的によるデータ破壊に使用されたことを示しています。ハッキングされたアカウントは、Fusion インシデントに記載されます。

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の疑わしい電子メール削除アクティビティ
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、影響 

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、データの破棄 (T1485)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、1 つのセッションで異常な数の電子メールが削除されたことを示しています。 これは、組織に害を与えたり、スパムに関連するメールを隠したりする悪意ある目的でデータを破壊するために、アカウントがハッキングされ、利用されたことを示しています。このアカウントは、Fusion インシデントの説明文に記載されます。 疑わしい電子メール削除アクティビティ アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。   

- **特殊な場所へのあり得ない移動の後の、疑わしい電子メール削除アクティビティ**

- **知らない場所からサインインされた後の、疑わしい電子メール削除アクティビティ**

- **感染したデバイスからサインインされた後の、疑わしい電子メール削除アクティビティ**

- **送信元のわからない IP アドレスからサインインされた後の、疑わしい電子メール削除アクティビティ**

- **資格情報が漏洩したユーザーからサインインされた後の、疑わしい電子メール削除アクティビティ**

## <a name="data-exfiltration"></a>データ窃盗

### <a name="new-mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently"></a>新しい情報!  管理者アカウントの新しい活動が見られなくなった後のメール転送活動
このケースは、**データ流出** と **悪意のある管理者活動** の 2 種類の脅威に分類されます。 わかりやすくするために、両方のセクションに記載されています。

このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK tactics:** Initial Access (最初のアクセス)、Collection (収集)、Exfiltration (流出)

**MITRE ATT&CK techniques:** Valid Account (有効なアカウント) (T1078)、Email Collection (メール収集) (T1114)、Exfiltration Over Web Service (Web サービスを通じた流出) (T1567)

**データ コネクター のソース:** Azure Sentinel (スケジュールされた分析のルール)、Microsoft Cloud App Security

**説明**: この Fusion インシデントは、新規作成された Exchange 管理者アカウントまたは既存の Exchange 管理者アカウントが、過去 2 週間ではじめて管理者としての操作を行い、その後メール転送操作を行ったことを示しています。管理者アカウントでは通常メールの転送は行いません。 これは、ユーザー アカウントがハッキングまたは操作され、組織のネットワークからデータを盗難するのに使用されたことを示しています。ハッキングされたアカウントは、Fusion インシデントの説明文に記載されます。

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の大量のファイルのダウンロード

**MITRE ATT&CK の方針:** 初期アクセス、流出

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、ユーザーによって異常な数のファイルがダウンロードされたことを示しています。 この情報は、Fusion インシデントの説明に記載されたアカウントが侵害され、組織のネットワークからデータを流出させるために使用されたことを示しています。 大量のファイルのダウンロード アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、大量のファイルのダウンロード**

- **知らない場所からサインインされた後の、大量のファイルのダウンロード**

- **感染したデバイスからサインインされた後の、大量のファイルのダウンロード**

- **送信元のわからない IP からサインインされた後の、大量のファイルのダウンロード**

- **資格情報が漏洩したユーザーからサインインされた後の、大量のファイルのダウンロード**

### <a name="new-mass-file-download-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance"></a>新しい情報!  Cisco ファイアウォール アプライアンスにブロックされている IP アドレスによる Azure AD へのサインインと、それに続くファイルの大規模なダウンロード
このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、流出

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、Web サービス経由の流出 (T1567)

**データ コネクター のソース:** Azure Sentinel (スケジュールされた分析のルール)、Microsoft Cloud App Security

**説明**: この Fusion インシデントは、Cisco ファイアウォール アプライアンスにブロックされている IP アドレスによって Azure AD へのサインインが行われたのに続いて、異常に多くのファイルがユーザーによりダウンロードされたことを示しています。 攻撃者が、ユーザー アカウントをハッキングした後で、組織のネットワークからデータを盗難しようとした可能性があります。 ファイアウォールにブロックされている IP アドレス が Azure AD へのログインに成功しているのは疑わしい点があり、ユーザー アカウントのサインイン情報が漏洩した可能性を示しています。

### <a name="new-mass-file-download-coinciding-with-sharepoint-file-operation-from-previously-unseen-ip"></a>新しい情報!  これまで見られなかった IP アドレスによる SharePoint ファイルの操作と同時に発生する、ファイルの大規模なダウンロード
このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK tactics:** Exfiltration (流出)

**MITRE ATT&CK techniques:** Exfiltration Over Web Service (Web サービスを通じた流出) (T1567)、Data Transfer Size Limits (データ転送サイズ上限) (T1030)

**データ コネクター のソース:** Azure Sentinel (スケジュールされた分析のルール)、Microsoft Cloud App Security

**説明:** この Fusion インシデントは、これまで見られなかった IP アドレスによりネットワークに接続したユーザーにより、異常に多くのファイルがダウンロードされたことを示しています。 これはマルチステージ攻撃があった証拠にはなりませんが、これら 2 つの重要性の低い警告の組み合わせは重要なインシデントであるとみなされます。攻撃者が、他者から乗っ取った可能性のあるアカウントを利用して、組織のネットワークからデータを盗難しようとしたことを示唆しています。 安定した環境を構築する場合、これまで見られなかった IP アドレスによるこのような接続は、特に大規模なドキュメントの流出に関連し得る通信量の急増との関連が疑われるときは、認可を受けられないようにします。

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の大量のファイル共有

**MITRE ATT&CK の方針:** 初期アクセス、流出

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、Web サービス経由の流出 (T1567)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、特定のしきい値を超える数のファイルが他者と共有されたことを示しています。 この情報は、Fusion インシデントの説明に記載されたアカウントが侵害され、ドキュメント、スプレッドシートなどのファイルを悪意のある目的で無許可のユーザーと共有して組織のネットワークからデータを流出させるために使用されたことを示しています。 大量のファイル共有アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、大量のファイル共有**

- **知らない場所からサインインされた後の、大量のファイル共有**

- **感染したデバイスからサインインされた後の、大量のファイル共有**

- **送信元のわからない IP アドレスからサインインされた後の、大量のファイル共有**

- **資格情報が漏洩したユーザーからサインインされた後の、大量のファイル共有**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の複数の Power BI レポート共有アクティビティ 
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、流出 

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、Web サービス経由の流出 (T1567)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、1 つのセッションで異常な数の Power BI レポートが共有されたことを示しています。 この情報は、Fusion インシデントの説明に記載されたアカウントが侵害され、Power BI レポートを悪意のある目的で無許可のユーザーと共有して組織のネットワークからデータを流出させるために使用されたことを示しています。 複数の Power BI レポート共有アクティビティと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動の後の、複数の Power BI レポート共有アクティビティ**

- **知らない場所からサインインされた後の、複数の Power BI レポート共有アクティビティ**

- **感染したデバイスからサインインされた後の、複数の Power BI レポート共有アクティビティ**

- **送信元のわからない IP アドレスからサインインされた後の、複数の Power BI レポート共有アクティビティ**

- **資格情報が漏洩したユーザーからサインインされた後の、複数の Power BI レポート共有アクティビティ**

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の Office 365 メールボックスからの情報の流出

**MITRE ATT&CK の方針:** 初期アクセス、流出、収集

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、電子メール収集 (T1114)、自動化された流出 (T1020)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、ユーザーの受信トレイで疑わしい受信トレイの転送ルールが設定されたことを示しています。 この情報は、(Fusion インシデントの説明に記載された) ユーザーのアカウントが侵害されたこと、および実際のユーザーが知らないうちにメールボックス転送ルールを有効にして組織のネットワークからデータを流出させるために使用されたことを示しています。 Office 365 メールボックスからの情報の流出アラートが組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。

- **特殊な場所へのあり得ない移動後の、Office 365 メールボックスからの情報の流出**

- **知らない場所からサインインされた後の、Office 365 メールボックスからの情報の流出**

- **感染したデバイスからサインインされた後の、Office 365 メールボックスからの情報の流出**

- **送信元のわからない IP アドレスからサインインされた後の、Office 365 メールボックスからの情報の流出**

- **資格情報が漏洩したユーザーからサインインされた後の、Office 365 メールボックスからの情報の流出**

### <a name="new-sharepoint-file-operation-from-previously-unseen-ip-following-malware-detection"></a>新しい情報!  マルウェアの検出と、それに続く、これまで見られなかった IP アドレスによる SharePoint ファイルの操作
このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK tactics:** Exfiltration (流出), Defense Evasion (防衛機構の回避)

**MITRE ATT&CK techniques:** Data Transfer Size Limits (データ転送サイズ上限) (T1030)

**データ コネクター のソース:** Azure Sentinel (スケジュールされた分析のルール)、Microsoft Cloud App Security

**説明:** この Fusion インシデントは、マルウェアを利用した SharePoint でのダウンロードまたは共有により、攻撃者が大量のデータを盗難しようとしたことを示しています。 安定した環境を構築する場合、これまで見られなかった IP アドレスによるこのような接続は、特に大規模なドキュメントの流出に関連し得る通信量の急増との関連が疑われるときは、認可を受けられないようにします。 

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の疑わしい受信トレイ操作ルールの設定
このシナリオは、このリストで 2 つの脅威の分類 (**データ流出** と **侵入拡大**) に含まれています。 わかりやすくするために、両方のセクションに記載されています。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、侵入拡大、流出

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、内部のスピアフィッシング (T1534)、自動化された流出 (T1020)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、ユーザーの受信トレイで異常な受信トレイ ルールが設定されたことを示しています。 これは、組織のネットワークからデータを盗難するなどの悪意ある目的でユーザーのメールの受信ルールを操作するために、アカウントがハッキングされ、利用されたことを、ほぼ間違いなく示しています。このアカウントは、Fusion インシデントの説明文に記載されます。 または、攻撃者は、その他のユーザーや特権のあるアカウントへのアクセスを取得し、(外部ソースからのメールを対象とするフィッシング検出メカニズムを迂回して) 組織内から侵入拡大目的でフィッシング メールを生成しようとしたと考えられます。 疑わしい受信トレイ操作ルール アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。

- **特殊な場所へのあり得ない移動後の、疑わしい受信トレイ操作ルール**

- **知らない場所からサインインされた後の、疑わしい受信トレイ操作ルール**

- **感染したデバイスからサインインされた後の、疑わしい受信トレイ操作ルール**

- **送信元のわからない IP アドレスからサインインされた後の、疑わしい受信トレイ操作ルール**

- **資格情報が漏洩したユーザーからサインインされた後の、疑わしい受信トレイ操作ルール**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の疑わしい Power BI レポート共有
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、流出 

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、Web サービス経由の流出 (T1567)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、疑わしい Power BI レポート共有アクティビティが発生したことを示しています。 Power BI レポートには自然言語処理を使用して識別された機密情報が含まれていたため、および外部の電子メール アドレスで共有された、Web に公開された、または外部で登録されている電子メール アドレスにスナップショットとして配信されたため、共有アクティビティは疑わしいものとして識別されました。 このアラートは、Fusion インシデントの説明に記載されたアカウントが侵害され、Power BI レポートを悪意のある目的で無許可のユーザーと共有して組織から機密データを流出させるために使用されたことを示しています。 疑わしい Power BI レポート共有と組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動の後の、疑わしい Power BI レポート共有**

- **知らない場所からサインインされた後の、疑わしい Power BI レポート共有**

- **感染したデバイスからサインインされた後の、疑わしい Power BI レポート共有**

- **送信元のわからない IP アドレスからサインインされた後の、疑わしい Power BI レポート共有**

- **資格情報が漏洩したユーザーからサインインされた後の、疑わしい Power BI レポート共有**

## <a name="denial-of-service"></a>サービス拒否

### <a name="multiple-vm-deletion-activities-following-suspicious-azure-ad-sign-in"></a>Azure AD への不審なサインインと、それに続く、複数の VM の削除操作
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、影響

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、エンドポイントのサービス拒否 (T1499)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、1 つのセッションで異常な数の VM が削除されたことを示しています。 この情報は、Fusion インシデントの説明に記載されたアカウントが侵害され、組織のクラウド環境を中断または破壊しようとするために使用されたことを示しています。 Azure AD への不審なサインインに対する警告で、複数の VM の削除操作に対する警告を伴うものを、次に挙げます。  

- **通常と異なる場所へのあり得ない移動と、それに続く複数の VM の削除操作**

- **なじみの薄い場所からのサインインと、それに続く複数の VM の削除操作**

- **感染したデバイスからのサインインと、それに続く複数の VM の削除操作**

- **匿名 IP アドレスによるサインインと、それに続く複数の VM の削除操作**

- **サインイン情報が漏洩したユーザーによるサインインと、それに続く複数の VM の削除操作**

## <a name="lateral-movement"></a>侵入拡大

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の Office 365 偽装

**MITRE ATT&CK の方針:** 初期アクセス、侵入拡大

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、内部のスピアフィッシング (T1534)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントからの疑わしいサインインの後、異常な数の偽装アクションが発生したことを示しています。 一部のソフトウェアでは、ユーザーが他のユーザーを偽装できるオプションがあります。 たとえば、メール サービスを使用すると、ユーザーは自分に代わって他のユーザーがメールを送信することを承認できます。 このアラートは、Fusion インシデントの説明に記載されているアカウントが侵害され、マルウェア配布や侵入拡大のためにフィッシング メールを送信するなど、悪意のある目的で偽装アクティビティを実行するために使用されたことを、高い信頼度で示しています。 Office 365 での偽装アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、Office 365 メールボックスの偽装**

- **知らない場所からサインインされた後の、Office 365 での偽装**

- **感染したデバイスからサインインされた後の、Office 365 での偽装**

- **送信元のわからない IP アドレスからサインインされた後の、Office 365 での偽装**

- **資格情報が漏洩したユーザーからサインインされた後の、Office 365 での偽装**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の疑わしい受信トレイ操作ルールの設定
このシナリオは、このリストで 2 つの脅威の分類 (**侵入拡大** と **データ流出**) に含まれています。 わかりやすくするために、両方のセクションに記載されています。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、侵入拡大、流出

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、内部のスピアフィッシング (T1534)、自動化された流出 (T1020)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、ユーザーの受信トレイで異常な受信トレイ ルールが設定されたことを示しています。 これは、組織のネットワークからデータを盗難するなどの悪意ある目的でユーザーのメールの受信ルールを操作するために、アカウントがハッキングされ、利用されたことを、ほぼ間違いなく示しています。このアカウントは、Fusion インシデントの説明文に記載されます。 または、攻撃者は、その他のユーザーや特権のあるアカウントへのアクセスを取得し、(外部ソースからのメールを対象とするフィッシング検出メカニズムを迂回して) 組織内から侵入拡大目的でフィッシング メールを生成しようとしたと考えられます。 疑わしい受信トレイ操作ルール アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。

- **特殊な場所へのあり得ない移動後の、疑わしい受信トレイ操作ルール**

- **知らない場所からサインインされた後の、疑わしい受信トレイ操作ルール**

- **感染したデバイスからサインインされた後の、疑わしい受信トレイ操作ルール**

- **送信元のわからない IP アドレスからサインインされた後の、疑わしい受信トレイ操作ルール**

- **資格情報が漏洩したユーザーからサインインされた後の、疑わしい受信トレイ操作ルール**

## <a name="malicious-administrative-activity"></a>悪意のある管理アクティビティ

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後のクラウド アプリでの疑わしい管理行為

**MITRE ATT&CK の方針:** 初期アクセス、永続性、防御侵入、侵入拡大、収集、流出、影響

**MITRE ATT&CK の手法:** 該当なし

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、同じアカウントからの疑わしい Azure AD サインインの後、1 つのセッションで異常な数の管理行為が実行されたことを示しています。 これは、悪意ある意図で認可を受けずに管理者としての操作を繰り返し実行するために、アカウントがハッキングされ、利用されたことを示唆しています。このアカウントは、Fusion インシデントの説明文に記載されます。 これは、管理特権を持つアカウントが侵害された可能性があることも示しています。 クラウド アプリでの疑わしい管理行為アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、クラウド アプリでの疑わしい管理行為**

- **知らない場所からサインインされた後の、クラウド アプリでの疑わしい管理行為**

- **感染したデバイスからサインインされた後の、クラウド アプリでの疑わしい管理行為**

- **送信元のわからない IP アドレスからサインインされた後の、クラウド アプリでの疑わしい管理行為**

- **資格情報が漏洩したユーザーからサインインされた後の、クラウド アプリでの疑わしい管理行為**

### <a name="new-mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently"></a>新しい情報!  管理者アカウントの新しい活動が見られなくなった後のメール転送活動
このケースは、**悪意のある管理者活動** と **データ流出** の 2 種類の脅威に分類されます。 わかりやすくするために、両方のセクションに記載されています。

このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK tactics:** Initial Access (最初のアクセス)、Collection (収集)、Exfiltration (流出)

**MITRE ATT&CK techniques:** Valid Account (有効なアカウント) (T1078)、Email Collection (メール収集) (T1114)、Exfiltration Over Web Service (Web サービスを通じた流出) (T1567)

**データ コネクター のソース:** Azure Sentinel (スケジュールされた分析のルール)、Microsoft Cloud App Security

**説明**: この Fusion インシデントは、新規作成された Exchange 管理者アカウントまたは既存の Exchange 管理者アカウントが、過去 2 週間ではじめて管理者としての操作を行い、その後メール転送操作を行ったことを示しています。管理者アカウントでは通常メールの転送は行いません。 これは、ユーザー アカウントがハッキングまたは操作され、組織のネットワークからデータを盗難するのに使用されたことを示しています。ハッキングされたアカウントは、Fusion インシデントの説明文に記載されます。

## <a name="malicious-execution-with-legitimate-process"></a>正当なプロセスによる悪意のある実行

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell で疑わしいネットワーク接続が行われた後に、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィックが続きます。
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 実行

**MITRE ATT&CK の手法:** コマンドおよびスクリプト インタープリター (T1059)

**データ コネクタのソース:** Microsoft Defender for Endpoint (旧称 Microsoft Defender Advanced Threat Protection または MDATP)、Palo Alto Networks 

**説明:** この種類の Fusion インシデントは、PowerShell コマンドを使用して送信接続要求が行われ、その後、Palo Alto Networks ファイアウォールによって異常な受信アクティビティが検出されたことを示しています。 これは、恐らく攻撃者が既にネットワークへのアクセスを獲得しており、悪意ある行為を実行しようとしていることを示唆しています。 このパターンに従う PowerShell による接続の試行は、マルウェアのコマンド アンド コントロール アクティビティ、追加のマルウェアのダウンロード要求、またはリモートの対話型アクセスを確立する攻撃者を示している可能性があります。 すべての "現地調達型" 攻撃と同様、このアクティビティは PowerShell を正当に使用している可能性があります。 ただし、PowerShell コマンドの実行後に疑わしい受信ファイアウォール アクティビティが続く場合、より高い信頼度で PowerShell が悪意のある方法で使用されていることを示しており、さらに詳しく調査する必要があります。 Palo Alto ログでは、Azure Sentinel は[脅威ログ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)に焦点を当てており、脅威 (疑わしいデータ、ファイル、フラッド、パケット、スキャン、スパイウェア、URL、ウイルス、脆弱性、wildfire ウイルス、wildfire) が許可されている場合、トラフィックは疑わしいと見なされます。 アラートのその他の詳細について、Fusion インシデントの説明に記載されている[脅威/コンテンツ タイプ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)に対応する Palo Alto 脅威ログも参照してください。

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>疑わしいリモート WMI 実行後の、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィック
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 実行、検出

**MITRE ATT&CK の手法:** Windows Management Instrumentation (T1047)

**データ コネクタのソース:** Microsoft Defender for Endpoint (旧称 MDATP)、Palo Alto Networks 

**説明:** この種類の Fusion インシデントは、Windows Management Interface (WMI) コマンドがシステムでリモート実行され、その後、疑わしい受信アクティビティが Palo Alto Networks ファイアウォールによって検出されたことを示しています。 これは、攻撃者が既にネットワークへのアクセスを獲得している可能性があり、水平移動、高度な権限の獲得、悪意あるペイロードの実行を試みていることを示唆しています。 すべての "現地調達型" 攻撃と同様、このアクティビティは WMI を正当に使用している可能性があります。 ただし、リモート WMI コマンドの実行後に疑わしい受信ファイアウォール アクティビティが続く場合、より高い信頼度で WMI が悪意のある方法で使用されていることを示しており、さらに詳しく調査する必要があります。 Palo Alto ログでは、Azure Sentinel は[脅威ログ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)に焦点を当てており、脅威 (疑わしいデータ、ファイル、フラッド、パケット、スキャン、スパイウェア、URL、ウイルス、脆弱性、wildfire ウイルス、wildfire) が許可されている場合、トラフィックは疑わしいと見なされます。 アラートのその他の詳細について、Fusion インシデントの説明に記載されている[脅威/コンテンツ タイプ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)に対応する Palo Alto 脅威ログも参照してください。

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>疑わしいサインインの後の疑わしい PowerShell コマンド ライン

**MITRE ATT&CK の方針:** 初期アクセス、実行

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、コマンドおよびスクリプト インタープリター (T1059)

**データ コネクタのソース:** Azure Active Directory Identity Protection、エンドポイント用 Microsoft Defender (旧称 MDATP)

**説明:** この種類の Fusion インシデントは、ユーザーが Azure AD アカウントへの疑わしいサインインの後に悪意のある可能性のある PowerShell コマンドを実行したことを示します。 これは、アカウントがハッキングされ、悪意ある行為がその他にも行われたことを、ほぼ間違いなく示しています。このアカウントは、警告の説明文に記載されます。 攻撃者はしばしば PowerShell を使用して、ディスク上に生成物を残さないよう、悪意あるペイロードをメモリ内で実行します。これは、ウイルス スキャナーなどのディスクに基づくセキュリティ機構によって検出されることを避けるためです。 疑わしい PowerShell コマンド アラートと組み合わされた、疑わしい Azure AD サインイン アラートの順列は次のとおりです。

- **特殊な場所へのあり得ない移動が、疑わしい PowerShell コマンド ラインにつながる**

- **未知の場所からのサインイン イベントが、疑わしい PowerShell コマンド ラインにつながる**

- **感染したデバイスからのサインイン イベントが、疑わしい PowerShell コマンド ラインにつながる**

- **匿名 IP アドレスからのサインイン イベントが、疑わしい PowerShell コマンド ラインにつながる**

- **資格情報が漏洩したユーザーからのサインイン イベントが、疑わしい PowerShell コマンド ラインにつながる**

## <a name="malware-c2-or-download"></a>マルウェア C2 またはダウンロード

### <a name="new-beacon-pattern-detected-by-fortinet-following-multiple-failed-user-sign-ins-to-a-service"></a>新しい情報!  サービスへのユーザー サインインが複数回失敗するイベントに続いて、Fortinet によりビーコン送信パターンを検出

このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK tactics:** Initial Access (最初のアクセス), Command and Control (命令と制御)

**MITRE ATT&CK techniques:** Valid Account (有効なアカウント) (T1078), Non-Standard Port (非標準ポート) (T1571), T1065 (廃止)

**データ コネクター のソース:** Azure Sentinel (スケジュールされた分析のルール)、Microsoft Cloud App Security

**説明**: この Fusion インシデントは、組織の関連エンティティを使用したサービスへのユーザー サインインが複数回失敗するイベントが発生したのに続いて、組織内部の IP アドレスから外部のアドレスへの通信に、ビーコン送信を示すパターンが検出されたことを示しています。 これら 2 つのイベントの組み合わせは、マルウェアに感染した可能性、または侵入を受けてされてデータ流出を引き起こしているホストが存在する可能性を示しています。 

### <a name="new-beacon-pattern-detected-by-fortinet-following-suspicious-azure-ad-sign-in"></a>新しい情報!  Beacon pattern detected by Fortinet following suspicious Azure AD sign-in

このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK tactics:** Initial Access (最初のアクセス), Command and Control (命令と制御)

**MITRE ATT&CK techniques:** Valid Account (有効なアカウント) (T1078), Non-Standard Port (非標準ポート) (T1571), T1065 (廃止)

**データ コネクタのソース:** Azure Sentinel (スケジュールされた分析のルール)、Azure Active Directory Identity Protection

**説明:** この Fusion インシデントは、Azure AD への不審なユーザー サインインに続いて、組織内部の IP アドレスから外部のアドレスへの通信に、ビーコン送信を示すパターンが検出されたことを示しています。 これら 2 つのイベントの組み合わせは、マルウェアに感染した可能性、または侵入を受けてされてデータ流出を引き起こしているホストが存在する可能性を示しています。 Fortinet で検出するビーコン送信パターンの警告で、Azure AD への不審なサインインに対する警告を伴うものを、次に挙げます。   

- **通常と異なる場所へのあり得ない移動と、それに続く Fortinet のビーコン送信パターン検出**

- **なじみの薄い場所からのサインインと、それに続く Fortinet のビーコン送信パターン検出**

- **感染したデバイスからのサインインと、それに続く Fortinet のビーコン送信パターン検出**

- **匿名 IP アドレスによるサインインと、それに続く Fortinet のビーコン送信パターン検出**

- **サインイン情報が漏洩したユーザーによるサインインと、それに続く Fortinet のビーコン送信パターン検出**

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>TOR 匿名化サービスに対するネットワーク要求の後に、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィックが続きます。
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** コマンドと制御

**MITRE ATT&CK の手法:** 暗号化されたチャネル (T1573)、プロキシ (T1090)

**データ コネクタのソース:** Microsoft Defender for Endpoint (旧称 MDATP)、Palo Alto Networks 

**説明:** この種類の Fusion インシデントは、TOR 匿名化サービスへの送信接続要求が行われ、その後、Palo Alto Networks ファイアウォールによって異常な受信アクティビティが検出されたことを示しています。 これは、恐らく攻撃者が既にネットワークへのアクセスを獲得しており、自分の行為または意図を隠そうとしていることを示唆しています。 このパターンに従う TOR ネットワークへの接続は、マルウェアのコマンド アンド コントロール アクティビティ、追加のマルウェアのダウンロード要求、またはリモートの対話型アクセスを確立する攻撃者を示している可能性があります。 Palo Alto ログでは、Azure Sentinel は[脅威ログ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)に焦点を当てており、脅威 (疑わしいデータ、ファイル、フラッド、パケット、スキャン、スパイウェア、URL、ウイルス、脆弱性、wildfire ウイルス、wildfire) が許可されている場合、トラフィックは疑わしいと見なされます。 アラートのその他の詳細について、Fusion インシデントの説明に記載されている[脅威/コンテンツ タイプ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)に対応する Palo Alto 脅威ログも参照してください。

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>承認されていないアクセス試行の履歴がある IP への送信接続の後に、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィックが続く
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** コマンドと制御

**MITRE ATT&CK の手法:** 適用なし

**データ コネクタのソース:** Microsoft Defender for Endpoint (旧称 MDATP)、Palo Alto Networks 

**説明:** この種類の Fusion インシデントは、未許可のアクセス試行履歴がある IP アドレスへの送信接続が確立され、その後、Palo Alto Networks ファイアウォールによって異常なアクティビティが検出されたことを示しています。 これは、恐らく攻撃者が既にネットワークへのアクセスを獲得したことを示唆しています。 このパターンに従う接続の試行は、マルウェアのコマンド アンド コントロール アクティビティ、追加のマルウェアのダウンロード要求、またはリモートの対話型アクセスを確立する攻撃者を示している可能性があります。 Palo Alto ログでは、Azure Sentinel は[脅威ログ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)に焦点を当てており、脅威 (疑わしいデータ、ファイル、フラッド、パケット、スキャン、スパイウェア、URL、ウイルス、脆弱性、wildfire ウイルス、wildfire) が許可されている場合、トラフィックは疑わしいと見なされます。 アラートのその他の詳細について、Fusion インシデントの説明に記載されている[脅威/コンテンツ タイプ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)に対応する Palo Alto 脅威ログも参照してください。

## <a name="persistence"></a>永続化
(新しい脅威の分類)

### <a name="new-rare-application-consent-following-suspicious-sign-in"></a>新しい情報!  不審なサインインと、それに続く、通常見られないアプリケーションへの同意

このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK tactics:** Persistence (持続性), Initial Access (最初のアクセス)

**MITRE ATT&CK techniques:** Create Account (アカウント作成) (T1136), Valid Account (有効なアカウント) (T1078)

**データ コネクタのソース:** Azure Sentinel (スケジュールされた分析のルール)、Azure Active Directory Identity Protection

**説明:** この Fusion インシデントは、関連する Azure AD アカウントへの不審なサインインに続いて、これまでそのようなことを全く、あるいはめったにしたことのないユーザーが、アプリケーションに同意を与えたことを示しています。 これは、悪意ある目的でアプリケーションにアクセスする、またはこれを操作するために、アカウントがハッキングされ、利用されたことを示唆しています。このアカウントは、Fusion インシデントの説明文に記載されます。  一般に、アプリケーションへの同意、サービス プリンシパルの追加、OAuth2PermissionGrant の追加は稀なイベントです。 攻撃者は、この方法で構成の変更を行って、当該システムにおける足場を確立あるいは維持している場合があります。 Azure AD への不審なサインインに対する警告で、通常見られないアプリケーションへの同意に対する警告を伴うものを、次に挙げます。

- **通常と異なる場所へのあり得ない移動と、それに続く、通常見られないアプリケーションへの同意**

- **なじみの薄い場所からのサインインと、それに続く、通常見られないアプリケーションへの同意**

- **感染したデバイスからのサインインと、それに続く、通常見られないアプリケーションへの同意**

- **匿名 IP アドレスによるサインインと、それに続く、通常見られないアプリケーションへの同意**

- **サインイン情報が漏洩したユーザーによるサインインと、それに続く、通常見られないアプリケーションへの同意**

## <a name="ransomware"></a>ランサムウェア

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後のランサムウェア実行

**MITRE ATT&CK の方針:** 初期アクセス、影響

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、影響を及ぼすためのデータの暗号化 (T1486)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、ランサムウェア攻撃を示す異常なユーザー ビヘイビアーが検出されたことを示しています。 この情報は、Fusion インシデントの説明に示されているアカウントが侵害され、データ所有者を脅迫する、またはデータ所有者によるデータへのアクセスを拒否する目的でデータを暗号化するために使用されたことを示しています。 ランサムウェア実行アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、クラウド アプリ内のランサムウェア**

- **知らない場所からサインインされた後の、クラウド アプリ内のランサムウェア**

- **感染したデバイスからサインインされた後の、クラウド アプリ内のランサムウェア**

- **送信元のわからない IP アドレスからサインインされた後の、クラウド アプリ内のランサムウェア**

- **資格情報が漏洩したユーザーからサインインされた後の、クラウド アプリ内のランサムウェア**

### <a name="multiple-alerts-possibly-related-to-ransomware-activity-detected-public-preview"></a>ランサムウェア アクティビティに関連する可能性のある複数のアラートの検出 (パブリック プレビュー)

次のデータ ソースからさまざまな種類の複数のアラートが検出され、ランサムウェア アクティビティに関連する可能性がある場合、Azure Sentinel によってインシデントが生成されます。

- [Azure Defender (Azure Security Center)](connect-azure-security-center.md)
- [Microsoft Defender for Endpoint](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender for Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Cloud App Security](./data-connectors-reference.md#microsoft-cloud-app-security-mcas)
- [Azure Sentinel のスケジュール化された分析ルール](detect-threats-built-in.md#scheduled)。 Fusion では、戦術情報を含むスケジュール化された分析ルールのみが考慮されます。

そのような Fusion インシデントは、**Multiple alerts possibly related to Ransomware activity detected (ランサムウェア アクティビティに関連する可能性のある複数のアラートの検出)** という名前が付けられ、関連するアラートが特定の期間内に検出され、攻撃の **実行** と **防衛回避** ステージに関連している場合に生成されます。

たとえば、Azure Sentinel は、特定の期間内に同じホストで次のアラートがトリガーされると、ランサムウェア アクティビティの可能性に関するインシデントを生成します。

- Azure Sentinel のスケジュールされたアラート (情報): **Windows エラーと警告イベント**
- Azure Defender (中程度): **'GandCrab' ransomware was prevented ("GandCrab" ランサムウェアが阻止された)**
- Microsoft Defender for Endpoint (情報): **'Emotet' malware was detected ("Emotet" マルウェアが検出された)**
- Azure Defender (低い): **'Tofsee' backdoor was detected ("Tofsee" バックドアが検出された)**
- Microsoft Defender for Endpoint (情報): **'Parite' malware was detected ("Parite" マルウェアが検出された)**

## <a name="remote-exploitation"></a>リモートの悪用

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>疑わしい攻撃フレームワーク使用後の、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィック
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、実行、侵入拡大、特権エスカレーション

**MITRE ATT&CK の手法:** 公開アプリケーションの悪用 (T1190)、クライアント実行の悪用 (T1203)、リモート サービスの悪用 (T1210)、特権エスカレーションの悪用 (T1068)

**データ コネクタのソース:** Microsoft Defender for Endpoint (旧称 MDATP)、Palo Alto Networks 

**説明:** この種類の Fusion インシデントは、Metasploit などの攻撃フレームワークの使用に似た、プロトコルの非標準の使用が検出され、その後、疑わしい受信アクティビティが Palo Alto Networks ファイアウォールによって検出されたことを示しています。 これは、攻撃者がネットワーク リソースへのアクセスを取得するためにサービスを悪用したこと、または攻撃者が既にアクセスを取得していて、利用可能なシステム/サービスをさらに悪用して侵入拡大や特権エスカレーションを行おうとしていることを示す最初の兆候の場合があります。 Palo Alto ログでは、Azure Sentinel は[脅威ログ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)に焦点を当てており、脅威 (疑わしいデータ、ファイル、フラッド、パケット、スキャン、スパイウェア、URL、ウイルス、脆弱性、wildfire ウイルス、wildfire) が許可されている場合、トラフィックは疑わしいと見なされます。 アラートのその他の詳細について、Fusion インシデントの説明に記載されている[脅威/コンテンツ タイプ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)に対応する Palo Alto 脅威ログも参照してください。

## <a name="resource-hijacking"></a>リソースの乗っ取り
(新しい脅威の分類)

### <a name="new-suspicious-resource--resource-group-deployment-by-a-previously-unseen-caller-following-suspicious-azure-ad-sign-in"></a>新しい情報!  Azure AD への不審なサインインと、それに続く、これまで見られなかった呼び出し元によるリソースまたはリソースグループの不審なデプロイ
このケースでは、**スケジュールされた分析のルール** に基づいて出される警告を利用します。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、影響

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、リソース ハイジャック (T1496)

**データ コネクタのソース:** Azure Sentinel (スケジュールされた分析のルール)、Azure Active Directory Identity Protection

**説明**: この Fusion インシデントは、しばらく使用されていなかったプロパティを使用して Azure AD アカウントへの不審なサインインが行われたのに続いて、ユーザーが Azure リソースまたはリソースグループをデプロイするという、通常見られない行為があったことを示しています。 これは、攻撃者がユーザー アカウントをハッキングし、その後、悪意のある目的でリソースまたはリソース グループをデプロイしようとした可能性を示しています。
Azure AD への不審なサインインに対する警告で、これまで見られなかった呼び出し元によるリソースまたはリソース グループの不審なデプロイに対する警告を伴うものを、次に挙げます。

- **通常と異なる場所へのあり得ない移動と、それに続く、これまで見られなかった呼び出し元によるリソースまたはリソース グループの不審なデプロイ**

- **なじみの薄い場所からのサインインと、それに続く、これまで見られなかった呼び出し元によるリソースまたはリソース グループの不審なデプロイ**

- **感染したデバイスからのサインインと、それに続く、これまで見られなかった呼び出し元によるリソースまたはリソース グループの不審なデプロイ**

- **匿名 IP アドレスによるサインインと、それに続く、これまで見られなかった呼び出し元によるリソースまたはリソース グループの不審なデプロイ**

- **サインイン情報が漏洩したユーザーによるサインインと、それに続く、これまで見られなかった呼び出し元によるリソースまたはリソース グループの不審なデプロイ**

## <a name="next-steps"></a>次のステップ

高度なマルチステージ攻撃の検出に関する詳細を学習したので、自分のデータや潜在的な脅威を視覚化する方法を学習することができる以下のクイックスタートにも関心を持たれるかもしれません。[Azure Sentinel の概要](get-visibility.md)

自分用として作成したインシデントを調査する準備ができたら、次のチュートリアルをご覧ください。[Azure Sentinel でインシデントを調査する](investigate-cases.md)