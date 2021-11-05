---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 10/05/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: ad344bd6aad97877a41c1be33039929f121603ae
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131014328"
---
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>セキュリティ スコア</b></th>
    <th class="tg-cly1"><b>セキュリティ コントロールと説明</b></th>
    <th class="tg-cly1"><b>Recommendations (推奨事項)</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong>10</strong></td>
    <td class="tg-lboi"><strong>MFA を有効にする</strong>- Defender for Cloud では、多要素認証 (MFA) に高い値が設定されます。 これらの推奨事項を使用して、サブスクリプションのユーザーをセキュリティで保護します。 <br>MFA を有効にし、推奨事項に準拠するには、セキュリティの既定値、ユーザーごとの割り当て、条件付きアクセス ポリシーの 3 つの方法があります。 これらのオプションの詳細については、<a href="/azure/security-center/security-center-identity-access">サブスクリプションでの MFA 実施の管理</a>に関する記事を参照してください。</td>
    <td class="tg-lboi" width=55%>-サブスクリプションに対する所有者アクセス許可を持つアカウントに対して、MFA を有効にする必要があります<br />-サブスクリプションに対する所有者アクセス許可を持つアカウントに対して、MFA を有効にする必要があります<br />- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある<br />- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>8</strong></td>
    <td class="tg-lboi"><strong>管理ポートのセキュリティ保護</strong> - ブルートフォース攻撃では、管理ポートがしばしば標的になります。 これらの推奨事項と、<a href="/azure/security-center/just-in-time-explained">Just-In-Time VM アクセス</a>や<a href="/azure/virtual-network/network-security-groups-overview">ネットワーク セキュリティ グループ</a>などのツールを使用して露出を減らします。</td>
    <td class="tg-lboi" width=55%>- インターネットに接続されている仮想マシンをネットワーク セキュリティ グループで保護する必要がある<br />- 仮想マシンの管理ポートは、Just-In-Time ネットワーク アクセス制御によって保護されている必要があります<br />- お使いの仮想マシンの管理ポートを閉じておく必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>6</strong></td>
    <td class="tg-lboi"><strong>システムの更新プログラムの適用</strong> - 更新プログラムを適用しないと、修正プログラムが脆弱性に適用されないままになるため、環境は攻撃を受けやすくなります。 これらの推奨事項を使用して、運用効率を維持し、セキュリティ脆弱性を軽減し、より安定した環境をエンド ユーザーに提供することができます。 システムの更新プログラムを展開するには、<a href="/azure/automation/update-management/overview">Update Management ソリューション</a>を使用してマシンの修正プログラムと更新プログラムを管理します。</td>
    <td class="tg-lboi" width=55%>- Log Analytics エージェントを Linux ベースの Azure Arc マシンにインストールする必要がある<br />- 仮想マシンに Log Analytics エージェントをインストールする必要がある<br />- 仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある<br />- Log Analytics エージェントを Windows ベースの Azure Arc マシンにインストールする必要がある<br />- Virtual Machine Scale Sets にシステムの更新プログラムをインストールする必要があります<br />- お使いのマシンにシステムの更新プログラムをインストールする必要があります<br />- システム更新プログラムをマシンにインストールする必要がある (更新センターを利用)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>6</strong></td>
    <td class="tg-lboi"><strong>脆弱性を修復する</strong>- Defender for Cloud には、マシン、データベース、コンテナー レジストリで脅威アクターが利用する可能性のある脆弱性を確認する複数の脆弱性評価スキャナーが含まれています。 これらの推奨事項を使用して、これらのスキャナーを有効にし、検出結果を確認してください。<br><a href="/azure/security-center/deploy-vulnerability-assessment-vm">マシン</a>、<a href="/azure/security-center/defender-for-sql-on-machines-vulnerability-assessment">SQL サーバー</a>、<a href="/azure/security-center/defender-for-container-registries-usage">コンテナー レジストリ</a>のスキャンの詳細を確認してください。</td>
    <td class="tg-lboi" width=55%>- 脆弱性評価ソリューションを仮想マシンで有効にする必要がある<br />- Azure Kubernetes Service クラスターには Kubernetes 用の Azure Policy アドオンがインストールされている必要がある<br />- コンテナー イメージは信頼されたレジストリからのみデプロイする必要がある<br />- Azure Container Registry イメージの脆弱性を修復する必要がある (Qualys を利用)<br />- 仮想マシンの脆弱性を修復する必要がある</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>4</strong></td>
    <td class="tg-lboi"><strong>転送中のデータの暗号化</strong> - これらの推奨事項を使用して、コンポーネント、拠点、またはプログラム間を移動するデータをセキュリティで保護します。 そのようなデータは、中間者攻撃、盗聴、セッション ハイジャックの影響を受けやすいものです。</td>
    <td class="tg-lboi" width=55%>- API アプリには、HTTPS を介してのみアクセスできるようにする必要があります<br />- MySQL データベース サーバーでは [SSL 接続を強制する] を有効にする必要があります<br />- PostgreSQL データベース サーバーでは [SSL 接続を強制する] を有効にする必要があります<br />- API アプリでは FTPS を必須とする必要があります<br />- 関数アプリでは FTPS を必須とする必要があります<br />- Web アプリでは FTPS を必須とする必要があります<br />- Function App には、HTTPS を介してのみアクセスできるようにする必要があります<br />- Redis Cache に対してセキュリティで保護された接続のみを有効にする必要があります<br />- ストレージ アカウントへの安全な転送を有効にする必要があります<br />- API アプリ用に TLS を最新バージョンに更新する必要があります<br />- 関数アプリ用に TLS を最新バージョンに更新する必要があります<br />- Web アプリ用に TLS を最新バージョンに更新する必要があります<br />- Web アプリケーションには、HTTPS を介してのみアクセスできるようにする必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>4</strong></td>
    <td class="tg-lboi"><strong>承認されていないネットワーク アクセスの制限</strong> - Azure には、ネットワーク全体のアクセスが最高のセキュリティ標準を満たすように設計された一連のツールが用意されています。<br>これらの推奨事項を使用して、<a href="/azure/security-center/security-center-adaptive-network-hardening">Security Center のアダプティブ ネットワーク セキュリティ強化</a>の設定を管理する、関連するすべての PaaS サービスに対して <a href="/azure/private-link/private-link-overview">Azure Private Link</a> が構成済みであることを確認する、仮想ネットワークで <a href="/azure/firewall/overview">Azure Firewall</a> を有効にするなどの対策を講じます。</td>
    <td class="tg-lboi" width=55%>- アダプティブ ネットワーク強化の推奨事項をインターネットに接続する仮想マシンに適用する必要があります<br />- 仮想マシンに関連付けられたネットワーク セキュリティ グループでは、すべてのネットワーク ポートを制限する必要がある<br />- App Configuration ではプライベート リンクを使用する必要がある<br />- Azure Cache for Redis は仮想ネットワーク内に存在しなければならない<br />- Azure Event Grid ドメインではプライベート リンクを使用する必要がある<br />- Azure Event Grid トピックではプライベート リンクを使用する必要がある<br />- Azure Kubernetes Service クラスターには Kubernetes 用の Azure Policy アドオンがインストールされている必要がある<br />- Azure Machine Learning ワークスペースではプライベート リンクを使用する必要がある<br />- Azure SignalR Service ではプライベート リンクを使用する必要がある<br />- Azure Spring Cloud ではネットワークの挿入を使用する必要がある<br />- コンテナー レジストリでは無制限のネットワーク アクセスを許可しない<br />- コンテナー レジストリではプライベート リンクを使用する必要がある<br />- コンテナーは許可されたポートでのみリッスンする必要がある<br />- CORS で、必ずしもすべてのリソースに API アプリへのアクセスを許可しないようにする必要があります<br />- CORS で、必ずしもすべてのリソースに Function App へのアクセスを許可しないようにする必要があります<br />- CORS で Web アプリケーションへのアクセスをすべてのリソースには許可しない<br />- キー コンテナーでファイアウォールを有効にする必要がある<br />- インターネットに接続されている仮想マシンをネットワーク セキュリティ グループで保護する必要がある<br />-お使いの仮想マシンでの IP 転送を無効にする必要があります<br />- Kubernetes API サーバーは制限付きアクセスで構成する必要がある<br />- キー コンテナー用にプライベート エンドポイントを構成する必要がある<br />- MariaDB サーバーに対してプライベート エンドポイントを有効にする必要があります<br />- MySQL サーバーに対してプライベート エンドポイントを有効にする必要があります<br />- PostgreSQL サーバーに対してプライベート エンドポイントを有効にする必要があります<br />- MariaDB サーバーでは、公衆ネットワーク アクセスを無効にする必要がある<br />- MySQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある<br />- PostgreSQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある<br />- サービスは許可されたポートでのみリッスンする必要がある<br />- ストレージ アカウントではプライベート リンク接続を使用する必要がある<br />- ストレージ アカウントでは、仮想ネットワーク ルールを使用してネットワーク アクセスを制限する必要がある<br />- ホスト ネットワークとポートの使用を制限する必要がある<br />- 仮想ネットワークは、Azure Firewall によって保護する必要がある<br />- VM Image Builder テンプレートでは、プライベート リンクを使用する必要がある</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>4</strong></td>
    <td class="tg-lboi"><strong>保存時の暗号化の有効化</strong> - これらの推奨事項を使用して、格納されているデータの保護に関する構成ミスを減らします。</td>
    <td class="tg-lboi" width=55%>- Service Fabric クラスターでは、ClusterProtectionLevel プロパティを EncryptAndSign に設定する必要があります<br />- SQL データベースで Transparent Data Encryption を有効にする必要があります<br />- コンピューティングとストレージのリソース間で一時ディスク、キャッシュ、データ フローを仮想マシンによって暗号化する必要がある</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>4</strong></td>
    <td class="tg-lboi"><strong>アクセスおよびアクセス許可の管理</strong> - セキュリティ プログラムの中核となるのは、ユーザーがそのジョブを実行するために必要なアクセスを付与しても、それ以上の権利を付与しないことです。つまり、最小限の特権モデルに従う必要があります。 これらの推奨事項を使用して、ID とアクセスの要件を管理します。</td>
    <td class="tg-lboi" width=55%>- Linux マシンに対する認証では SSH キーを要求する必要がある<br />- Azure Kubernetes Service クラスターには Kubernetes 用の Azure Policy アドオンがインストールされている必要がある<br />- 特権エスカレーションを含むコンテナーは避ける必要がある<br />- 機密性の高いホストの名前空間を共有するコンテナーは避ける必要がある<br />- 非推奨のアカウントをサブスクリプションから削除する必要がある<br />- 非推奨のアカウントをサブスクリプションから削除する必要がある<br />- 所有者アクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある<br />- 所有者アクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある<br />- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある<br />- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある<br />- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある<br />- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある<br />- 関数アプリでクライアント証明書 (着信クライアント証明書) を有効にする必要がある<br />- ゲスト構成拡張機能をマシンにインストールする必要がある<br />- コンテナーで不変 (読み取り専用) のルート ファイル システムを適用する必要がある<br />- コンテナーで最小限の特権を持つ Linux 機能を適用する必要がある<br />- API アプリではマネージド ID を使用する必要がある<br />- 関数アプリではマネージド ID を使用する必要がある<br />- Web アプリではマネージド ID を使用する必要がある<br />- 特権コンテナーの使用を避ける必要がある<br />- Kubernetes Services ではロールベースのアクセス制御を使用する必要がある<br />- コンテナーをルート ユーザーとして実行しない<br />- Service Fabric クラスターでは、クライアント認証に Azure Active Directory のみを使用する必要があります<br />-サブスクリプションを保護するには、管理証明書ではなくサービス プリンシパルを使用する必要があります<br />- ストレージ アカウントのパブリック アクセスを禁止する必要がある<br />- ポッドの HostPath ボリューム マウントの使用を既知のリストに制限して、侵害されたコンテナーからのノード アクセスを制限する必要がある<br />- 仮想マシンのゲスト構成拡張機能はシステム割り当てマネージド ID を使用してデプロイする必要がある</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>4</strong></td>
    <td class="tg-lboi"><strong>セキュリティ構成の修復</strong> - 正しく構成されていない IT 資産は、攻撃の対象となる危険性が高くなります。 これらの推奨事項を使用して、インフラストラクチャ全体で識別された構成ミスを修正します。</td>
    <td class="tg-lboi" width=55%>- Azure Kubernetes Service クラスターには Kubernetes 用の Azure Policy アドオンがインストールされている必要がある<br />- Log Analytics エージェントを Linux ベースの Azure Arc マシンにインストールする必要がある<br />- 仮想マシンに Log Analytics エージェントをインストールする必要がある<br />- 仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある<br />- Log Analytics エージェントを Windows ベースの Azure Arc マシンにインストールする必要がある<br />- コンテナーの AppArmor プロファイルのオーバーライドまたは無効化を制限する必要がある<br />- Kubernetes Services では、ポッドのセキュリティ ポリシーを定義する必要がある (非推奨)<br />- SQL データベースでは脆弱性の検出結果を解決する必要がある<br />- マシン上の SQL サーバーでは脆弱性の検出結果を解決する必要がある<br />- コンテナーのセキュリティ構成の脆弱性を修復する必要があります<br />- Linux マシンのセキュリティ構成の脆弱性を修復する必要がある (ゲスト構成を利用)<br />- お使いのマシンでセキュリティ構成の脆弱性を修復する必要があります<br />- Virtual Machine Scale Sets のセキュリティ構成の脆弱性を修復する必要があります<br />- Windows マシンのセキュリティ構成の脆弱性を修復する必要がある (ゲスト構成を利用)<br />- SQL Managed Instance で脆弱性評価を有効にする必要があります<br />- SQL サーバーで脆弱性評価を有効にする必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>3</strong></td>
    <td class="tg-lboi"><strong>適応型アプリケーション制御の適用</strong> - 適応型アプリケーション制御は、どのアプリケーションをマシンで実行できるかを制御するための、インテリジェントで、自動化された、エンドツーエンドのソリューションです。 また、これは、マルウェアに対してマシンを強化するためにも役立ちます。</td>
    <td class="tg-lboi" width=55%>- 安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある<br />- 適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある<br />- Log Analytics エージェントを Linux ベースの Azure Arc マシンにインストールする必要がある<br />- 仮想マシンに Log Analytics エージェントをインストールする必要がある<br />- Log Analytics エージェントを Windows ベースの Azure Arc マシンにインストールする必要がある</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>2</strong></td>
    <td class="tg-lboi"><strong>DDoS 攻撃からのアプリケーションの保護</strong> - Azure の高度なネットワーク ソリューションには、<a href="/azure/ddos-protection/ddos-protection-overview"></a>Azure DDoS Protection</a>、<a href="/azure/web-application-firewall/overview"></a>Azure Web Application Firewall</a>、<a href="/azure/governance/policy/concepts/policy-for-kubernetes"></a>Azure Policy Add-on for Kubernetes</a> が含まれます。 これらの推奨事項を使用して、アプリケーションがこれらのツールやその他の手段によって保護されていることを確認します。</td>
    <td class="tg-lboi" width=55%>- Azure DDoS Protection Standard を有効にする必要がある<br />- Azure Kubernetes Service クラスターには Kubernetes 用の Azure Policy アドオンがインストールされている必要がある<br />- コンテナーの CPU とメモリの制限を強制する必要がある<br />- Application Gateway に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある<br />- Azure Front Door Service サービスに対して Web Application Firewall (WAF) を有効にする必要がある</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>2</strong></td>
    <td class="tg-lboi"><strong>Endpoint Protection の有効化</strong> - Security Center ではお客様の組織のエンドポイントを検査し、アクティブな脅威検出および対応ソリューションがないか調べます。たとえば、<a href="/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions-">こちらの一覧</a>で示されている Microsoft Defender for Endpoint やその他の主要なソリューションです。<br>エンドポイントでの検出と対応 (EDR) ソリューションが見つからない場合は、これらの推奨事項を使用して、Microsoft Defender for Endpoint をデプロイします (これは、<a href="/azure/security-center/defender-for-servers-introduction">Azure Defender for servers</a> の一部として含まれています)。<br>このコントロールの他の推奨事項は、Log Analytics エージェントのデプロイや、<a href="/azure/security-center/security-center-file-integrity-monitoring">ファイルの整合性の監視</a>構成を行う上で役立ちます。</td>
    <td class="tg-lboi" width=55%>- Virtual Machine Scale Sets で Endpoint Protection の正常性エラーを修復する必要があります<br />- - お使いのマシンで Endpoint Protection の正常性の問題を解決する必要があります<br />- - お使いのマシンで Endpoint Protection の正常性の問題を解決する必要があります<br />- エンドポイント保護をマシンにインストールする必要がある<br />- Virtual Machine Scale Sets に Endpoint Protection ソリューションをインストールする必要があります<br />- サーバーでファイルの整合性の監視を有効にする必要がある<br />- 仮想マシンに Endpoint Protection ソリューションをインストールします<br />- お使いのマシンに Endpoint Protection ソリューションをインストールします<br />- Log Analytics エージェントを Linux ベースの Azure Arc マシンにインストールする必要がある<br />- 仮想マシンに Log Analytics エージェントをインストールする必要がある<br />- 仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある<br />- Log Analytics エージェントを Windows ベースの Azure Arc マシンにインストールする必要がある</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>1</strong></td>
    <td class="tg-lboi"><strong>監査とログの有効化</strong> - 詳細ログは、インシデント調査とその他の多くのトラブルシューティング操作の重要な部分です。 このコントロールの推奨事項は、必要な場面で診断ログが有効にされていたかどうかを確認することに焦点を当てています。</td>
    <td class="tg-lboi" width=55%>- SQL Server の監査を有効にする必要があります<br />- Azure Data Lake Store の診断ログを有効にする必要があります<br />- Azure Stream Analytics の診断ログを有効にする必要があります<br />- Batch アカウントの診断ログを有効にする必要があります<br />- Data Lake Analytics の診断ログを有効にする必要があります<br />- イベント ハブの診断ログを有効にする必要があります<br />- Key Vault の診断ログを有効にする必要があります<br />- Search サービスにおける診断ログを有効にする必要がある<br />- Service Bus の診断ログを有効にする必要があります<br />- Virtual Machine Scale Sets の診断ログを有効にする必要があります<br />- ロジック アプリの診断ログを有効にする必要がある<br />- App Service の診断ログを有効にする必要がある</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>0</strong></td>
    <td class="tg-lboi"><strong>セキュリティのベスト プラクティスの実装</strong> - このコントロールは、お客様のセキュア スコアに影響しません。 そのため、これは、お客様の組織のセキュリティにとっては実行するのが重要ではあるものの、スコア全体を評価する方法の一部とすべきではないと考えられる推奨事項のコレクションです。</td>
    <td class="tg-lboi" width=55%>- [必要に応じて有効にする] Cosmos DB アカウントでは、カスタマー マネージド キーを使用して保存データを暗号化する必要がある<br />- [必要に応じて有効にする] Azure Machine Learning ワークスペースは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある<br />- [必要に応じて有効にする] Cognitive Services アカウントでカスタマー マネージド キー (CMK) によるデータ暗号化を有効にする必要がある<br />- [必要に応じて有効にする] コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある<br />- [必要に応じて有効にする] MySQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br />- [必要に応じて有効にする] PostgreSQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br />- [必要に応じて有効にする] SQL マネージド インスタンスでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br />- [必要に応じて有効にする] SQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br />- [必要に応じて有効にする] ストレージ アカウントでは暗号化にカスタマー マネージド キー (CMK) を使用する必要がある<br />- サブスクリプションには最大 3 人の所有者を指定する必要があります<br />- ファイアウォールと仮想ネットワークの構成があるストレージ アカウントへのアクセスを制限する必要があります<br />- SQL Managed Instance の Advanced Data Security 設定で、Advanced Threat Protection のすべての種類を有効にする必要があります<br />- SQL Server の Advanced Data Security 設定では、Advanced Threat Protection のすべての種類を有効にする必要があります<br />- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要があります<br />- API Management サービスには仮想ネットワークが使用されている必要がある<br />- SQL サーバーの監査の保有期間は少なくとも 90 日に設定する必要があります<br />- 自分のサブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある<br />- Automation アカウント変数を暗号化する必要があります<br />- 仮想マシンに対して Azure Backup を有効にする必要があります<br />- Azure Cosmos DB のアカウントにはファイアウォール規則を含める必要がある<br />-保護されていない Azure SQL サーバーに対して Microsoft Defender for SQLする必要があります<br />-保護されていないマネージド SQLに対して Microsoft Defender for SQL有効にする必要がある<br />- Cognitive Services アカウントでデータ暗号化を有効にする必要がある<br />- Cognitive Services アカウントでネットワーク アクセスを制限する必要がある<br />- Cognitive Services アカウントで、顧客所有のストレージを使用するか、データ暗号化を有効にする必要がある<br />- 既定の IP フィルター ポリシーを拒否にする必要がある<br />- IoT Hub の診断ログを有効にする必要があります<br />- 重要度が高いアラートの電子メール通知を有効にする必要がある<br />- 重要度が高いアラートについて、サブスクリプション所有者に対する電子メール通知を有効にする必要がある<br />- API アプリでクライアント証明書、着信クライアント証明書がオンに設定されていることを確認する<br />- - 読み取りアクセス許可を持つ外部アカウントをお使いのサブスクリプションから除外する必要があります<br />- - 読み取りアクセス許可を持つ外部アカウントをお使いのサブスクリプションから除外する必要があります<br />- Azure Database for MariaDB の geo 冗長バックアップを有効にする必要があります<br />- Azure Database for MySQL の geo 冗長バックアップを有効にする必要があります<br />- Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要があります<br />- サポートされている Linux 仮想マシンのスケール セットに Guest Attestation 拡張機能をインストールする必要がある<br />- Guest Attestation 拡張機能を、サポートしている Linux 仮想マシンにインストールする必要がある<br />- サポートされている Windows 仮想マシンのスケール セットに Guest Attestation 拡張機能をインストールする必要がある<br />- Guest Attestation 拡張機能を、サポートしている Windows 仮想マシンにインストールする必要がある<br />- ゲスト構成拡張機能をマシンにインストールする必要がある<br />- 認証の資格情報が同一<br />- IoT デバイス - エージェントで低使用率のメッセージが送信されている<br />- IoT デバイス - Auditd プロセスでイベントの送信が停止された<br />- IoT デバイス - デバイス上でポートを開く<br />- IoT デバイス - オペレーティング システムのベースラインの検証に失敗した<br />- IoT デバイス - チェーンのうちの 1 つに制限の緩すぎるファイアウォール ポリシーが見つかりました<br />- IoT デバイス - 入力チェーンに制限の緩すぎるファイアウォール ルールが見つかりました<br />- IoT デバイス - 出力チェーンに制限の緩すぎるファイアウォール ルールが見つかりました<br />- IoT デバイス - TLS 暗号スイートのアップグレードが必要<br />- IP フィルター ルールの IP 範囲が広い<br />- API アプリ用に Java を最新バージョンに更新する必要があります<br />- 関数アプリ用に Java を最新バージョンに更新する必要があります<br />- Web アプリ用に Java を最新バージョンに更新する必要があります<br />- Key Vault キーには有効期限が必要である<br />- Key Vault シークレットには有効期限が必要である<br />- キー コンテナーで消去保護が有効になっている必要がある<br />- キー コンテナーで論理的な削除が有効になっている必要がある<br />- Kubernetes クラスターは HTTPS 経由でのみアクセス可能である必要がある<br />- Kubernetes クラスターで API 資格情報の自動マウントを無効にする必要がある<br />- Kubernetes クラスターでは、CAPSYSADMIN セキュリティ機能を許可しない<br />- Kubernetes クラスターでは既定の名前空間を使用しない<br />- Linux 仮想マシンでカーネル モジュール署名の検証を強制する必要がある<br />- Linux 仮想マシンでは、署名された信頼できるブート コンポーネントのみを使用する必要がある<br />- Linux 仮想マシンではセキュア ブートを使用する必要がある<br />- セキュリティ構成の更新を適用するにはマシンを再起動する必要がある<br />- お使いのサブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要があります<br />- お使いのサブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要があります<br />- ネットワーク トラフィック データ収集エージェントを Linux 仮想マシンにインストールする必要がある<br />- ネットワーク トラフィック データ収集エージェントを Windows 仮想マシンにインストールする必要がある<br />- Network Watcher を有効にする必要がある<br />- インターネットに接続されていない仮想マシンをネットワーク セキュリティ グループで保護する必要があります<br />- API アプリ用に PHP を最新バージョンに更新する必要があります<br />- Web アプリ用に PHP を最新バージョンに更新する必要があります<br />- Azure SQL Database のプライベート エンドポイント接続を有効にする必要がある<br />- Azure SQL Database のパブリック ネットワーク アクセスを無効にする必要がある<br />- Cognitive Services アカウントに対して公衆ネットワーク アクセスを無効にする必要がある<br />- API アプリ用に Python を最新バージョンに更新する必要があります<br />- 関数アプリ用に Python を最新バージョンに更新する必要があります<br />- Web アプリ用に Python を最新バージョンに更新する必要があります<br />-API アプリのリモート デバッグを無効にする必要があります<br />- Function App のリモート デバッグを無効にする必要があります<br />- Web アプリケーションでリモート デバッグを無効にする必要がある<br />- セキュア ブートはサポートされている Windows 仮想マシンで有効にする必要がある<br />- ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要があります<br />- サブネットはネットワーク セキュリティ グループに関連付けられている必要がある<br />- サブスクリプションには、セキュリティの問題に備えて連絡先メール アドレスが用意されている必要がある<br />- 複数の所有者がサブスクリプションに割り当てられている必要があります<br />- Azure Key Vault に保存されている証明書の有効期間は 12 か月以内にする必要がある<br />- 仮想マシンのゲスト構成証明の状態は正常である必要がある<br />- 仮想マシンを新しい Azure Resource Manager リソースに移行する必要があります<br />- 仮想マシンのゲスト構成拡張機能はシステム割り当てマネージド ID を使用してデプロイする必要がある<br />- vTPM はサポートされている仮想マシンで有効にする必要がある<br />- Web アプリではすべての受信要求に対して SSL 証明書を要求する必要があります<br />- マシンで Windows Defender Exploit Guard を有効にする必要がある<br />- Windows Web サーバーをセキュリティで保護された通信プロトコルを使用するように構成する必要がある</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>0</strong></td>
    <td class="tg-lboi"><strong>データ分類の適用</strong> - 組織のデータを機密性とビジネスへの影響によって分類すると、データの価値を決定して割り当てることができ、ガバナンスのための戦略と基準を策定できます。</td>
    <td class="tg-lboi" width=55%>- SQL データベースの機密データを分類する必要がある</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>0</strong></td>
    <td class="tg-lboi"><strong>強化されたセキュリティ機能を有効にする</strong>- これらの推奨事項を使用して <a href="/azure/security-center/azure-defender">、Microsoft Defender プランを有効にしてください</a>。</td>
    <td class="tg-lboi" width=55%>- Azure Arc Kubernetes クラスターに Defender の拡張機能がインストールされている必要がある<br />- Azure Defender for App Service を有効にする必要がある<br />- Azure Defender for Azure SQL Database サーバーを有効にする必要がある<br />- コンテナー レジストリ用 Azure Defender を有効にする必要がある<br />- Azure Defender for DNS を有効にする必要がある<br />- Azure Defender for Key Vault を有効にする必要がある<br />- Azure Defender for Kubernetes を有効にする必要がある<br />- Azure Defender for Resource Manager を有効にする必要がある<br />- サーバー用 Azure Defender を有効にする必要がある<br />- ワークスペースで Azure Defender for servers を有効にする必要がある<br />- マシン上の Azure Defender for SQL サーバーを有効にする必要がある<br />- Azure Defender for Storage を有効にする必要がある</td>
  </tr>
</tbody>
</table>
