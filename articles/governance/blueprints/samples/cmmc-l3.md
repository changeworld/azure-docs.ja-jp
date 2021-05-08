---
title: CMMC レベル 3 ブループリント サンプル
description: CMMC レベル 3 ブループリント サンプルの概要。 このブループリント サンプルは、お客様が特定のコントロールを評価するのに役立ちます。
ms.date: 03/24/2021
ms.topic: sample
ms.openlocfilehash: 950c6064ce8b3d9973ac08e5895a4b6f48e37d6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572145"
---
# <a name="cmmc-level-3-blueprint-sample"></a>CMMC レベル 3 ブループリント サンプル

CMMC レベル 3 のブループリント サンプルでは、特定の[Cybersecurity Maturity Model Certification (CMMC) フレームワーク](https://www.acq.osd.mil/cmmc/index.html) コントロールの評価に役立つ、[Azure Policy](../../policy/overview.md) を使用したガバナンス ガードレールが提供されます。 このブループリントは、CMMC レベル 3 コントロールの実施が必要とされる、Azure でデプロイされたアーキテクチャのために、一連の主要なポリシーをデプロイするのに役立ちます。

## <a name="control-mapping"></a>コントロール マッピング

[Azure Policy のコントロール マッピング](../../policy/samples/cmmc-l3.md)では、このブループリントに含まれるポリシーの定義について詳しく取り上げると共に、それらのポリシー定義が CMMC フレームワークの **コントロール** にどのようにマッピングされるかについて詳しく説明します。 リソースはアーキテクチャに割り当てられると、割り当て済みのポリシー定義に違反していないかどうかを Azure Policy によって評価されます。 詳細については、[Azure Policy](../../policy/overview.md) に関するページをご覧ください。

## <a name="deploy"></a>配置

Azure Blueprints CMMC レベル 3 ブループリント サンプルをデプロイするには、以下の手順を実行する必要があります。

> [!div class="checklist"]
> - サンプルから新しいブループリントを作成する
> - サンプルのコピーを **発行済み** としてマークする
> - ブループリントのコピーを既存のサブスクリプションに割り当てる

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

### <a name="create-blueprint-from-sample"></a>サンプルからブループリントを作成する

最初に、サンプルをスターターとして使用して環境内に新しいブループリントを作成することにより、ブループリント サンプルを実装します。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[はじめに]** ページで、 _[ブループリントの作成]_ の下にある **[作成]** ボタンを選択します。

1. **CMMC レベル 3** ブループリント サンプルを _[その他のサンプル]_ で検索し、 **[このサンプルを使用する]** を選択します。

1. ブループリント サンプルの _[基本]_ を入力します。

   - **[ブループリントの名前]** : CMMC レベル 3 ブループリント サンプルのコピーの名前を指定します。
   - **[定義の場所]** :省略記号を使用して、サンプルのコピーを保存する管理グループを選択します。

1. ページの上部にある _[アーティファクト]_ タブまたはページの下部にある **[次へ: アーティファクト]** を選択します。

1. ブループリント サンプルに含まれるアーティファクトの一覧を確認します。 多くのアーティファクトには、後で定義するパラメーターがあります。 ブループリント サンプルの確認が完了したら、 **[下書きの保存]** を選択します。

### <a name="publish-the-sample-copy"></a>サンプルのコピーを発行する

環境でのブループリント サンプルのコピーの作成が完了しました。 これは **下書き** モードで作成されており、割り当ておよびデプロイの前に **発行** する必要があります。 ブループリント サンプルのコピーは、お使いの環境や必要性に応じてカスタマイズできますが、その変更が原因で CMMC レベル 3 コントロールに準拠しなくなる可能性もあります。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[ブループリントの定義]** ページを選択します。 ブループリント サンプルのコピーを、フィルターを使用して検索し、選択します。

1. ページの上部にある **[ブループリントを発行する]** を選択します。 右側の新しいページで、ブループリント サンプルのコピーの **[バージョン]** を指定します。 このプロパティは、後で変更を加える場合に役立ちます。 **[変更に関するメモ]** に入力します (例: 「CMMC レベル 3 ブループリント サンプルから発行する最初のバージョン」)。 次に、ページの下部にある **[発行]** を選択します。

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
|CMMC レベル 3|ポリシーの割り当て|ゲスト構成ポリシーを評価するときに Arc に接続されたサーバーを含める|'true' を選択すると、Arc に接続されたマシンごとに毎月課金されることに同意したことになります。詳細については、 https://aka.ms/policy-pricing にアクセスしてください|
|CMMC レベル 3|ポリシーの割り当て|Windows VM の Administrators グループから除外する必要があるユーザーの一覧|ローカルの Administrators グループで除外する必要があるユーザーのセミコロン区切りリスト。例: Administrator; myUser1; myUser2|
|CMMC レベル 3|ポリシーの割り当て|Windows VM の Administrators グループに含める必要があるユーザーの一覧|ローカルの Administrators グループに含める必要があるユーザーのセミコロン区切りリスト。例: Administrator; myUser1; myUser2|
|CMMC レベル 3|ポリシーの割り当て|VM エージェント レポート用の Log Analytics ワークスペース ID|VM エージェントからレポートする必要のある Log Analytics ワークスペースの ID (GUID)|
|CMMC レベル 3|ポリシーの割り当て|許可されている楕円曲線名|楕円曲線暗号証明書で許可されている曲線名の一覧。|
|CMMC レベル 3|ポリシーの割り当て|許可されているキーの種類|許可されているキーの種類の一覧|
|CMMC レベル 3|ポリシーの割り当て|Kubernetes クラスター ポッドに対してホスト ネットワークの使用を許可する|ポッドがホスト ネットワークを使用できる場合はこの値を true に設定し、それ以外の場合は false に設定します。|
|CMMC レベル 3|ポリシーの割り当て|認証ポリシーの変更の監査|認証ポリシーに変更が加えられたときに監査イベントを生成するかどうかを指定します。 この設定は、ユーザー アカウントまたはグループに付与されている、ドメイン レベルとフォレスト レベルの信頼関係と特権の変更を追跡するために便利です。|
|CMMC レベル 3|ポリシーの割り当て|承認ポリシーの変更の監査|ユーザー権利ポリシーでのユーザー権利の割り当てと削除、セキュリティ トークン オブジェクトのアクセス許可の変更、ファイル システム オブジェクトのリソース属性の変更と集約型アクセス ポリシーの変更について監査イベントを生成するかどうかを指定します。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: 仮想マシンに対して Azure Backup を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Cognitive Services アカウントでネットワーク アクセスを制限する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: SQL マネージド インスタンスでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure API for FHIR での保存データの暗号化には、カスタマー マネージド キー (CMK) を使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Azure Front Door Service に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: SQL サーバーの脆弱性評価の設定には、スキャン レポートを受信するためのメール アドレスが含まれている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Cognitive Services アカウントに対してパブリック ネットワーク アクセスを無効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: CORS で関数アプリへのアクセスをすべてのリソースには許可しない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 複数の所有者がサブスクリプションに割り当てられている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 仮想マシンでディスク暗号化を適用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: サブスクリプション所有者に対する重要度 - 高のアラートのメール通知を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: キー コンテナーでは消去保護を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: SQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 関数アプリでリモート デバッグを無効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Azure Defender for Key Vault を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure Database for MariaDB に対して geo 冗長バックアップを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: CORS で API for FHIR へのアクセスをすべてのドメインには許可しない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Windows マシンでは [セキュリティ オプション - ネットワーク セキュリティ] の要件を満たしている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: 適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Web アプリケーション ファイアウォール (WAF) で Application Gateway の指定されたモードを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: キーには有効期限が設定されている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: SQL データベースで Transparent Data Encryption を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure Monitor ログ プロファイルでは、'書き込み'、'削除'、'アクション' の各カテゴリのログを収集する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: SQL Managed Instance で脆弱性評価を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: API アプリの一部として使用された "PHP のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: キー コンテナーでは論理的な削除を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Azure Cache for Redis に対しては安全な接続のみを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure Database for PostgreSQL サーバーに対してインフラストラクチャ暗号化を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Azure Defender for App Service を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Windows マシンでは [システム監査ポリシー - ポリシーの変更] の要件を満たしている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Cognitive Services アカウントではデータ暗号化を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: インターネットからの SSH アクセスをブロックする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: アタッチされていないディスクを暗号化する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Azure Defender for Storage を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントではネットワーク アクセスを制限する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: CORS で API アプリへのアクセスをすべてのリソースには許可しない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: ストレージ アカウントで Advanced Threat Protection をデプロイする|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Automation アカウント変数を暗号化する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: IoT Hub の診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure Database for MySQL サーバーに対してインフラストラクチャ暗号化を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 特定のセキュリティ操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Security/securitySolutions/delete)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Windows マシンでは [セキュリティ オプション - ネットワーク アクセス] の要件を満たしている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントへの安全な転送を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure Monitor では、すべてのリージョンからアクティビティ ログを収集する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Web アプリケーション ファイアウォール (WAF) で Azure Front Door Service の指定されたモードを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: ストレージ アカウントにはインフラストラクチャの暗号化が必要|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: 安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Windows マシンでは [セキュリティ オプション - ユーザー アカウント制御] の要件を満たす必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Web アプリの一部として使用された "Java のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Azure Defender for servers を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 最大 3 人の所有者をサブスクリプションに対して指定する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: サブスクリプションには、セキュリティの問題に備えて連絡先メール アドレスが用意されている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントのパブリック アクセスを禁止する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: 脆弱性評価ソリューションを仮想マシンで有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Azure Defender for Kubernetes を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: キー コンテナーでファイアウォールを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: アプリケーション ゲートウェイに対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: CORS で、Web アプリケーションへのアクセスをすべてのリソースには許可しない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: 過去 24 個分のパスワードの再利用が可能な Windows マシンを監査する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: PostgreSQL フレキシブル サーバーに対して公衆ネットワーク アクセスを無効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Azure Container Registry イメージの脆弱性を修復する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Service Fabric クラスターでは ClusterProtectionLevel プロパティを EncryptAndSign に設定する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Azure Defender for SQL servers on machines を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Cognitive Services アカウントでカスタマー マネージド キーによるデータ暗号化を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 非推奨のアカウントをサブスクリプションから削除する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Function App には HTTPS 経由でのみアクセスできるようにする|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 重要度が高いアラートのメール通知を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントでは暗号化にカスタマー マネージド キーを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Web アプリの一部として使用された "Python のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 関数アプリの一部として使用された "Python のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Web アプリの一部として使用された "PHP のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: API アプリの一部として使用された "Python のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: キーは指定された暗号化の種類 RSA または EC である必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Azure サブスクリプションにはアクティビティ ログのログ プロファイルが必要|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure Kubernetes Service クラスター内のオペレーティング システムとデータ ディスクの両方がカスタマー マネージド キーによって暗号化されている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Azure Defender for Azure SQL Database servers を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure Data Explorer における保存時の暗号化でカスタマー マネージド キーを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: RSA 暗号を使用するキーにキーの最小サイズを指定する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: Kubernetes クラスター ポッドでは承認されたホスト ネットワークとポート範囲のみを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: システム更新プログラムをマシンにインストールする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Windows マシンでは [システム監査ポリシー - 特権の使用] の要件を満たしている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure Stream Analytics ジョブでは、データの暗号化にカスタマー マネージド キーを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: API アプリの一部として使用された "Java のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Web アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: API アプリでは最新の TLS バージョンを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: API アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Microsoft IaaSAntimalware 拡張機能は Windows Server 上にデプロイする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 関数アプリの一部として使用された "Java のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: 仮想マシンに関連付けられたネットワーク セキュリティ グループでは、すべてのネットワーク ポートを制限する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Security Center の Standard 価格レベルを選択する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: パスワードの最小長が 14 文字に制限されていない Windows マシンを監査する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: カスタム RBAC 規則の使用監査|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Web アプリケーションには HTTPS を介してのみアクセスできるようにする|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: SQL Server の監査を有効にする必要があります|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 仮想マシンに Log Analytics エージェントをインストールする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Advanced Data Security を、SQL Server 上で有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: SQL Managed Instance で Advanced Data Security を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Kubernetes サービスではロールベースのアクセス制御 (RBAC) を使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: 仮想マシンではゲスト構成拡張機能が必要|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Endpoint Protection の欠落の Azure Security Center での監視|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: アクティビティ ログは 1 年以上保持する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: 仮想マシンの管理ポートは Just-In-Time のネットワーク アクセス制御で保護する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: PostgreSQL サーバーに対してパブリック ネットワーク アクセスを無効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Cosmos DB アカウントに Advanced Threat Protection をデプロイする|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: App Services の診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: API アプリには HTTPS を介してのみアクセスできるようにする|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: 特定の管理操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.ClassicNetwork/networkSecurityGroups/delete)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: 特定の管理操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 特定の管理操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Network/networkSecurityGroups/delete)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 特定の管理操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Network/networkSecurityGroups/securityRules/delete)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 特定の管理操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Sql/servers/firewallRules/delete)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: インターネットに接続されていない仮想マシンをネットワーク セキュリティ グループで保護する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: パスワードの複雑さ設定が有効になっていない Windows マシンを監査する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Azure Defender for container registries を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure Data Box ジョブで、デバイス上の保存データについて二重暗号化を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 仮想マシン スケール セットにシステムの更新プログラムをインストールする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure 向け Microsoft Antimalware では保護定義を自動的に更新するように構成する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 特定のポリシー操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Authorization/policyAssignments/delete)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: MySQL フレキシブル サーバーに対して公衆ネットワーク アクセスを無効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントでは、信頼された Microsoft サービスからのアクセスを許可する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Web アプリケーションのリモート デバッグを無効にする|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: RSA 暗号を使用する証明書に、キーの最小サイズを指定する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: コンテナー レジストリでは無制限のネットワーク アクセスを許可しない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: システム割り当てマネージド ID を持つ Azure 仮想マシンにゲスト構成拡張機能がデプロイされている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Azure SQL データベースの長期的な geo 冗長バックアップを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: MySQL サーバーに対してパブリック ネットワーク アクセスを無効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: 暗号化を元に戻せる状態でパスワードを保存していない Windows マシンを監査する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Windows マシンでは [ユーザー権利の割り当て] の要件を満たしている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 関数アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: インターネットからの RDP アクセスをブロックする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: passwd ファイルへのアクセス許可が 0644 に設定されていない Linux マシンを監査する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: サブネットはネットワーク セキュリティ グループに関連付けられている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: コンテナーのセキュリティ構成の脆弱性を修復する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: API アプリでリモート デバッグを無効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: パスワードなしのアカウントからのリモート接続が可能な Linux マシンを監査する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure Data Explorer で二重暗号化を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 脆弱性評価を SQL Server 上で有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: Web アプリでは最新の TLS バージョンを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure Data Explorer でディスク暗号化を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: インターネットに接続されている仮想マシンをネットワーク セキュリティ グループで保護する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: パスワードなしのアカウントがある Linux マシンを監査する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Azure Synapse ワークスペースでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: 関数アプリでは最新の TLS バージョンを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Kubernetes Service を脆弱性のない Kubernetes バージョンにアップグレードする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: すべてのインターネット トラフィックはデプロイされた Azure ファイアウォールを介してルーティングする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: Linux マシンでは Azure セキュリティ ベースラインの要件を満たしている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーに対する効果: MariaDB サーバーに対してパブリック ネットワーク アクセスを無効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの効果: SQL データベースの脆弱性を修復する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの結果: 楕円曲線暗号を使用するキーに曲線名を指定する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ポリシーの評価から除外する名前空間: Kubernetes クラスター ポッドでは承認されたホスト ネットワークとポート範囲のみを使用する必要がある|ポリシー評価から除外する Kubernetes 名前空間の一覧。|
|CMMC レベル 3|ポリシーの割り当て|App Services に対する最新の Java バージョン|App Services に対してサポートされる最新の Java バージョン|
|CMMC レベル 3|ポリシーの割り当て|App Services に対する Linux 用の最新の Python バージョン|App Services に対してサポートされる最新の Python バージョン|
|CMMC レベル 3|ポリシーの割り当て|省略可能: Log Analytics エージェントのデプロイを監査するときにスコープに追加する、Linux OS をサポートしている VM イメージのリスト|値の例: '/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage'|
|CMMC レベル 3|ポリシーの割り当て|省略可能: Log Analytics エージェントのデプロイを監査するときにスコープに追加する、Windows OS をサポートしている VM イメージのリスト|値の例: '/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage'|
|CMMC レベル 3|ポリシーの割り当て|Network Watcher を有効にする必要があるリージョンの一覧|Network Watcher がリージョンに対して有効になっていない場合に監査します。|
|CMMC レベル 3|ポリシーの割り当て|診断ログを有効にする必要のあるリソースの種類の一覧||
|CMMC レベル 3|ポリシーの割り当て|ポッドがホスト ネットワーク名前空間で使用可能な、許容されるホスト ポート範囲内の最大値|ポッドがホスト ネットワーク名前空間で使用可能な、許容されるホスト ポート範囲内の最大値です。|
|CMMC レベル 3|ポリシーの割り当て|キーの RSA キーの最小サイズ|RSA キーの最小キー サイズ。|
|CMMC レベル 3|ポリシーの割り当て|最小 RSA キー サイズ証明書|RSA 証明書の最小キー サイズ。|
|CMMC レベル 3|ポリシーの割り当て|Windows Web サーバーの TLS の最小バージョン|より低い TLS バージョンの Windows Web サーバーが非準拠として評価されます|
|CMMC レベル 3|ポリシーの割り当て|ポッドがホスト ネットワーク名前空間で使用可能な、許容されるホスト ポート範囲内の最小値|ホスト ネットワーク名前空間でポッドにより使用できる、許容されるホスト ポート範囲内の最小値です。|
|CMMC レベル 3|ポリシーの割り当て|モードの要件|すべての WAF ポリシーに対してモードが必要です|
|CMMC レベル 3|ポリシーの割り当て|モードの要件|すべての WAF ポリシーに対してモードが必要です|
|CMMC レベル 3|ポリシーの割り当て|ポッドの hostPath ボリュームで使用される、許可されたホスト パス|ポッドの hostPath ボリュームでの使用が許可されたホスト パスです。 すべてのホスト パスをブロックするには、空のパスの一覧を指定します。|
|CMMC レベル 3|ポリシーの割り当て|ネットワーク アクセス: リモートからアクセスできるレジストリのパス|`winreg` レジストリ キーのアクセス制御リスト (ACL) に一覧表示されているユーザーまたはグループに関係なく、どのレジストリ パスに対してネットワーク経由でアクセスできるかを指定します。|
|CMMC レベル 3|ポリシーの割り当て|ネットワーク アクセス: リモートからアクセスできるレジストリのパスおよびサブパス|`winreg` レジストリ キーのアクセス制御リスト (ACL) に一覧表示されているユーザーまたはグループに関係なく、どのレジストリ パスとサブパスに対してネットワーク経由でアクセスできるかを指定します。|
|CMMC レベル 3|ポリシーの割り当て|ネットワーク アクセス: 匿名でアクセスできる共有|匿名ユーザーがアクセスできるネットワーク共有を指定します。 どのユーザーもサーバー上の共有リソースにアクセスする前に認証されている必要があるため、このポリシー設定の既定の構成はほとんど効果がありません。|
|CMMC レベル 3|ポリシーの割り当て|ネットワーク セキュリティ: Kerberos で許可する暗号化の種類を構成する|Kerberos で使用できる暗号化の種類を指定します。|
|CMMC レベル 3|ポリシーの割り当て|ネットワーク セキュリティ: LAN Manager 認証レベル|ネットワーク ログオンに使用するチャレンジレスポンス認証プロトコルを指定します。 この認証レベルを選択すると、クライアントが使用する認証プロトコル、ネゴシエートされるセッション セキュリティ、およびサーバーで受け付けられる認証レベルが影響を受けます。|
|CMMC レベル 3|ポリシーの割り当て|ネットワーク セキュリティ: 必須の署名をしている LDAP クライアント|LDAP BIND 要求を発行するクライアントの代理として要求されるデータ署名のレベルを指定します。|
|CMMC レベル 3|ポリシーの割り当て|ネットワーク セキュリティ: NTLM SSP ベース (セキュア RPC を含む) のクライアント向け最小セッション セキュリティ|NTLM セキュリティ サポート プロバイダー (SSP) を使用するアプリケーションに対してクライアントが許可する動作を指定します。 SSP インターフェイス (SSPI) は認証サービスが必要なアプリケーションに使用されます。 詳細については、「[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers)」を参照してください。|
|CMMC レベル 3|ポリシーの割り当て|ネットワーク セキュリティ: NTLM SSP ベース (セキュア RPC を含む) のサーバー向け最小セッション セキュリティ|NTLM セキュリティ サポート プロバイダー (SSP) を使用するアプリケーションに対してサーバーが許可する動作を指定します。 SSP インターフェイス (SSPI) は認証サービスが必要なアプリケーションに使用されます。|
|CMMC レベル 3|ポリシーの割り当て|App Services に対する最新の PHP バージョン|App Services に対してサポートされる最新の PHP バージョン|
|CMMC レベル 3|ポリシーの割り当て|IoT Hub の診断ログの必須の保持期間 (日数)||
|CMMC レベル 3|ポリシーの割り当て|Network Watcher 用のリソース グループの名前|NetworkWatcherRG などの NetworkWatcher のリソース グループの名前。 これは、Network Watcher が配置されているリソース グループの名前です。|
|CMMC レベル 3|ポリシーの割り当て|SQL サーバーに必要な監査設定||
|CMMC レベル 3|ポリシーの割り当て|ソフトウェアベースの二重暗号化をサポートする Azure Data Box SKU|ソフトウェアベースの二重暗号化をサポートする Azure Data Box SKU の一覧|
|CMMC レベル 3|ポリシーの割り当て|UAC: あらかじめ登録された Administrator アカウントの管理者承認モード|あらかじめ登録された Administrator アカウントの管理者承認モードの動作を指定します。|
|CMMC レベル 3|ポリシーの割り当て|UAC: 管理者承認モードにおける管理者の昇格時のプロンプトの動作|管理者向けの昇格時のプロンプトの動作を指定します。|
|CMMC レベル 3|ポリシーの割り当て|UAC: アプリケーションのインストールの検出と昇格時のプロンプト表示|コンピューターのアプリケーションのインストール検出の動作を指定します。|
|CMMC レベル 3|ポリシーの割り当て|UAC: すべての管理者を管理者承認モードで実行する|コンピューターのすべてのユーザー アカウント制御 (UAC) ポリシー設定の動作を指定します。|
|CMMC レベル 3|ポリシーの割り当て|リモート システムからシャットダウンを強制できるユーザーとグループ|ネットワーク上のリモートの場所からコンピューターをシャットダウンできるユーザーとグループを指定します。|
|CMMC レベル 3|ポリシーの割り当て|ネットワークからこのコンピューターへのアクセスが拒否されるユーザーとグループ|ネットワーク経由でのこのコンピューターへの接続を明示的に禁止するユーザーまたはグループを指定します。|
|CMMC レベル 3|ポリシーの割り当て|ローカル ログオンが拒否されるユーザーとグループ|コンピューターへのログオンが明示的に許可されないユーザーとグループを指定します。|
|CMMC レベル 3|ポリシーの割り当て|バッチ ジョブとしてのログオンが拒否されるユーザーとグループ|バッチ ジョブ (スケジュールされたタスク) としてコンピューターにログオンすることが明示的に許可されないユーザーとグループを指定します。|
|CMMC レベル 3|ポリシーの割り当て|サービスとしてのログオンが拒否されるユーザーとグループ|サービスとしてのプロセスの登録が明示的に許可されないサービス アカウントを指定します。|
|CMMC レベル 3|ポリシーの割り当て|リモート デスクトップ サービスによるログオンが拒否されるユーザーとグループ|ターミナル サービスまたはリモート デスクトップ クライアントを使用してコンピューターにログオンすることが明示的に許可されないユーザーとグループを指定します。|
|CMMC レベル 3|ポリシーの割り当て|ファイルとディレクトリを復元できるユーザーとグループ|バックアップしたファイルとディレクトリを復元するときに、ファイル、ディレクトリ、レジストリ、その他の永続的オブジェクトのアクセス許可を回避できるユーザーとグループを指定します。|
|CMMC レベル 3|ポリシーの割り当て|システムをシャットダウンできるユーザーとグループ|シャットダウン コマンドを使用してオペレーティング システムをシャットダウンすることが許可される、環境内のコンピューターにローカルでログオンしているユーザーとグループを指定します。|
|CMMC レベル 3|ポリシーの割り当て|ローカルでログオンできるユーザーまたはグループ|コンピューターへの接続を許可するネットワーク上のリモート ユーザーを指定します。 これにはリモート デスクトップ接続は含まれません。|
|CMMC レベル 3|ポリシーの割り当て|ファイルとディレクトリをバックアップできるユーザーまたはグループ|ファイルとディレクトリのアクセス許可を回避してシステムをバックアップできるユーザーとグループを指定します。|
|CMMC レベル 3|ポリシーの割り当て|システム時刻を変更できるユーザーまたはグループ|コンピューターの内部時計の日時を変更できるユーザーとグループを指定します。|
|CMMC レベル 3|ポリシーの割り当て|タイム ゾーンを変更できるユーザーまたはグループ|このコンピューターのタイム ゾーンを変更できるユーザーとグループを指定します。|
|CMMC レベル 3|ポリシーの割り当て|トークン オブジェクトを作成できるユーザーまたはグループ|アクセス トークンを作成できるユーザーとグループを指定します。これにより、機密データにアクセスするための昇格された権限が与えられる可能性があります。|
|CMMC レベル 3|ポリシーの割り当て|ローカルでログオンできるユーザーまたはグループ|このコンピューターに対話的にログオンできるユーザーまたはグループを指定します。 リモート デスクトップ接続または IIS 経由でログオンしようとするユーザーにもこのユーザー権限が必要です。|
|CMMC レベル 3|ポリシーの割り当て|Remote Desktop Users|リモート デスクトップ サービスを使用してログオンできるユーザーまたはグループ|
|CMMC レベル 3|ポリシーの割り当て|監査とセキュリティ ログを管理できるユーザーまたはグループ|ファイルとディレクトリの監査オプションを変更し、セキュリティ ログをクリアできるユーザーとグループを指定します。|
|CMMC レベル 3|ポリシーの割り当て|ファイルまたは他のオブジェクトの所有権を取得できるユーザーまたはグループ|ファイル、フォルダー、レジストリ キー、プロセス、またはスレッドの所有権を取得できるユーザーとグループを指定します。 このユーザー権利は、オブジェクトを保護するために設定されているすべてのアクセス許可をバイパスして、指定されたユーザーに所有権を与えます。|

## <a name="next-steps"></a>次のステップ

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../how-to/update-existing-assignments.md)方法を参照する。