---
title: Azure Active Directory の検証可能な資格情報の発行ソリューションを計画する (プレビュー)
description: エンドツーエンドの発行ソリューションを計画する方法について説明します。
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.custom: references_regions
ms.openlocfilehash: fdf6ef5910e31dc21929cf484b2b1ad580badef7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319722"
---
# <a name="plan-your-azure-active-directory-verifiable-credentials-issuance-solution-preview"></a>Azure Active Directory の検証可能な資格情報の発行ソリューションを計画する (プレビュー)

 >[!IMPORTANT]
> Azure Active Directory の検証可能な資格情報は現在、パブリック プレビュー段階にあります。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳細については、「[**Microsoft Azure プレビューの追加使用条件**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

資格情報の発行に加えて、アーキテクチャとビジネスに対するソリューションの影響が完全にわかるように、発行ソリューションを計画することが重要です。 まだ行っていない場合は、[Azure Active Directory の検証可能な資格情報アーキテクチャの概要](introduction-to-verifiable-credentials-architecture.md)に関する記事を参照して、基本情報を確認することをお勧めします。

## <a name="scope-of-guidance"></a>ガイダンスのスコープ

この記事では、Identity Overlay Network (ION) と相互運用する、Microsoft 製品を使用した検証可能な資格情報の発行ソリューションの計画に関する技術的側面について説明します。 Microsoft の検証可能な資格情報ソリューションは、World Wide Web コンソーシアム (W3C) の[検証可能な資格情報のデータ モデル 1.0](https://www.w3.org/TR/vc-data-model/) と[分散識別子 (DID) V1.0](https://www.w3.org/TR/did-core/) 標準に従っているため、Microsoft 以外のサービスと相互運用できます。 ただし、このコンテンツの例には、検証可能な資格情報の Microsoft ソリューション スタックが反映されています。 

このコンテンツの対象外となるのは、発行ソリューションに固有ではないサポート技術に関するトピックです。 たとえば、検証可能な資格情報の発行ソリューションでは Web サイトが使用されますが、Web サイトのデプロイ計画について詳しくは説明しません。

## <a name="components-of-the-solution"></a>ソリューションのコンポーネント

発行ソリューションの計画の一環として、発行者、ユーザー、検証者間の対話を可能にするソリューションを設計する必要があります。 複数の検証可能な資格情報を発行できます。 次の図は、発行アーキテクチャのコンポーネントを示しています。

### <a name="microsoft-vc-issuance-solution-architecture"></a>Microsoft VC 発行ソリューションのアーキテクチャ

![発行ソリューションのコンポーネント](media/plan-issuance-solution/plan-issuance-solution-architecture.png)


### <a name="azure-active-directory-tenant"></a>Azure Active Directory テナント

Azure AD の検証可能な資格情報サービスを実行するための前提条件は、それを Azure Active Directory (Azure AD) テナントでホストすることです。 Azure AD テナントにより、ソリューションに含まれる Azure リソース用の ID およびアクセス管理 (IAM) コントロール プレーンが提供されます。

各テナントには、Azure AD の検証可能な資格情報サービスの 1 つのインスタンスと、1 つの分散識別子 (DID) があります。 DID により、発行者が DID に組み込まれているドメインを所有していることが証明されます。 DID は、発行者を検証するために、サブジェクトおよび検証者によって使用されます。 

### <a name="microsoft-azure-services"></a>Microsoft Azure サービス

![発行ソリューションのコンポーネント (Azure サービスに重点を置いています)](media/plan-issuance-solution/plan-issuance-solution-azure-services.png)

**Azure Key Vault** サービスにより、発行者キーが保存されます。このキーは、Azure AD の検証可能な資格情報の発行サービスを開始すると生成されます。 このキーとメタデータを使用して、資格情報の管理操作が実行され、メッセージのセキュリティが提供されます。

各発行者は、署名、更新、回復に使用する 1 つのキー セットを所有します。 このキー セットは、生成するすべての検証可能な資格情報のすべての発行に使用されます。 

**Azure Storage** を使用して、資格情報のメタデータと定義 (具体的には、資格情報のルールと表示ファイル) が保存されます。 

* 表示ファイルにより、VC でどのクレームを保存するか、および所有者のウォレットでそれをどのように表示するかが決定されます。 表示ファイルには、ブランドおよびその他の要素も含まれます。 ルール ファイルのサイズは 50 KB に制限され、表示ファイルは 150 KB に制限されます。 「[検証可能な資格情報をカスタマイズする方法](../verifiable-credentials/credential-design.md)」を参照してください。

* ルールは、発行者定義モデルであり、検証可能な資格情報の必須入力、入力の信頼できるソース、出力クレームへの入力クレームのマッピングを表します。 

   * **入力** – クライアントが使用するための、ルール ファイル内のモデルのサブセットです。 このサブセットでは、入力のセット、入力を取得する場所、検証可能な資格情報を取得するために呼び出すエンドポイントを表す必要があります。

* さまざまなコンテナー、サブスクリプション、ストレージを使用するように、さまざまな資格情報のルールおよび表示ファイルを構成できます。 たとえば、特定の VC の管理を担当するさまざまなチームにアクセス許可を委任できます。 

### <a name="azure-ad-verifiable-credentials-service"></a>Azure AD の検証可能な資格情報サービス

![Microsoft Azure AD の検証可能な資格情報サービス](media/plan-issuance-solution/plan-issuance-azure-active-directory-verifiable-credential-services.png)

Azure AD の検証可能な資格情報サービスを使用すると、構成に基づいて VC を発行したり、取り消したりすることができます。 サービスによって:

* 分散識別子 (DID) がプロビジョニングされ、DID ドキュメントが ION に書き込まれます。この ION で、サブジェクトおよび検証者はその DID ドキュメントを使用できます。 各発行者は、テナントごとに 1 つの DID を所有します。 

* キー セットが Key Vault にプロビジョニングされます。 

* 発行サービスおよび Microsoft Authenticator によって使用される構成メタデータが保存されます。

### <a name="ion"></a>ION

![ION](media/plan-issuance-solution/plan-issuance-solution-ion.png)

Microsoft では、[Identity Overlay Network (ION)](https://identity.foundation/ion/) を使用しています。これは、分散識別子 (DID) の実装のためにビットコインのブロックチェーンを使用する [Sidetree ベースのネットワーク](https://identity.foundation/sidetree/spec/)です。 発行者の DID ドキュメントは ION に保存され、暗号化署名チェックを実行するためにトランザクションのパーティによって使用されます。

### <a name="microsoft-authenticator-application"></a>Microsoft Authenticator アプリケーション

![Microsoft Authenticator アプリケーション](media/plan-issuance-solution/plan-issuance-solution-authenticator.png)

Microsoft Authenticator は、モバイル アプリケーションであり、ユーザー、Azure AD の検証可能な資格情報サービス、VC の発行に使用されるコントラクトで記述される依存関係の間の相互作用を調整します。 これは、VC の所有者が VC (VC のサブジェクトの秘密キーを含む) を保存するデジタル ウォレットとして機能します。 Authenticator は、検証対象の VC を提示するために使用されるメカニズムでもあります。

### <a name="issuance-business-logic"></a>発行ビジネス ロジック 

![発行ビジネス ロジック](media/plan-issuance-solution/plan-issuance-solution-business-logic.png)

発行ソリューションには、Web フロントエンドが含まれます。ここで、ユーザーは VC、ID ストア、その他の属性ストアを要求して、サブジェクトやその他のバックエンド サービスに関するクレームの値を取得します。 

Web フロントエンドは、ディープ リンクや QR コードを生成することによって、サブジェクトのウォレットに対して発行要求を出します。 コントラクトの構成によっては、VC を作成するための要件を満たすために、他のコンポーネントが必要になる場合があります。

これらのサービスにより、ION や Azure AD の検証可能な資格情報の発行サービスと必ずしも統合する必要のないサポート ロールが提供されます。 通常、このレイヤーには次のものが含まれます。

* **Open ID Connect (OIDC) 準拠の 1 つ以上のサービス** – VC を発行するために必要な id_tokens を取得するために使用されます。 Identity Server などのカスタム ソリューションで OIDC 準拠のサービスを提供できるように、Azure AD や Azure AD B2C などの既存の ID システムでも OIDC 準拠のサービスを提供できます。

* **属性ストア** – これらはディレクトリ サービスの外部にある場合があり、VC を発行するために必要な属性を提供します。 たとえば、学生情報システムで、取得済みの学位に関するクレームを提供する場合があります。 

* **追加の中間層サービス** – これには、検索、検証、課金に関するビジネス ルール、および資格情報を発行するために必要なその他のランタイム チェックとワークフローが含まれます。

Web フロントエンドの設定の詳細については、[検証可能な資格情報を発行するための Azure AD の構成](../verifiable-credentials/enable-your-tenant-verifiable-credentials.md)に関するチュートリアルを参照してください。 

## <a name="credential-design-considerations"></a>資格情報の設計に関する考慮事項

特定のユース ケースにより、資格情報の設計が決まります。 ユース ケースにより、次のことが決まります。

* 相互運用性の要件

* ユーザーが VC を取得するために本人であることを証明する方法

* 資格情報に必要なクレーム

* 資格情報を取り消す必要が発生するか

 

### <a name="credential-use-cases"></a>資格情報のユース ケース

Azure AD の検証可能な資格情報では、最も一般的な資格証明のユース ケースは次のとおりです。

**本人確認**: 資格情報が複数の条件に基づいて発行されます。 これには、パスポートや運転免許証などの政府発行ドキュメントの信頼性を検証し、そのドキュメント内の情報を次のような他の情報と関連付けることが含まれる場合があります。

* ユーザーの自撮り写真 

* 生存性の検証

この種の資格情報は、新しい従業員、パートナー、サービス プロバイダー、学生、および本人確認が必要なその他の場合の ID オンボード シナリオに適しています。

 

![本人確認のユース ケース](media/plan-issuance-solution/plan-issuance-solution-identity-verification-use-cases.png)

**雇用/メンバーシップの証明**: ユーザーと機関の間の関係を証明するために資格情報が発行されます。 この種の資格情報は、従業員や学生に割引を提供する小売業者など、疎結合企業間アプリケーションにアクセスする場合に適しています。 VC の主な価値の 1 つはポータビリティです。発行後、ユーザーは多くのシナリオで VC を使用できます。 

![雇用証明のユース ケース](media/plan-issuance-solution/plan-issuance-solution-employment-proof-use-cases.png)

その他のユース ケースについては、[検証可能な資格情報のユース ケース (w3.org)](https://www.w3.org/TR/vc-use-cases/) に関する記事を参照してください。

### <a name="credential-interoperability"></a>資格情報の相互運用性

設計プロセスの一環として、相互運用性と使用率を最大化するために、適用できる業界固有のスキーマ、名前空間、識別子を調査します。 例については、[Schema.org](https://schema.org/) および [DIF - クレームと資格情報の作業グループ](https://identity.foundation/working-groups/claims-credentials.html)に関するページを参照してください。

共通スキーマは、標準がまだ形になりつつある段階であることに注意してください。 このような取り組みの 1 つの例として、[教育機関向けの検証可能な資格情報タスク フォース](https://github.com/w3c-ccg/vc-ed)があります。 所属組織の業界の形になりつつある標準を調査し、それに協力することをお勧めします。

### <a name="credential-attributes"></a>資格情報の属性 

資格情報のユース ケースを確立した後、資格情報に含める属性を決定する必要があります。 検証者は、ユーザーが提示した VC のクレームを読み取ることができます。

シナリオに適用できる可能性のある業界固有の標準とスキーマに加えて、次の点を考慮します。

* **個人情報を最小限にする**: 必要最小限の個人情報でユース ケースに対処します。 たとえば、従業員と退職者に割引を提供する e コマース Web サイトで使用される VC は、名と姓のクレームのみで資格情報を提示することで実現できます。 入社日、役職、部署などの追加情報は必要ありません。

* **抽象クレームを優先する**: 詳細を最小限に抑える一方で、各クレームでニーズを満たす必要があります。 たとえば、"13"、"21"、"60" などの不連続値を使用した "ageOver" という名前のクレームは、誕生日クレームよりも抽象的です。

* **取り消しに関する計画**: 資格情報を検索して取り消すメカニズムを有効にするために、インデックス クレームを定義することをお勧めします。 コントラクトごとに定義できるインデックス クレームは 1 つのみです。 インデックス クレームの値はバックエンドに保存されず、クレーム値のハッシュのみが保存されることに注意することが重要です。 詳細については、「[以前に発行された検証可能な資格情報を失効させる](../verifiable-credentials/how-to-issuer-revoke.md)」を参照してください。

資格情報の属性に関するその他の考慮事項については、「[検証可能な資格情報のデータ モデル 1.0 (w3.org)](https://www.w3.org/TR/vc-data-model/)」の仕様を参照してください。

## <a name="plan-quality-attributes"></a>品質属性の計画

### <a name="plan-for-performance"></a>パフォーマンスの計画

他のソリューションと同様に、パフォーマンスについて計画する必要があります。 注目する必要がある重要な領域は、待機時間、スループット、ストレージ、スケーラビリティです。 リリース サイクルの初期フェーズでは、パフォーマンスを考慮する必要はありません。 ただし、発行ソリューションを導入したことにより多くの検証可能な資格情報が発行されるときは、パフォーマンス計画がソリューションの重要な部分になる可能性があります。

以下では、パフォーマンスを計画するときに考慮する必要がある領域を示します。

* Azure AD の検証可能な資格情報の発行サービスは、西ヨーロッパ、北ヨーロッパ、米国西部 2、米国中西部の各 Azure リージョンにデプロイされています。 サービスをデプロイする先のリージョンは選択しません。 

* 待機時間を制限するには、前に示したリージョンのうち、要求が発生すると予想される場所に最も近いものに、発行フロントエンド Web サイト、キー コンテナー、ストレージをデプロイします。

スループットに基づくモデル:
* 発行者サービスは、[Azure Key Vault サービスの制限](../../key-vault/general/service-limits.md)の対象です。 

*  Azure Key Vault では、VC の発行ごとに 3 つの署名操作が必要です。

      * Web サイトからの発行要求に対して 1 つ

      * 作成される VC に対して 1 つ

      * コントラクトのダウンロードに対して 1 つ

* Key Vault の最大署名パフォーマンスは、約 10 秒で 2,000 件の署名です。 これは、1 分あたり約 12,000 件の署名です。 つまり、ソリューションでは、1 分あたり最大で 4,000 の VC 発行をサポートできます。

* スロットリングは制御できません。ただし、「[Azure Key Vault のスロットル ガイダンス](../../key-vault/general/overview-throttling.md)」を確認することをお勧めします。 

* VC の大規模なロールアウトとオンボードを計画している場合は、制限を超えないようにするために、VC の作成をバッチ処理することを検討してください。

* 発行サービスは、Azure Storage の制限の対象です。 一般的なユース ケースでは、ストレージは考慮する必要ありません。 ただし、ストレージの制限を超える可能性があると感じる場合や、ストレージがボトルネックになる可能性があると感じる場合は、以下を確認してください。 

   * 計画プロセスの一環として「[BLOB ストレージのスケーラビリティとパフォーマンスのターゲット](../../storage/blobs/scalability-targets.md)」を確認することをお勧めします。 Azure AD の検証可能な資格情報の発行サービスは、ルールを読み取り、ファイルを表示します。結果はサービスによってキャッシュされます。

   * 「[BLOB ストレージのパフォーマンスとスケーラビリティのチェックリスト - Azure Storage](../../storage/blobs/storage-performance-checklist.md)」も確認することをお勧めします。 

パフォーマンスの計画の一環として、ソリューションのパフォーマンスをよりよく理解するために監視する対象を決定します。 VC 発行の監視戦略を定義するときに、アプリケーション レベルの Web サイト監視に加えて、次の点を考慮してください。

スケーラビリティについては、以下に関するメトリックの実装を検討してください。

   * 発行プロセスの論理フェーズを定義します。 次に例を示します。

   * 最初の要求 

   * QR コードまたはディープ リンクの提供

   * 属性の参照

   * Azure AD の検証可能な資格情報の発行サービスの呼び出し

   * 資格情報の発行

   * フェーズに基づいてメトリックを定義します。

      * 要求の総数 (量)

      * 時間単位あたりの要求数 (スループット)

      * 消費時間 (待機時間)

* 以下を使用して Azure Key Vault と Storage を監視します。

   * [Azure Key Vault の監視とアラート](../../key-vault/general/alert.md)

   * [Azure Blob Storage の監視](../../storage/blobs/monitor-blob-storage.md)

* ビジネス ロジック層に使用されるコンポーネントを監視します。 

### <a name="plan-for-reliability"></a>信頼性の計画

信頼性を計画するために、以下をお勧めします。

* 可用性と冗長性の目標を定義した後、次のガイドを使用して目標を達成する方法を理解します。

   * [Azure Key Vault の可用性と冗長性 - Azure Key Vault](../../key-vault/general/disaster-recovery-guidance.md)

   * [ディザスター リカバリーとストレージ アカウントのフェールオーバー - Azure Storage](../../storage/common/storage-disaster-recovery-guidance.md)

* フロントエンドおよびビジネス層では、ソリューションは無数の方法で実現できます。 他のソリューションと同様に、識別された依存関係について、依存関係の回復性を確保し、監視できるようにします。 

Azure AD の検証可能な資格情報の発行サービス、Azure Key Vault、または Azure Storage サービスが使用できなくなるというまれな事態が発生した場合、ソリューション全体が使用できなくなります。

### <a name="plan-for-compliance"></a>コンプライアンスの計画

組織には、業界、トランザクションの種類、または運用国に関連する特定のコンプライアンス ニーズがある場合があります。 

**データ所在地**: Azure AD の検証可能な資格情報の発行サービスは、Azure リージョンのサブセットにデプロイされます。 このサービスは、コンピューティング機能のためにのみ使用されます。 検証可能な資格情報の値は、Microsoft システムには保存されません。 ただし、発行プロセスの一環として、VM の発行時に個人データが送信され、使用されます。 VC サービスを使用しても、データ所在地の要件には影響しません。 本人確認の一環として個人情報を保存する場合、コンプライアンス要件を満たす方法で、コンプライアンス要件を満たすリージョンに保存する必要があります。 Azure 関連のガイダンスについては、Microsoft トラスト センターの Web サイトを参照してください。 

**資格情報の取り消し**: 組織で資格情報を取り消す必要があるかどうかを決定します。 たとえば、従業員が退職するとき、管理者は資格情報を取り消す必要がある場合があります。 また、運転免許証に対して資格情報が発行され、所有者が何かを行って逮捕され、それが原因で運転免許証が一時停止となった場合、VC を取り消す必要がある場合があります。 詳細については、「[以前に発行された検証可能な資格情報を失効させる](how-to-issuer-revoke.md)」を参照してください。

**資格情報の有効期限切れ**: 資格情報の有効期限を設けるかどうかを決定し、設定する場合は、どのような状況で有効期限切れにするかを決定します。 たとえば、運転免許証を所持していることの証明として VC を発行した場合、数年後に有効期限切れになる可能性があります。 ユーザーとの関連付けの立証として VC を発行する場合、VC を毎年有効期限切れにすることが望ましい場合があります。これにより、ユーザーが毎年最新バージョンの VC を取得するようにします。

## <a name="plan-for-operations"></a>運用の計画

運用を計画する場合、トラブルシューティング、レポート作成、サポートするさまざまな顧客の識別に使用するスキーマを作成することが重要です。 さらに、運用チームが VC 取り消しの実行を担当する場合は、そのプロセスを定義する必要があります。 プロセスの各ステップを関連付ける必要があります。これにより、どのログ エントリを一意の各発行要求に関連付けることができるか特定できます。 監査のために、資格情報発行の各試行を個別にキャプチャすることをお勧めします。 具体的には:

* 顧客とサポート エンジニアが必要に応じて参照できる一意のトランザクション ID を生成します。

* Azure Key Vault トランザクションのログを、ソリューションの発行部分のトランザクション ID に関連付けるメカニズムを考案します。

* 複数の顧客に代わって VC を発行する ID 検証サービスの場合は、顧客向けレポートと課金のために、顧客またはコントラクトの ID によって監視および軽減措置を行います。

* 複数の顧客に代わって VC を発行する ID 検証サービスの場合は、顧客向けレポートと課金、監視、および軽減措置のために、顧客またはコントラクトの ID を使用します。 

## <a name="plan-for-security"></a>セキュリティの計画

セキュリティに重点を置く設計の考慮事項の一部として、以下を推奨します。

* キー管理:

   * VC 発行専用の Key Vault を作成します。 Azure Key Vault のアクセス許可を Azure AD の検証可能な資格情報の発行サービス、および発行サービス フロントエンド Web サイトのサービス プリンシパルに制限します。 

   * Azure Key Vault を権限が高いシステムとして扱います - Azure Key Vault により資格情報が顧客に発行されます。 Azure Key Vault サービスに対する継続的なアクセス許可を人間の ID に付与しないようにすることをお勧めします。 管理者には、Key Vault への 1 回限りのアクセス権のみ付与する必要があります。 Azure Key Vault の使用に関するその他のベスト プラクティスについては、「[Key Vault 用の Azure セキュリティ ベースライン](/security/benchmark/azure/baselines/key-vault-security-baseline)」を参照してください。

* 発行フロントエンド Web サイトを表すサービス プリンシパルの場合:

   * Azure Key Vault へのアクセスを承認する専用のサービス プリンシパルを定義します。 Web サイトが Azure にある場合、[Azure マネージド ID](../managed-identities-azure-resources/overview.md) を使用することをお勧めします。 

   * Web サイトを表すサービス プリンシパルとユーザーを 1 つの信頼境界として扱います。 複数の Web サイトを作成できますが、発行ソリューションに設定されるキーは 1 つのみです。 

セキュリティ ログと監視について、以下をお勧めします。

* 資格情報の発行操作、キーの抽出試行、アクセス許可の変更を追跡するため、および構成の変更を監視し、アラートを送信するために、Azure Key Vault のログとアラートを有効にします。 詳細については、「[Key Vault のログ記録を有効にする方法](../../key-vault/general/howto-logging.md)」を参照してください。 

* 構成の変更を監視し、アラートを送信するために、Azure Storage アカウントのログを有効にします。 詳細については、「[Azure Blob Storage の監視](../../storage/blobs/monitor-blob-storage.md)」を参照してください。

* 長期保有のため、[Microsoft Sentinel](https://azure.microsoft.com/services/azure-sentinel) などのセキュリティ情報イベント管理 (SIEM) システムにログをアーカイブします。

* 以下を使用してスプーフィングのリスクを軽減します。

   * 顧客が発行者のブランドを識別するのに役立つ DNS 確認。

   * エンド ユーザーにわかりやすいドメイン名。

   * エンド ユーザーが認める信頼できるブランド。

* 分散型サービス拒否 (DDOS) と Key Vault リソース枯渇のリスクを軽減します。 VC 発行要求をトリガーするすべての要求により、Key Vault 署名操作が生成されます。これは、サービス制限まで増加します。 発行要求を生成する前に、認証または CAPTCHA を組み込むことにより、トラフィックを保護することをお勧めします。

Azure 環境の管理のガイダンスについて、「[Azure セキュリティ ベンチマーク](/security/benchmark/azure/)」と「[Azure Active Directory を使用した Azure 環境のセキュリティ保護](https://aka.ms/AzureADSecuredAzure)」を確認することをお勧めします。 これらのガイドでは、Azure Key Vault、Azure Storage、Web サイト、その他の Azure 関連のサービスや機能など、基になる Azure リソースを管理するためのベスト プラクティスを提供しています。

## <a name="additional-considerations"></a>その他の注意点

POC を完了した後、生成されたすべての情報とドキュメントを収集し、発行者の構成を破棄することを検討します。 これにより、POC の有効期限が切れた後に、検証可能な資格情報が発行されることを回避できます。 

Key Vault の実装と操作について詳しくは、「[Key Vault を使用するためのベスト プラクティス](../../key-vault/general/best-practices.md)」を参照してください。 Active Directory を使用した Azure 環境のセキュリティ保護について詳しくは、「[Azure Active Directory を使用した Azure 環境のセキュリティ保護](https://aka.ms/AzureADSecuredAzure)」を参照してください。 

## <a name="next-steps"></a>次のステップ

[アーキテクチャの概要を確認する](introduction-to-verifiable-credentials-architecture.md)

[検証ソリューションの計画](plan-verification-solution.md)

[検証可能な資格情報のスタート ガイド](get-started-verifiable-credentials.md)
