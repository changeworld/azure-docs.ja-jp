---
title: Azure AD Connect Health の AD FS の危険な IP のレポート ワークブック | Microsoft Docs
description: Azure Monitor ブックを使用した Azure Active Directory Connect Health AD FS 危険な IP レポートについて説明します。
services: active-directory
documentationcenter: ''
ms.reviewer: ''
author: billmath
manager: karen444
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/14/2021
ms.author: billmath
ms.custom: ''
ms.collection: ''
ms.openlocfilehash: 8463da51788c7faa680f42aaeb1999c174419366
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478424"
---
# <a name="risky-ip-report-workbook"></a>危険な IP レポートブック 
> [!NOTE]
> 危険な IP レポートブックを使用するには、[診断設定] ブレードで "ADFSSignInLogs" を有効にする必要があります。 これは、Connect Health を通じて Azure AD に送信される AD FS サインインのログ分析ストリームです。 Azure AD での AD FS サインイン情報の詳細については、こちらのドキュメントを参照してください。

AD FS のお客様は、エンド ユーザーが Microsoft 365 などの SaaS アプリケーションにアクセスするための認証サービスを提供する目的で、パスワード認証エンドポイントをインターネットに公開する場合があります。 この場合、悪意のあるアクターが AD FS システムへのログインを試みて、エンド ユーザーのパスワードを推測し、アプリケーションのリソースにアクセスする可能性があります。 Windows Server 2012 R2 の AD FS 以降、これらの種類の攻撃を防止するためのエクストラネット アカウント ロックアウト機能が用意されています。 これよりも古いバージョンを使用している場合は、AD FS システムを Windows Server 2016 にアップグレードすることを強くお勧めします。 <br />

さらに、単一の IP アドレスから複数のユーザーに対してログイン試行が複数回実行される可能性もあります。 このような場合、ユーザーあたりの試行回数が AD FS のアカウント ロックアウト保護のしきい値に達しない可能性があります。 Azure AD Connect Health では、この状態を検出し、その発生時に管理者に通知する "危険な IP のレポート" が提供されるようになりました。 このレポートの主要な利点を次に示します。 
- 失敗したパスワードベースのログインのしきい値を超える IP アドレスの検出
- パスワードの間違いまたはエクストラネットのロックアウト状態が原因で失敗したログインのサポート
- Azure アラートを使用したアラートの有効化をサポート
- 組織のセキュリティ ポリシーに適合するカスタマイズ可能なしきい値設定
- カスタマイズ可能なクエリと拡張された視覚化による詳細な分析
- 以前の危険な IP レポートから、機能が拡張されました。以前のバージョンは 2022 年 1 月 24 日以降、非推奨となる予定です。

## <a name="requirements"></a>要件
1.  AD FS 用の Connect Health がインストールされ、最新のエージェントに更新されます。
2.  "ADFSSignInLogs" ストリームが有効になっている Log Analytics ワークスペース。
3.  Azure AD Monitor ブックを使用する権限。 ブックを使用するには、次のものが必要です。
- プレミアム (P1 または P2) ライセンスがある Azure Active Directory テナント。
- Azure AD の Log Analytics ワークスペースと次のロールへのアクセス (Azure AD ポータルから Log Analytics にアクセスする場合): セキュリティ管理者、セキュリティ閲覧者、レポート閲覧者、グローバル管理者


## <a name="what-is-in-the-report"></a>レポートの内容
危険な IP レポートブックは、ADFSSignInLogs ストリームのデータを基にしています。また、既存のクエリを使用して、危険な IP を迅速に視覚化して分析することができます。 これらのパラメーターは、しきい値の数に合わせて構成およびカスタマイズできます。 ブックはクエリに基づいて構成することもでき、各クエリは組織のニーズに基づいて更新および変更できます。

危険な IP ブックは ADFSSignInLogs からのデータを分析し、パスワード スプレーやパスワードのブルート フォース攻撃を検出するのに役立ちます。 ブックには 2 つの部分があります。 最初の部分である "危険な IP 分析" は、指定されたエラーしきい値と検出ウィンドウの長さに基づいて、危険な IP アドレスを識別します。 2 番目の部分では、選択した IP のサインインの詳細とエラー数を示します。

[ブックの場所が表示されているスクリーンショット。![](./media/how-to-connect-health-adfs-risky-ip-workbook/workbook-template-overview-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/workbook-template-overview-1.png#lightbox)

- [ワークスペース] では、リスクの高い IP の場所をすばやく分析するためのマップの視覚化と領域の内訳が表示されます。
- 危険な IP の詳細テーブルは、過去の危険な IP のレポートの機能に似ています。 テーブル内のフィールドの詳細については、以下のセクションを参照してください。
- 危険な IP タイムラインに、タイムライン ビューで要求内にある異常または急増のクイック ビューを表示します
- IP 別のサインインの詳細とエラー数により、詳細テーブルでは、IP またはユーザーによって詳細なフィルターが適用されたビューを使用できるようになります。 

危険な IP のレポート テーブルの各項目は、指定されたしきい値を超える、失敗した AD FS サインイン アクティビティに関する集計情報を示します。 次の情報が提供されます。[![列ヘッダーが強調表示されている危険な IP のレポートを示すスクリーンショット。](./media/how-to-connect-health-adfs-risky-ip-workbook/risky-ip-table-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/risky-ip-table-1.png#lightbox)

| レポート アイテム | 説明 |
| ------- | ----------- |
| 検出ウィンドウの開始時刻 | Azure Portal の現地時間に基づく、検出時間枠の開始時のタイム スタンプを表示します。<br /> 日単位のイベントは、すべて UTC の午前 0 時に生成されます。 <br />時間単位のイベントのタイムスタンプは、毎時 0 分の値に丸められます。 エクスポートされたファイルの "firstAuditTimestamp" から、最初のアクティビティの開始時刻を見つけることができます。 |
| 検出期間の長さ | 検出時間枠の種類を示します。 集計トリガーの種類は、"1 時間あたり" または "1 日あたり" です。 これは、高頻度のブルート フォース攻撃と、試行回数が 1 日の中で分散している低速の攻撃を検出するのに役立ちます。 |
| IP アドレス | 間違ったパスワードまたはエクストラネット ロックアウト サインイン アクティビティが発生した単一の危険な IP アドレス。 これは、IPv4 アドレスまたは IPv6 アドレスです。 |
| 間違ったパスワードのエラー回数 (50126) | 検出時間枠の間に特定の IP アドレスで発生した、間違ったパスワード エラーの数。 間違ったパスワード エラーは、特定のユーザーで複数回発生する可能性があります。 これには期限切れのパスワードが原因で失敗した試行は含まれないことに注意してください。 |
| エクストラネットのロックアウトのエラー回数 (30030) | 検出時間枠の間に特定の IP アドレスで発生したエクストラネットのロックアウト エラーの数。 エクストラネットのロックアウト エラーは、特定のユーザーで複数回発生する可能性があります。 これは、エクストラネットのロックアウトが AD FS (バージョン 2012R2 以降) で構成されている場合にのみ表示されます。 <b>注</b> パスワードを使用したエクストラネット ログインを許可する場合は、この機能を有効にすることを強くお勧めします。 |
| 試行した一意のユーザー | 検出時間枠の間に特定の IP アドレスでログインを試行した、一意のユーザー アカウントの数。 これにより、単一ユーザーの攻撃パターンと複数ユーザーの攻撃パターンを区別するメカニズムが提供されます。  |

IP アドレスまたはユーザー名でレポートをフィルター処理して、危険な IP イベントごとのサインインの詳細を表示します。

## <a name="load-balancer-ip-addresses-in-the-list"></a>一覧のロード バランサーの IP アドレス
ロード バランサーが失敗したサインイン アクティビティを集計し、アラートのしきい値に達しました。 ロード バランサーの IP アドレスが表示されている場合は、外部ロード バランサーが要求を Web アプリケーション プロキシ サーバーに渡すときにクライアント IP アドレスを送信していない可能性が高くなっています。 転送クライアント IP アドレスを渡すようにロード バランサーを適切に構成してください。 

## <a name="configure-threshold-settings"></a>しきい値設定の構成
アラートのしきい値は、[しきい値の設定] で更新できます。 まず、システムには既定でしきい値が設定されています。 しきい値の設定は、1 時間または 1 日の検出時間によって設定できます。また、フィルターでカスタマイズすることもできます。

[![しきい値フィルター](./media/how-to-connect-health-adfs-risky-ip-workbook/threshold-settings-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/threshold-settings-1.png#lightbox)

| しきい値の項目 | 説明 |
| --- | --- |
| 無効なパスワードとエクストラネットのロックアウトのしきい値  | "間違ったパスワードの試行回数 + エクストラネットのロックアウトの回数" が **1 時間または 1 日** のしきい値を超えたときに、アクティビティをレポートしてアラート通知をトリガーするためのしきい値設定。|
| エクストラネットのロックアウト エラーのしきい値 | エクストラネットのロックアウトの回数が **1 時間または 1 日** のしきい値を超えたときに、アクティビティをレポートしてアラート通知をトリガーするためのしきい値設定。 既定値は 50 です。|

**時間または日** の検出期間の長さは、しきい値をカスタマイズするためのフィルターの上にあるトグル ボタンを使用して構成できます。 

## <a name="configure-notification-alerts-using-azure-monitor-alerts-through-the-azure-portal"></a>Azure portal で Azure Monitor アラートを使用して通知アラートを構成します。
[![Azure アラート ルール](./media/how-to-connect-health-adfs-risky-ip-workbook/azure-alerts-rule-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/azure-alerts-rule-1.png#lightbox)
1.  Azure portal で、検索バーで「Monitor」を検索し、Azure "Monitor" サービスに移動します。 左のメニューから [アラート] を選択した後、[+ 新しいアラート ルール] を選択します。 
2.  [アラート ルールの作成] ブレードで、次のようにします。
   * [スコープ]: [リソースの選択] をクリックし、監視する ADFSSignInLogs が含まれている Log Analytics ワークスペースを選択します。
   * 条件: [条件の追加] をクリックします。 シグナルの種類として [ログ] を選択し、監視サービスの場合は "Log analytics" を選択します。 [カスタム ログ検索] を選択します。 

3. アラートをトリガーする条件を構成します。 Connect Health 危険な IP レポートの電子メール通知を照合するには、次の手順に従います。
   * 次のクエリをコピーして貼り付け、エラー数のしきい値を指定します。 このクエリでは、指定されたエラーしきい値を超える IP の数が生成されます。

```
ADFSSignInLogs
| extend ErrorCode = ResultType
| where ErrorCode in ("50126", "300030")
| summarize badPasswordErrorCount = countif(ErrorCode == "50126"), extranetLockoutErrorCount = countif(ErrorCode == "300030") by IPAddress
| where extranetLockoutErrorCount > [TODO: put error count threshold here] 
```
または結合されたしきい値の場合:
```
badPasswordErrorCount + extranetLockoutErrorCount > [TODO: put error count threshold here] // Customized thresholds
```

> [!NOTE]
> このアラート ロジックは、エクストラネットのロックアウト エラーの少なくとも 1 つの IP が指定されたしきい値を超えた場合、または無効なパスワードとエクストラネットのロックアウト エラーの合計数が指定したしきい値を超えた場合にアラートをトリガーすることを意味します。 危険な IP を検出するためにクエリを評価する頻度を選択できます。


## <a name="faq"></a>よく寄せられる質問
**レポートにロード バランサーの IP アドレスが表示されるのはなぜですか?**  <br />
ロード バランサーの IP アドレスが表示されている場合は、外部ロード バランサーが要求を Web アプリケーション プロキシ サーバーに渡すときにクライアント IP アドレスを送信していない可能性が高くなっています。 転送クライアント IP アドレスを渡すようにロード バランサーを適切に構成してください。 

**IP アドレスをブロックするにはどうすればよいですか?**  <br />
特定した悪意のある IP アドレスをファイアウォールまたは Exchange のブロックに追加する必要があります。   <br />

**このレポートに項目が何も表示されないのはなぜですか?** <br />
- 診断設定では、' ADFSSignInLogs ' Log Analytics ストリームが有効になっていません。
- 失敗したサインイン アクティビティがしきい値の設定を超えていません。
- AD FS サーバー リストで "Health Service が最新ではありません" アラートがアクティブになっていないことを確認します。  [このアラートのトラブルシューティングを行う方法](how-to-connect-health-data-freshness.md)を確認してください
- AD FS ファームで監査が有効になっていません。

## <a name="next-steps"></a>次のステップ
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health エージェントのインストール](how-to-connect-health-agent-install.md)
