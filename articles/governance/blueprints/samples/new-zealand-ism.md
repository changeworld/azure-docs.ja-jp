---
title: New Zealand ISM Restricted ブループリント サンプル
description: New Zealand ISM Restricted ブループリント サンプルの概要です。 このブループリント サンプルは、お客様が特定のコントロールを評価するのに役立ちます。
ms.date: 03/22/2021
ms.topic: sample
ms.openlocfilehash: a52470ea45e6358007dc49122599e87091fbf8f7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802981"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>New Zealand ISM Restricted ブループリント サンプル

New Zealand ISM Restricted ブループリント サンプルでは、特定の [New Zealand Information Security Manual](https://www.nzism.gcsb.govt.nz/) コントロールの評価に役立つ、[Azure Policy](../../policy/overview.md) を使用したガバナンス ガードレールが提供されます。 このブループリントは、New Zealand ISM Restricted コントロールを実装する必要がある Azure でデプロイされたアーキテクチャのために、お客様が一連の主要なポリシーをデプロイするのに役立ちます。

## <a name="control-mapping"></a>コントロール マッピング

[Azure Policy のコントロール マッピング](../../policy/samples/new-zealand-ism.md)では、このブループリントに含まれるポリシーの定義について詳しく取り上げると共に、それらの定義が New Zealand Information Security Manual の **コントロール** にどのようにマッピングされるかについて詳しく説明します。 リソースはアーキテクチャに割り当てられると、割り当て済みのポリシー定義に違反していないかどうかを Azure Policy によって評価されます。 詳細については、[Azure Policy](../../policy/overview.md) に関するページをご覧ください。

## <a name="deploy"></a>配置

Azure Blueprints New Zealand ISM Restricted ブループリント サンプルをデプロイするには、以下の手順を実行する必要があります。

> [!div class="checklist"]
> - サンプルから新しいブループリントを作成する
> - サンプルのコピーを **発行済み** としてマークする
> - ブループリントのコピーを既存のサブスクリプションに割り当てる

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

### <a name="create-blueprint-from-sample"></a>サンプルからブループリントを作成する

最初に、サンプルをスターターとして使用して環境内に新しいブループリントを作成することにより、ブループリント サンプルを実装します。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[はじめに]** ページで、 _[ブループリントの作成]_ の下にある **[作成]** ボタンを選択します。

1. **New Zealand ISM Restricted** ブループリント サンプルを _[その他のサンプル]_ で検索し、 **[このサンプルを使用する]** を選択します。

1. ブループリント サンプルの _[基本]_ を入力します。

   - **[ブループリントの名前]** : New Zealand ISM Restricted ブループリント サンプルのコピーの名前を指定します。
   - **[定義の場所]** :省略記号を使用して、サンプルのコピーを保存する管理グループを選択します。

1. ページの上部にある _[アーティファクト]_ タブまたはページの下部にある **[次へ: アーティファクト]** を選択します。

1. ブループリント サンプルに含まれるアーティファクトの一覧を確認します。 多くのアーティファクトには、後で定義するパラメーターがあります。 ブループリント サンプルの確認が完了したら、 **[下書きの保存]** を選択します。

### <a name="publish-the-sample-copy"></a>サンプルのコピーを発行する

環境でのブループリント サンプルのコピーの作成が完了しました。 これは **下書き** モードで作成されており、割り当ておよびデプロイの前に **発行** する必要があります。 ブループリント サンプルのコピーは、お使いの環境や必要性に応じてカスタマイズできますが、それが原因で New Zealand ISM Restricted コントロールに準拠しなくなる可能性もあります。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[ブループリントの定義]** ページを選択します。 ブループリント サンプルのコピーを、フィルターを使用して検索し、選択します。

1. ページの上部にある **[ブループリントを発行する]** を選択します。 右側の新しいページで、ブループリント サンプルのコピーの **[バージョン]** を指定します。 このプロパティは、後で変更を加える場合に役立ちます。 **[変更に関するメモ]** に入力します (例: 「New Zealand ISM Restricted ブループリント サンプルから発行する最初のバージョン」)。 次に、ページの下部にある **[発行]** を選択します。

### <a name="assign-the-sample-copy"></a>サンプルのコピーを割り当てる

正常に **発行** されたブループリント サンプルのコピーは、保存先の管理グループ内のサブスクリプションに割り当てることができます。 この手順では、ブループリント サンプルのコピーの各デプロイを一意にするためのパラメーターを指定します。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[ブループリントの定義]** ページを選択します。 ブループリント サンプルのコピーを、フィルターを使用して検索し、選択します。

1. ブループリント定義ページの上部にある **[ブループリントの割り当て]** を選択します。

1. ブループリント割り当て用のパラメーター値を指定します。

   - 基本

     - **サブスクリプション**:ブループリント サンプルのコピーを保存した管理グループ内の 1 つ以上のサブスクリプションを選択します。 複数のサブスクリプションを選択すると、入力したパラメーターを使用して、それぞれに対して割り当てが作成されます。
     - **割り当て名**:名前は、ブループリントの名前に基づいてあらかじめ設定されています。
       必要に応じて変更することも、そのままにしておくこともできます。
     - **[場所]** :マネージド ID を作成するリージョンを選択します。 Azure Blueprint は、この管理対象 ID を使用して、割り当てられたブループリント内にすべての成果物をデプロイします。 詳細については、[Azure リソースの管理対象 ID の概要](../../../active-directory/managed-identities-azure-resources/overview.md)に関するページをご覧ください。
     - **ブループリント定義ラベル**:ブループリント サンプルのコピーの **発行済み** バージョンを選択します。

   - ロックの割り当て

     お使いの環境のブループリントのロック設定を選択します。 詳細については、[ブループリント リソースのロック](../concepts/resource-locking.md)に関するページを参照してください。

   - マネージド ID

     既定の _システム割り当て_ マネージド ID オプションはそのままにします。

   - アーティファクトのパラメーター

     このセクションで定義するパラメーターは、定義対象のアーティファクトに適用されます。 これらのパラメーターはブループリントの割り当て時に定義されるので、[動的パラメーター](../concepts/parameters.md#dynamic-parameters)です。 アーティファクトのパラメーターとその説明を含む詳しい一覧については、「[アーティファクトのパラメーター表](#artifact-parameters-table)」を参照してください。

1. すべてのパラメーターの入力が完了したら、ページの下部にある **[割り当て]** を選択します。 ブループリントの割り当てが作成され、アーティファクトのデプロイが開始されます。 デプロイに要する時間は、約 1 時間です。 デプロイの状態を確認するには、ブループリントの割り当てを開きます。

> [!WARNING]
> Azure Blueprints サービスと、組み込まれているブループリント サンプルは、**無料** でご利用になれます。 Azure リソースは、[製品ごとに課金](https://azure.microsoft.com/pricing/)されます。 このブループリント サンプルでデプロイされるリソースの実行コストを見積もるには、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。

### <a name="artifact-parameters-table"></a>アーティファクトのパラメーター表

以下の表は、ブループリント アーティファクトのパラメーターの一覧を示しています。

|アーティファクト名|アーティファクトの種類|パラメーター名|説明|
|-|-|-|-|
|New Zealand ISM Restricted|ポリシーの割り当て|Windows VM の Administrators グループに含める必要があるユーザーの一覧|ローカルの Administrators グループに含める必要があるユーザーのセミコロン区切りリスト。例: Administrator; myUser1; myUser2|
|New Zealand ISM Restricted|ポリシーの割り当て|Windows VM の Administrators グループから除外する必要があるユーザーの一覧|ローカルの Administrators グループで除外する必要があるユーザーのセミコロン区切りリスト。例: Administrator; myUser1; myUser2|
|New Zealand ISM Restricted|ポリシーの割り当て|Windows VM の Administrators グループに含める必要があるユーザー "だけ" の一覧|ローカルの Administrators グループに必要なすべてのメンバーのセミコロン区切りの一覧。例: Administrator; myUser1; myUser2|
|New Zealand ISM Restricted|ポリシーの割り当て|VM エージェント レポート用の Log Analytics ワークスペース ID|VM エージェントからレポートする必要のある Log Analytics ワークスペースの ID (GUID)|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: Azure Front Door Service に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: SQL サーバーの脆弱性評価の設定には、スキャン レポートを受信するためのメール アドレスが含まれている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 複数の所有者がサブスクリプションに割り当てられている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 仮想マシンでディスク暗号化を適用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 関数アプリでリモート デバッグを無効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: Web アプリケーション ファイアウォール (WAF) で Application Gateway の指定されたモードを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|Application Gateway の WAF モード要件|Application Gateway サービスでは、防止または検出モードを有効にする必要があります。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: SQL データベースで Transparent Data Encryption を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: SQL Managed Instance で脆弱性評価を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|省略可能: ギャラリー内のイメージに加えてスコープに追加する、Windows OS をサポートしているカスタム VM イメージのリスト: デプロイ - 依存関係エージェントを Windows 仮想マシンで有効になるように構成する|ゲスト構成の詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: Azure Cache for Redis に対しては安全な接続のみを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの評価時に Arc に接続されたサーバーを含める: Administrators グループで指定されたメンバーが不足している Windows マシンを監査する|'true' を選択すると、Arc に接続されたマシンごとに毎月請求されることに同意したものと見なされます|
|New Zealand ISM Restricted|ポリシーの割り当て|省略可能: ギャラリー内のイメージに加えてスコープに追加する、Windows OS をサポートしているカスタム VM イメージのリスト: [プレビュー]: 一覧に含まれる仮想マシン イメージに対して Log Analytics エージェントを有効にする必要がある|ゲスト構成の詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|省略可能: ギャラリー内のイメージに加えてスコープに追加する、Linux OS をサポートしているカスタム VM イメージのリスト: [プレビュー]: 一覧に含まれる仮想マシン イメージに対して Log Analytics エージェントを有効にする必要がある|ゲスト構成の詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントではネットワーク アクセスを制限する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|省略可能: ギャラリー内のイメージに加えてスコープに追加する、Windows OS をサポートしているカスタム VM イメージのリスト: デプロイ - 依存関係エージェントを Windows 仮想マシン スケール セットで有効になるように構成する|ゲスト構成の詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの評価時に Arc に接続されたサーバーを含める: Administrators グループに追加のアカウントがある Windows マシンを監査する|'true' を選択すると、Arc に接続されたマシンごとに毎月請求されることに同意したものと見なされます|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントへの安全な転送を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: Web アプリケーション ファイアウォール (WAF) で Azure Front Door Service の指定されたモードを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|Azure Front Door Service の WAF モード要件|Azure Front Door Service では、防止または検出モードを有効にする必要があります|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: 安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 最大 3 人の所有者をサブスクリプションに対して指定する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: [プレビュー]: ストレージ アカウントのパブリック アクセスを禁止する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: 脆弱性評価ソリューションを仮想マシンで有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: アプリケーション ゲートウェイに対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: CORS で、Web アプリケーションへのアクセスをすべてのリソースには許可しない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの評価時に Arc に接続されたサーバーを含める: セキュリティで保護された通信プロトコルを使用していない Windows Web サーバーを監査する|'true' を選択すると、Arc に接続されたマシンごとに毎月請求されることに同意したものと見なされます|
|New Zealand ISM Restricted|ポリシーの割り当て|Windows Web サーバーの TLS の最小バージョン|より低い TLS バージョンの Windows Web サーバーが非準拠として評価されます|
|New Zealand ISM Restricted|ポリシーの割り当て|省略可能: ギャラリー内のイメージに加えてスコープに追加する、Linux OS をサポートしているカスタム VM イメージのリスト: [プレビュー]: 一覧に含まれる仮想マシン イメージの仮想マシン スケール セットでは Log Analytics エージェントを有効にする必要がある|ゲスト構成の詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|省略可能: ギャラリー内のイメージに加えてスコープに追加する、Windows OS をサポートしているカスタム VM イメージのリスト: [プレビュー]: 一覧に含まれる仮想マシン イメージの仮想マシン スケール セットでは Log Analytics エージェントを有効にする必要がある|ゲスト構成の詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの評価時に Arc に接続されたサーバーを含める: Administrators グループに指定されたメンバーが存在する Windows マシンを監査する|'true' を選択すると、Arc に接続されたマシンごとに毎月請求されることに同意したものと見なされます|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 非推奨のアカウントをサブスクリプションから削除する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: Function App には HTTPS 経由でのみアクセスできるようにする|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: Azure サブスクリプションにはアクティビティ ログのログ プロファイルが必要|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|診断ログを有効にする必要のあるリソースの種類の一覧||
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: システム更新プログラムをマシンにインストールする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: API アプリでは最新の TLS バージョンを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: Microsoft IaaSAntimalware 拡張機能は Windows Server 上にデプロイする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: Web アプリケーションには HTTPS を介してのみアクセスできるようにする|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: Azure DDoS Protection Standard を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: Advanced Data Security を、SQL Server 上で有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: SQL Managed Instance で Advanced Data Security を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: Endpoint Protection の欠落の Azure Security Center での監視|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: アクティビティ ログは 1 年以上保持する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: 仮想マシンの管理ポートは Just-In-Time のネットワーク アクセス制御で保護する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: Service Fabric クラスターは、クライアント認証に Azure Active Directory だけを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: API アプリには HTTPS を介してのみアクセスできるようにする|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: Windows Defender Exploit Guard が有効になっていない Windows マシンを監査する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの評価時に Arc に接続されたサーバーを含める: Windows Defender Exploit Guard が有効になっていない Windows マシンを監査する|'true' を選択すると、Arc に接続されたマシンごとに毎月請求されることに同意したものと見なされます|
|New Zealand ISM Restricted|ポリシーの割り当て|Windows Defender Exploit Guard が使用できない Windows マシンについて報告するためのコンプライアンスの状態|Microsoft Defender Exploit Guard は、更新プログラム 1709 が適用された Windows 10 または Windows Server 以降でのみ利用できます。 この値を [非準拠] に設定すると、Microsoft Defender Exploit Guard が利用できない古いバージョンのマシン (Windows Server 2012 R2 など) が準拠しなくなります。 この値を [準拠] に設定すると、これらのマシンが準拠として表示されます。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 仮想マシン スケール セットにシステムの更新プログラムをインストールする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: Web アプリケーションのリモート デバッグを無効にする|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: コンテナーのセキュリティ構成の脆弱性を修復する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: API アプリでリモート デバッグを無効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: パスワードなしのアカウントからのリモート接続が可能な Linux マシンを監査する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの評価時に Arc に接続されたサーバーを含める: パスワードなしのアカウントからのリモート接続を許可する Linux マシンを監査する|'true' を選択すると、Arc に接続されたマシンごとに毎月請求されることに同意したものと見なされます|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 脆弱性評価を SQL Server 上で有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: Web アプリでは最新の TLS バージョンを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: Windows マシンでは [セキュリティの設定 - アカウント ポリシー] の要件を満たす必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|Windows VM ローカル アカウントのパスワード履歴を適用する|パスワードの再利用に制限を指定する - 同じパスワードを再利用できるようになる前に、ユーザー アカウントに新しいパスワードを何回作成する必要があるか|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの評価時に Arc に接続されたサーバーを含める: Windows マシンでは [セキュリティの設定 - アカウント ポリシー] の要件を満たす必要がある|'true' を選択すると、Arc に接続されたマシンごとに毎月請求されることに同意したものと見なされます|
|New Zealand ISM Restricted|ポリシーの割り当て|Windows VM ローカル アカウントのパスワードの有効期間|ユーザー アカウントのパスワードを変更しなければならなくなるまでの最大日数を指定します。値の形式は、範囲を表すコンマ区切りの 2 つの整数で、両端の値を含みます|
|New Zealand ISM Restricted|ポリシーの割り当て|Windows VM ローカル アカウントのパスワードの変更禁止期間|ユーザー アカウントのパスワードを変更できるようになるまでの最小日数を指定します|
|New Zealand ISM Restricted|ポリシーの割り当て|Windows VM ローカル アカウントのパスワードの最小の長さ|ユーザー アカウントのパスワードに含めることができる最小文字数を指定します|
|New Zealand ISM Restricted|ポリシーの割り当て|パスワードは Windows VM ローカル アカウントの複雑さの要件を満たす必要がある|ユーザー アカウントのパスワードを複雑にする必要があるかどうかを指定します。その必要がある場合、複雑なパスワードには、そのユーザーのアカウント名またはフルネームの一部を含めることができません。また、6 文字以上でなければならず、大文字、小文字、数字、非アルファベットの文字を組み合わせて使用する必要があります|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: インターネットに接続されている仮想マシンをネットワーク セキュリティ グループで保護する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: パスワードなしのアカウントがある Linux マシンを監査する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの評価時に Arc に接続されたサーバーを含める: パスワードなしのアカウントがある Linux マシンを監査する|'true' を選択すると、Arc に接続されたマシンごとに毎月請求されることに同意したものと見なされます|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: 関数アプリでは最新の TLS バージョンを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーに対する効果: [プレビュー]: すべてのインターネット トラフィックはデプロイされた Azure Firewall を介してルーティングする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|New Zealand ISM Restricted|ポリシーの割り当て|ポリシーの効果: SQL データベースの脆弱性を修復する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|

## <a name="next-steps"></a>次のステップ

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../how-to/update-existing-assignments.md)方法を参照する。