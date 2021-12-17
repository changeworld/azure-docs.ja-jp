---
title: Microsoft Sentinel での高度なマルチステージ攻撃の検出
description: Microsoft Sentinel の Fusion テクノロジを利用して、アラートによる疲労を減らし、高度なマルチステージ攻撃の検出に基づく、アクションにつながるインシデントを作成します。
services: sentinel
documentationcenter: na
author: yelevin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 808910eb32973a3d567960c98364d1d60036d673
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721140"
---
# <a name="advanced-multistage-attack-detection-in-microsoft-sentinel"></a>Microsoft Sentinel での高度なマルチステージ攻撃の検出

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> 一部の Fusion 検出 (以下でそのように示されているものを参照) は、現在 **プレビュー** 段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel では、スケーラブルな機械学習アルゴリズムに基づく関連付けエンジンである Fusion を使用して、キル チェーンのさまざまな段階で観察される異常な動作と疑わしいアクティビティの組み合わせを特定することによって、マルチステージ攻撃 (持続的標的型攻撃または APT とも呼ばれる) を自動的に検出します。 これらの検出を基に、Microsoft Sentinel では、Microsoft Sentinel 以外では検出が困難であろうインシデントが生成されます。 このインシデントは、2 つ以上のアラートまたはアクティビティで構成されています。 設計上、このようなインシデントでは、ボリュームが低、忠実度が高、重大度が高になります。

この検出テクノロジはご利用の環境によってカスタマイズされるため、[誤検知](false-positives.md)率を減らすだけでなく、情報が制限されているか、不足している攻撃も検出できます。

Fusion は、高度なマルチステージ攻撃を検出するためにさまざまな製品からの複数の信号を関連付けるため、成功した Fusion 検出は、Microsoft Sentinel の **[インシデント]** ページに **Fusion インシデント** として表示され、**アラート** としては表示されず、 **[ログ]** の *[インシデント]* テーブルに格納され、 *[セキュリティ アラート]* テーブルには格納されません。

### <a name="configure-fusion"></a>Fusion を構成する

Fusion は、**高度なマルチステージ攻撃の検出** と呼ばれる[分析ルール](detect-threats-built-in.md#view-built-in-detections)として、Microsoft Sentinel で既定で有効になります。 ルールの状態を表示および変更したり、Fusion ML モデルに含めるソース シグナルを構成したり、Fusion 検出から環境に適用できない特定の検出パターンを除外したりすることができます。 [Fusion ルールの構成方法](configure-fusion-rules.md)についての記事を参照してください。

> [!NOTE]
> 現在、Microsoft Sentinel では 30 日間の履歴データを使用して Fusion エンジンの機械学習アルゴリズムをトレーニングしています。 このデータは、機械学習パイプラインを通過するときに、Microsoft のキーを使用して常に暗号化されます。 ただし、Microsoft Sentinel ワークスペースで CMK を有効にした場合、トレーニング データは[カスタマー マネージド キー (CMK)](customer-managed-keys.md) を使用して暗号化されません。 Fusion をオプトアウトするには、 **[Microsoft Sentinel]** \> **[構成]** \> **[分析]\> [アクティブな規則] の順にクリックし、** **[Advanced Multistage Attack Detection]\(高度なマルチステージ攻撃の検出\)** 規則を右クリックし、 **[無効にする]** を選択します。

## <a name="fusion-for-emerging-threats"></a>新しい脅威用の Fusion

> [!IMPORTANT]
>
> - 現在、新しい脅威用の Fusion ベースの検出は **プレビュー段階** です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

セキュリティ イベントの量は増え続けており、攻撃の範囲や巧妙さがますます高まっています。 既知の攻撃シナリオは定義できますが、環境における新たな脅威と未知の脅威についてはどうでしょうか。  

Microsoft Sentinel の ML を利用した Fusion エンジンを使用すると、**拡張 ML 分析** を適用して **より広範な異常のシグナル** を関連付けることにより、アラートによる負担を低く保ちながら、環境内で **新たな未知の脅威** を見つけるために役立ちます。

Fusion エンジンの ML アルゴリズムでは、既存の攻撃から継続的に学習し、セキュリティ アナリストの考えに基づいて分析が適用されます。 その結果、以前は検出されなかった脅威が、環境全体のキル チェーンで数百万の異常な動作から検出できるため、攻撃者の一歩先を行く状態を維持できます。

**新しい脅威用の Fusion** は、次のソースからのデータの収集と分析をサポートします。

- [すぐに使える異常検出](soc-ml-anomalies.md)
- Microsoft 製品からのアラート:
  - Azure Active Directory Identity Protection
  - Microsoft Defender for Cloud
  - Microsoft Defender for IoT
  - Microsoft 365 Defender
  - Microsoft Defender for Cloud Apps
  - Microsoft Defender for Endpoint
  - Microsoft Defender for Identity
  - Microsoft Defender for Office 365
- [**スケジュールされた分析ルールからのアラート**](configure-fusion-rules.md#configure-scheduled-analytics-rules-for-fusion-detections) ([組み込み](detect-threats-built-in.md#scheduled)と [セキュリティ アナリストが作成したアラート](detect-threats-custom.md)の両方)。 Fusion によって使用されるためには、分析ルールにキル チェーン (戦術) とエンティティ マッピング情報が含まれている必要があります。

新しい脅威用の Fusion を機能させるために、上記のすべてのデータ ソースを接続している必要はありません。 ただし、接続されているデータ ソースが多くなるほど、範囲が広くなり、Fusion が検出する脅威はさらに増えます。

Fusion エンジンの関連付けによって、新たな脅威が検出されると、"**Fusion によって検出される可能性のあるマルチステージ攻撃アクティビティ**" という重大度の高いインシデントが Microsoft Sentinel ワークスペースの *incidents* テーブルに生成されます。

## <a name="fusion-for-ransomware"></a>ランサムウェア用の Fusion

Microsoft Sentinel の Fusion エンジンでは、次のデータ ソースからさまざまな種類の複数のアラートを検出したときにインシデントを生成し、ランサムウェア アクティビティに関連している可能性があることを判断します。

- [Microsoft Defender for Cloud](connect-defender-for-cloud.md)
- [Microsoft Defender for Endpoint](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender for Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Defender for Cloud Apps](./data-connectors-reference.md#microsoft-defender-for-cloud-apps)
- [Microsoft Sentinel のスケジュール化された分析ルール](detect-threats-built-in.md#scheduled)。 Fusion では、戦術とマップ済みエンティティを含むスケジュールされた分析ルールのみが考慮されます。

そのような Fusion インシデントは、**Multiple alerts possibly related to Ransomware activity detected (ランサムウェア アクティビティに関連する可能性のある複数のアラートの検出)** という名前が付けられ、関連するアラートが特定の期間内に検出され、攻撃の **実行** と **防衛回避** ステージに関連している場合に生成されます。

たとえば、Microsoft Sentinel では、特定の期間内に同じホストで次のアラートがトリガーされると、ランサムウェア アクティビティの可能性に関するインシデントを生成します。

| アラート: | source | 重大度 |
| ----- | ------ | -------- |
| **Windows エラーおよび警告イベント** | Microsoft Sentinel のスケジュール化された分析ルール | informational |
| **'GandCrab' ランサムウェアが回避されました** | Microsoft Defender for Cloud | 中 |
| **'Emotet' マルウェアが検出されました** | Microsoft Defender for Endpoint | informational |
| **'Tofsee' のバックドアが検出されました** | Microsoft Defender for Cloud | low |
| **'Parite' マルウェアが検出されました** | Microsoft Defender for Endpoint | informational |

## <a name="scenario-based-fusion-detections"></a>シナリオベースの Fusion の検出

次のセクションでは、Microsoft Sentinel によって Fusion 関連付けエンジンを使用して検出された、[シナリオベースのマルチステージ攻撃](fusion-scenario-reference.md)の種類を脅威の分類別に示します。

これらの Fusion による攻撃の検出シナリオを有効にするには、これらに関連付けられたデータ ソースが Log Analytics ワークスペースに注入される必要があります。 各シナリオとそれに関連付けられているデータ ソースの詳細については、次の表のリンクを選択してください。

> [!NOTE]
> これらのシナリオの一部は **プレビュー** 段階です。 そのように明記されています。

| 脅威の分類  | シナリオ  |
| -------- | -------- |
| **Compute リソース プールの不正使用**      | <ul><li>(プレビュー) [疑わしい Azure Active Directory サインインの後の複数の VM 作成アクティビティ](fusion-scenario-reference.md#multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in) |
| **資格情報のアクセス**           | <ul><li>(プレビュー) [不審なサインインに続き、ユーザーによりパスワードが複数回リセットされる](fusion-scenario-reference.md#multiple-passwords-reset-by-user-following-suspicious-sign-in) <li>(プレビュー) [<br>Azure AD へのサインインに複数回失敗した IP アドレスによる](fusion-scenario-reference.md#suspicious-sign-in-coinciding-with-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins) Palo Alto VPN へのサインインの成功と同時に行われる、不審なサインイン |
| **サインイン情報の不正取得**       | <ul><li>[疑わしいサインインの後の悪意のある資格情報盗難ツールの実行](fusion-scenario-reference.md#malicious-credential-theft-tool-execution-following-suspicious-sign-in)    <li>[疑わしいサインインの後の資格情報盗難アクティビティの疑い](fusion-scenario-reference.md#suspected-credential-theft-activity-following-suspicious-sign-in)      |
| **暗号通貨マイニング**               | <ul><li>[疑わしいサインインの後の暗号化マイニング アクティビティ](fusion-scenario-reference.md#crypto-mining-activity-following-suspicious-sign-in)          |
| **データの破壊**            | <ul><li>[疑わしい Azure AD サインインの後の大量のファイル削除](fusion-scenario-reference.md#mass-file-deletion-following-suspicious-azure-ad-sign-in)     <li>(プレビュー) [<br>Cisco ファイアウォール アプライアンスにブロックされている IP アドレス](fusion-scenario-reference.md#mass-file-deletion-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance)による Azure AD へのサインインと、それに続くファイルの大規模な削除        <li>(プレビュー) [<br>Azure AD へのサインインに複数回失敗した IP アドレスによる](fusion-scenario-reference.md#mass-file-deletion-following-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins) Palo Alto VPN へのサインインと、それに続くファイルの大規模な削除        <li>(プレビュー) [疑わしい Azure AD サインインの後の疑わしい電子メール削除アクティビティ](fusion-scenario-reference.md#suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in) |
| **データ窃盗**           | <ul><li>(プレビュー) [管理者アカウントの新しい活動が見られなくなった後のメール転送活動](fusion-scenario-reference.md#mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently)      <li>[疑わしい Azure AD サインインの後の大量のファイルのダウンロード](fusion-scenario-reference.md#mass-file-download-following-suspicious-azure-ad-sign-in)       <li>(プレビュー) [<br>Cisco ファイアウォール アプライアンスにブロックされている IP アドレス](fusion-scenario-reference.md#mass-file-download-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance)による Azure AD へのサインインと、それに続くファイルの大規模なダウンロード       <li>(プレビュー) [これまで見られなかった IP アドレスによる SharePoint ファイルの操作と同時に発生する、ファイルの大規模なダウンロード](fusion-scenario-reference.md#mass-file-download-coinciding-with-sharepoint-file-operation-from-previously-unseen-ip)        <li>[疑わしい Azure AD サインインの後の大量のファイル共有](fusion-scenario-reference.md#mass-file-sharing-following-suspicious-azure-ad-sign-in)         <li>(プレビュー) [疑わしい Azure AD サインインの後の複数の Power BI レポート共有アクティビティ](fusion-scenario-reference.md#multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in)         <li>[疑わしい Azure AD サインインの後の Office 365 メールボックスからの情報の流出](fusion-scenario-reference.md#office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in)        <li>(プレビュー) [マルウェアの検出と、それに続く、これまで見られなかった IP アドレスによる SharePoint ファイルの操作](fusion-scenario-reference.md#sharepoint-file-operation-from-previously-unseen-ip-following-malware-detection)         <li>(プレビュー) [疑わしい Azure AD サインインの後の疑わしい受信トレイ操作ルールの設定](fusion-scenario-reference.md#suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in)        <li>(プレビュー) [疑わしい Azure AD サインインの後の疑わしい Power BI レポート共有](fusion-scenario-reference.md#suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in)  |
| **サービス拒否**           | <ul><li>(プレビュー) [Azure AD への不審なサインインと、それに続く、複数の VM の削除操作](fusion-scenario-reference.md#multiple-vm-deletion-activities-following-suspicious-azure-ad-sign-in)          |
| **侵入拡大**            | <ul><li>[疑わしい Azure AD サインインの後の Office 365 偽装](fusion-scenario-reference.md#office-365-impersonation-following-suspicious-azure-ad-sign-in)      <li>(プレビュー) [疑わしい Azure AD サインインの後の疑わしい受信トレイ操作ルールの設定](fusion-scenario-reference.md#suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in-1)        |
| **悪意のある管理アクティビティ**     | <ul><li>[疑わしい Azure AD サインインの後のクラウド アプリでの疑わしい管理行為](fusion-scenario-reference.md#suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in)    <li>(プレビュー) [管理者アカウントの新しい活動が見られなくなった後のメール転送活動](fusion-scenario-reference.md#mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently-1)          |
| **正当なプロセスによる悪意のある実行<br>**    | <ul><li>(プレビュー) [PowerShell で疑わしいネットワーク接続が行われた後に続き、<br>Palo Alto Networks ファイアウォールによって異常なトラフィックのフラグが設定された](fusion-scenario-reference.md#powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)   <li>(プレビュー) [疑わしいリモート WMI 実行に続く、<br>Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィック](fusion-scenario-reference.md#suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)   <li>[疑わしいサインインの後の疑わしい PowerShell コマンド ライン](fusion-scenario-reference.md#suspicious-powershell-command-line-following-suspicious-sign-in)          |
| **マルウェア C2 またはダウンロード**      | <ul><li>(プレビュー) [サービスへのユーザー サインインが複数回失敗するイベントに続いて、Fortinet によりビーコン送信パターンを検出](fusion-scenario-reference.md#beacon-pattern-detected-by-fortinet-following-multiple-failed-user-sign-ins-to-a-service)     <li>(プレビュー) [Azure AD への不審なサインインに続く、Fortinet によるビーコン送信パターンの検出](fusion-scenario-reference.md#beacon-pattern-detected-by-fortinet-following-suspicious-azure-ad-sign-in)       <li>(プレビュー) [TOR 匿名化サービスに対するネットワーク要求の後に続く、<br>Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィック](fusion-scenario-reference.md#network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)       <li>(プレビュー) [承認されていないアクセス試行の履歴がある IP への送信接続の後に続く、<br>Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィック](fusion-scenario-reference.md#outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)    |
| **永続化**                 | <ul><li>(プレビュー) [不審なサインインと、それに続く、通常見られないアプリケーションへの同意](fusion-scenario-reference.md#rare-application-consent-following-suspicious-sign-in)         |
| **ランサムウェア**                  | <ul><li>[疑わしい Azure AD サインインの後のランサムウェア実行](fusion-scenario-reference.md#ransomware-execution-following-suspicious-azure-ad-sign-in)          |
| **リモートの悪用**         | <ul><li>(プレビュー) [疑わしい攻撃フレームワーク使用後の、<br>Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィック](fusion-scenario-reference.md#suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)          |
| **リソースの乗っ取り**          | <ul><li>(プレビュー) [Azure AD への不審なサインインと、それに続く、<br>これまで見られなかった呼び出し元によるリソースまたはリソース グループの不審なデプロイ](fusion-scenario-reference.md#suspicious-resource--resource-group-deployment-by-a-previously-unseen-caller-following-suspicious-azure-ad-sign-in)          |
|

## <a name="next-steps"></a>次のステップ

Fusion の高度なマルチステージ攻撃の検出に関する次の詳細をご覧ください。

- [Fusion のシナリオベースの攻撃検出](fusion-scenario-reference.md)の詳細についてご覧ください。
- [Fusion ルールの構成方法](configure-fusion-rules.md)に関する記事をご覧ください。

高度なマルチステージ攻撃の検出に関する詳細を学習したので、自分のデータや潜在的な脅威を視覚化する方法を学習することができる以下のクイックスタートにも関心を持たれるかもしれません。[Microsoft Sentinel の使用を開始する](get-visibility.md)。

作成されたインシデントを調査する準備ができたら、次のチュートリアルをご覧ください。[Microsoft Sentinel を使用してインシデントを調査する](investigate-cases.md)。
