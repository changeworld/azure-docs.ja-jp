---
title: Azure Security Center での脅威の防止
description: このトピックでは、Azure Security Center の脅威の防止機能によって保護されるリソースについて説明します
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: b28901918f2606100d92f47800c6e0fb6778e3d0
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82606893"
---
# <a name="threat-protection-in-azure-security-center"></a>Azure Security Center での脅威の防止

Security Center は、ご自身の環境のいずれかの領域で脅威を検出すると、アラートを生成します。 これらのアラートでは、影響を受けるリソースや推奨される修復手順の詳細と、場合によっては、ロジック アプリを応答でトリガーするオプションが示されます。

Azure Security Center の脅威の防止によって、お使いの環境が包括的に保護されます。

* **Azure コンピューティング コンテナーの脅威の防止**: Windows マシン、Linux マシン、Azure App Service、および Azure コンテナー

* **Azure データ リソースの脅威の防止**: SQL Database と SQL Data Warehouse、Azure Storage、および Azure Cosmos DB

* **Azure サービス レイヤーの脅威の防止**: Azure ネットワーク レイヤー、Azure 管理レイヤー (Azure Resource Manager) (プレビュー)、および Azure Key Vault (プレビュー)

アラートは、Security Center によって生成されたか、別のセキュリティ製品の Security Center によって受信されたかにかかわらず、エクスポートすることができます。 アラートを Azure Sentinel (またはサードパーティの SIEM) あるいはその他の外部ツールにエクスポートする場合は、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。 

> [!TIP]
> Security Center の脅威保護機能を有効にするには、Standard 価格レベルを、適用可能なワークロードを含むサブスクリプションに適用する必要があります。
>
> **Azure Storage アカウント**の脅威保護は、サブスクリプション レベルまたはリソース レベルで有効にできます。
> **Azure SQL Database SQL サーバー**の脅威保護は、サブスクリプション レベルまたはリソース レベルで有効にできます。
> **Azure Database for MariaDB/MySQL/PostgreSQL** の脅威保護は、リソース レベルでのみ有効にできます。



## <a name="threat-protection-for-windows-machines"></a>Windows マシンの脅威の防止 <a name="windows-machines"></a>

Azure Security Center は Azure サービスと統合し、Windows ベースのマシンの監視と保護が行われます。 Security Center は、これらすべてのサービスからのアラートと修復の提案を使いやすい形式で示します。

* **Microsoft Defender ATP** <a name="windows-atp"></a> - Security Center では、Microsoft Defender Advanced Threat Protection (ATP) と統合することで、そのクラウド ワークロード保護プラットフォームを拡張します。 同時に、包括的なエンドポイントの検出と対応 (EDR) 機能が提供されます。

    > [!IMPORTANT]
    > Microsoft Defender ATP センサーは、Security Center を使用する Windows サーバーで自動的に有効になります。

    Microsoft Defender ATP で脅威が検出されると、アラートがトリガーされます。 アラートは、[Security Center] ダッシュボードに表示されます。 ダッシュボードからは、Microsoft Defender ATP コンソールにピボットし、詳細な調査を実行して攻撃の範囲を明らかにすることができます。 Microsoft Defender ATP の詳細については、「[Microsoft Defender ATP サービスに対するサーバーのオンボード](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)」をご覧ください。

* **クラッシュ ダンプ分析** <a name="windows-dump"></a> - ソフトウェアがクラッシュすると、クラッシュ時のメモリが部分的にクラッシュ ダンプにキャプチャされます。

    クラッシュは、マルウェアが原因か、マルウェアが含まれている可能性があります。 セキュリティ製品によって検出されないようにするために、さまざまな形式のマルウェアがファイルレス攻撃を使用して、ディスクへの書き込みやディスクに書き込まれたソフトウェア コンポーネントの暗号化を回避します。 この種の攻撃は、従来のディスク ベースのアプローチを使用して検出することは困難です。

    しかし、メモリ分析を使用すると、この種の攻撃を検出できます。 クラッシュ ダンプでメモリを分析すると、Security Center では、攻撃で使用されている手法を検出できます。 たとえば、攻撃では、ソフトウェアの脆弱性の悪用、機密データへのアクセス、侵害されたコンピューターでの不正な保持が試みられている可能性があります。 Security Center では、ホストへのパフォーマンスへの影響を最小限に抑えながら、これが行われます。

    クラッシュ ダンプ分析アラートの詳細については、[アラートのリファレンス表](alerts-reference.md#alerts-windows)に関するページを参照してください。

* **ファイルレス攻撃の検出** <a name="windows-fileless"></a> - エンドポイントを対象とするファイルレス攻撃は一般的です。 ファイルレス攻撃は、検出を回避するために悪意のあるペイロードをメモリに挿入します。 侵害されたプロセスのメモリ内に存続する攻撃者のペイロードにより、さまざまな悪意のあるアクティビティが実行されます。

    自動メモリ フォレンジック手法は、ファイルレス攻撃の検出を使用して、ファイルレス攻撃のツールキット、手法、および動作を識別します。 このソリューションでは、実行時にマシンが定期的にスキャンされて、セキュリティ クリティカルなプロセスのメモリから分析情報が直接抽出されます。

    これは、悪用、コード インジェクション、および悪意のあるペイロードの実行の証拠を見つけます。 ファイルレス攻撃の検出により、アラートのトリアージ、相関関係、およびダウン ストリームの応答時間を高速化するための詳細なセキュリティ アラートが生成されます。 このアプローチにより、イベント ベースの EDR ソリューションが補完され、検出範囲が拡大します。

    ファイルレス攻撃の検出アラートの詳細については、[アラートのリファレンス表](alerts-reference.md#alerts-windows)に関するページを参照してください。

> [!TIP]
> Windows のアラートをシミュレートするには、「[Azure Security Center プレイブック:セキュリティ アラート](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)」をダウンロードします。






## <a name="threat-protection-for-linux-machines"></a>Linux マシンの脅威の防止 <a name="linux-machines"></a>

Security Center では、**auditd** (最も一般的な Linux 監査フレームワークの 1 つ) を使用して Linux マシンから監査レコードが収集されます。 auditd はメインライン カーネルに存在します。 

* **Linux auditd アラートと Log Analytics エージェントの統合** <a name="linux-auditd"></a> - auditd システムは、システム コールの監視を担当するカーネル レベルのサブシステムで構成されます。 指定されたルール セットによってそれらのフィルター処理が行われ、それらに対するメッセージがソケットに書き込まれます。 Security Center は、Log Analytics エージェント内で auditd パッケージの機能を統合します。 この統合により、前提条件なしで、すべてのサポートされている Linux ディストリビューションで auditd イベントの収集が可能になります。

    Linux 用 Log Analytics エージェントを使用して、auditd レコードの収集、拡充、およびイベントへの集約が行われます。 Security Center では、Linux のシグナルを使用してクラウドおよびオンプレミスの Linux マシン上で悪意のある動作を検出する新しい分析が、継続的に追加されています。 Windows の機能と同様に、これらの分析は、疑わしいプロセス、不審なサインイン試行、カーネル モジュールの読み込み、およびその他のアクティビティにまたがります。 これらのアクティビティは、マシンが攻撃を受けているか、侵害されたことを示している可能性があります。  

    Linux アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-linux)に関するページを参照してください。

> [!TIP]
> Linux のアラートをシミュレートするには、「[Azure Security Center プレイブック:Linux の検出](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)」をダウンロードします。





## <a name="threat-protection-for-azure-app-service"></a>Azure App Service の脅威の防止 <a name="app-services"></a>

> [!NOTE]
> 現在、Azure Government およびソブリン クラウド リージョンでは、このサービスを利用できません。

Security Center では、クラウドのスケールを使用して、App Service で実行されるアプリケーションをターゲットとした攻撃が識別されます。 攻撃者は、Web アプリケーションをプローブして弱点を発見し、悪用します。 Azure で実行されているアプリケーションに対する要求は、特定の環境にルーティングされる前に、複数のゲートウェイを通過し、そこで検査され、ログに記録されます。 その後、このデータは、悪用や攻撃者を特定し、後で使用される新しいパターンを学習するために使用されます。

Security Center では、Azure がクラウド プロバイダーとして備える可視性を使用して、App Service の内部ログが分析され、複数のターゲットに対する攻撃手法が識別されます。 たとえば、広範囲にわたるスキャンや分散型の攻撃などの手法です。 この種の攻撃は通常、IP の小さなサブセットから発生し、複数のホスト上の類似のエンドポイントをクロールするパターンを示します。 攻撃では脆弱なページやプラグインが検索され、単一のホストの観点からは攻撃を特定できません。

Windows ベースの App Service プランを実行している場合、Security Center では、基になるサンド ボックスおよび VM にアクセスすることもできます。 前述のログ データと組み合わせることで、インフラストラクチャは、流行している新しい攻撃から、お客様のマシンの侵害まで、状況を通知できます。 したがって、Web アプリが悪用された後に Security Center がデプロイされたとしても、進行中の攻撃を検出できる可能性があります。

Azure App Service アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureappserv)に関するページを参照してください。

App Service プランの詳細については、「[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/plans/)」を参照してください。





## <a name="threat-protection-for-azure-containers"></a>Azure コンテナーの脅威の防止 <a name="azure-containers"></a>

> [!NOTE]
> 現在、Azure Government およびソブリン クラウド リージョンでは、このサービスを利用できません。

Security Center では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、疑わしいアクティビティに対してはアラートが生成されます。 ユーザーは、この情報を使用して、迅速にセキュリティの問題を修復し、コンテナーのセキュリティを強化することができます。

Security Center では、さまざまなレベルで脅威の防止が提供されます。 

* **ホスト レベル** - Security Center のエージェント (Standard レベルで利用可能。詳細については、[価格](security-center-pricing.md)に関するページを参照) が、Linux に対する不審なアクティビティを監視します。 ノードまたはそこで実行されているコンテナーを発生源とする不審なアクティビティについては、エージェントによってアラートがトリガーされます。 そのようなアクティビティとしては、たとえば、Web シェルの検出や既知の不審な IP アドレスとの接続が挙げられます。

    コンテナー化された環境のセキュリティについて、より詳しい分析情報を得るために、エージェントは、コンテナー固有の分析情報を監視します。 特権コンテナーの作成、API サーバーへの不審なアクセス、Docker コンテナー内での SSH (Secure Shell) サーバーの実行などのイベントが発生すると、アラートがトリガーされます。

    >[!IMPORTANT]
    > ホストにエージェントをインストールしなかった場合、脅威の防止によってもたらされるメリットとセキュリティ アラートは限定されます。 その場合でも、ネットワーク分析や悪意のあるサーバーとの通信に関連したアラートは通知されます。

    ホスト レベルのアラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-containerhost)に関するページを参照してください。


* **AKS クラスター レベル**では、脅威の防止は Kubernetes の監査ログの分析に基づきます。 この**エージェントレス**の監視を有効にするには、 **[価格と設定]** ページから、ご利用のサブスクリプションに Kubernetes オプションを追加してください ([価格](security-center-pricing.md)に関するページを参照)。 このレベルのアラートを生成するために、Security Center は、AKS によって取得されたログを使用して、AKS のマネージド サービスを監視します。 このレベルのイベントの例として、公開されている Kubernetes ダッシュボード、高い特権ロールの作成、機微なマウントの作成などがあります。

    >[!NOTE]
    > Azure Kubernetes Service のアクションやデプロイについてのセキュリティ アラートが Security Center から生成されるのは、サブスクリプションの設定で Kubernetes オプションが有効にされた後になります。 

    AKS クラスター レベルのアラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-akscluster)に関するページを参照してください。

また、Microsoft のセキュリティ研究員から成るグローバル チームも、脅威の状況を絶えず監視しています。 コンテナー固有のアラートや脆弱性は、それらが検出された時点で追加されます。

> [!TIP]
> コンテナーのアラートをシミュレートするには、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)の手順に従います。








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>SQL Database および SQL Data Warehouse の脅威の防止 <a name="data-sql"></a>

Advanced Threat Protection for Azure SQL Database では、データベースへのアクセスや悪用を試みる、通常とは異なる、害を及ぼす可能性がある異常なアクティビティが検出されます。

疑わしいデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセスやクエリのパターンがある場合に、アラートが表示されます。

Advanced Threat Protection for Azure SQL Database and SQL は、高度な SQL セキュリティ機能を提供する [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 統合パッケージに含まれており、Azure SQL データベース、Azure SQL Database マネージド インスタンス、Azure SQL Data Warehouse データベース、Azure Virtual Machines 上の SQL サーバーに対応しています。

詳細については、次を参照してください。

* [Advanced Threat Protection for Azure SQL Database を有効にする方法](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Azure Virtual Machines 上の SQL サーバーに対して Advanced Threat Protection を有効にする方法](security-center-iaas-advanced-data.md)
* [SQL Database および SQL Data Warehouse 向け脅威保護アラートの一覧](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Azure Storage の脅威の防止 <a name="azure-storage"></a>

Advanced Threat Protection for Storage では、ストレージ アカウントにアクセスしたり、ストレージ アカウントを利用したりする試みに通常と異なるところがあり、有害な性質が疑われる場合に、そのような試みを検出できます。 この保護層により、セキュリティの専門家でなくても脅威に対処し、セキュリティ監視システムを管理できます。

Advanced Threat Protection for Azure Storage は、現時点では [BLOB ストレージ](https://azure.microsoft.com/services/storage/blobs/)でのみ使用できます。 

このサービスはすべてのパブリック クラウドと米国政府のクラウドで利用できますが、他のソブリン クラウドと Azure Government クラウドのリージョンでは使用できません。

30 日間の無料試用など、価格の詳細については、[Azure Security Center の価格ページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

詳細については、次を参照してください。

* [Advanced Threat Protection for Azure Storage を有効にする方法](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure Storage 向け脅威保護アラートの一覧](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> Azure Storage のアラートをシミュレートするには、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)の手順に従います。




## <a name="threat-protection-for-azure-cosmos-db"></a>Azure Cosmos DB の脅威の防止 <a name="cosmos-db"></a>

Azure Cosmos DB アラートは、通常とは異なる、害を及ぼす可能性がある、Azure Cosmos DB アカウントへのアクセスや悪用が試行された場合に生成されます。

詳細については、次を参照してください。

* [Azure Cosmos DB の Advanced Threat Protection (プレビュー)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB 向け脅威保護アラートの一覧 (プレビュー)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Azure ネットワーク レイヤーの脅威の防止 <a name="network-layer"></a>

Security Center のネットワーク レイヤー分析は、サンプルの [IPFIX データ](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)に基づくものであり、Azure コア ルーターによって収集されるパケット ヘッダーです。 このデータ フィードに基づき、Security Center は機械学習モデルを使用して、悪意のあるトラフィック アクティビティを特定し、それらにフラグを設定します。 IP アドレスを強化するために、Security Center では、Microsoft の脅威インテリジェンス データベースも使用します。

ネットワーク構成によっては、Security Center が疑わしいネットワーク アクティビティに対してアラートを生成することを制限する場合があります。 Security Center でネットワーク アラートを生成するには、次のことを確認してください。

- 仮想マシンにパブリック IP アドレスがある (または、仮想マシンがパブリック IP アドレスを持つロード バランサー上にある)。

- 仮想マシンのネットワーク エグレス トラフィックが、外部 ID ソリューションによってブロックされていない。

- 不信な通信が発生した時間全体にわたって、仮想マシンに同じ IP アドレスが割り当てられている。 これは、管理サービスの一部として作成された VM (AKS、Databricks など) にも適用されます。

Azure ネットワーク レイヤー アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azurenetlayer)に関するページを参照してください。

Security Center でネットワーク関連のシグナルを使用して脅威の防止を適用する方法の詳細については、「[Security Center でのヒューリスティック DNS 検出](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)」を参照してください。



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Azure 管理レイヤー (Azure Resource Manager) の脅威の防止 (プレビュー)<a name ="management-layer"></a>

Azure Resource Manager に基づく Security Center の保護レイヤーは、現在プレビュー段階です。

Security Center では、Azure のコントロール プレーンと見なされる Azure Resource Manager イベントを使用することで、追加の保護レイヤーが提供されます。 Security Center では、Azure Resource Manager のレコードを分析することで、Azure サブスクリプション環境での異常な、または害を及ぼす可能性のある操作を検出します。

Azure Resource Manager (プレビュー) アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureresourceman)に関するページを参照してください。



>[!NOTE]
> 上記のいくつかの分析では、Microsoft Cloud App Security が利用されています。 これらの分析を活用するには、Cloud App Security ライセンスをアクティブにする必要があります。 Cloud App Security ライセンスをお持ちの場合、これらのアラートは既定で有効になります。 アラートを無効にするには、次のようにします。
>
> 1. **[Security Center]** ブレードで **[セキュリティ ポリシー]** を選択します。 変更するサブスクリプションに対して **[設定の編集]** を選択します。
> 2. **[脅威検出]** を選択します。
> 3. **[統合の有効化]** で、 **[Microsoft Cloud App Security にデータへのアクセスを許可する]** チェック ボックスをクリアして、 **[保存]** を選択します。

>[!NOTE]
>Security Center では、そのリソースと同じ地域でセキュリティ関連の顧客データが格納されます。 Microsoft によってまだリソースの地域に Security Center がデプロイされていない場合、米国でデータが格納されます。 Cloud App Security が有効になっている場合、この情報は、Cloud App Security の地域の場所のルールに従って格納されます。 詳細については、[非リージョン サービスのデータ ストレージ](https://azuredatacentermap.azurewebsites.net/)に関するページを参照してください。








## <a name="threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault の脅威の防止 (プレビュー)<a name="azure-keyvault"></a>

> [!NOTE]
> 現在、Azure Government およびソブリン クラウド リージョンでは、このサービスを利用できません。

Azure Key Vault は、暗号化キーとシークレット (証明書、接続文字列、パスワードなど) を保護するクラウド サービスです。 

Azure Security Center には、Azure Key Vault 用に Azure ネイティブの高度な脅威保護機能が含まれており、セキュリティ インテリジェンスのレイヤーが追加されます。 Security Center では、異常であり、害を及ぼす可能性のある、Key Vault アカウントに対するアクセスまたは悪用の試みを検出できます。 この保護層により、セキュリティの専門家でなくても、サードパーティ製のセキュリティ監視システムを管理する必要なしに、脅威に対処することができます。  

異常なアクティビティが発生した場合、Security Center では、アラートが表示され、必要に応じてサブスクリプション管理者にメールが送信されます。 これらのアラートには、不審なアクティビティの詳細と、脅威の調査や修復方法に関する推奨事項が含まれます。 

Azure Key Vault アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azurekv)に関するページを参照してください。





## <a name="threat-protection-for-other-microsoft-services"></a>他の Microsoft サービスの脅威の防止 <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF の脅威の防止 <a name="azure-waf"></a>

Azure Application Gateway は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する Web アプリケーション ファイアウォール (WAF) を提供します。

Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の標的になるケースが増えています。 Application Gateway の WAF は、Open Web Application Security Project のコア ルール セット 3.0 または 2.2.9 に基づいています。 WAF は、新たな脆弱性から保護するために自動的に更新されます。 

Azure WAF のライセンスをお持ちの場合は、WAF アラートが Security Center にストリーミングされます。その際に追加の構成は必要ありません。 WAF によって生成されるアラートの詳細については、[Web アプリケーション ファイアウォールの CRS 規則グループと規則](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)に関する記事をご覧ください。


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS Protection の脅威の防止 <a name="azure-ddos"></a>

分散型サービス拒否 (DDoS) 攻撃は、簡単に実行できることが分かっています。 セキュリティの大きな不安材料になっており、アプリケーションをクラウドに移行する場合は特にそうです。 

DDoS 攻撃では、アプリケーションのリソースを使い果たし、正当なユーザーがアプリケーションを使用できなくなるようにすることが試みられます。 DDoS 攻撃では、インターネットを介して到達できるあらゆるエンドポイントが対象になる可能性があります。

DDoS 攻撃を防ぐには、Azure DDoS Protection のライセンスを購入し、アプリケーションの設計に関するベスト プラクティスに従っていることを確認します。 DDoS Protection では、各種のサービス レベルが提供されます。 詳細については、[Azure DDoS Protection の概要](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)に関する記事を参照してください。

Azure DDoS Protection アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureddos)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
これらの脅威の防止機能からのセキュリティ アラートの詳細については、次の記事を参照してください。

* [Azure Security Center のすべてのアラートの参照テーブル](alerts-reference.md)
* [Security alerts in Azure Security Center](security-center-alerts-overview.md)
* [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)
* [セキュリティ アラートと推奨事項のエクスポート (プレビュー)](continuous-export.md)