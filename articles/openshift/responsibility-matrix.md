---
title: Azure Red Hat OpenShift の責任分担表
description: Azure Red Hat OpenShift クラスターの運用に関する責任の所有権について説明します
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 4/12/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro、OpenShift、az aro、Red Hat、CLI、RACI、サポート
ms.openlocfilehash: 477fe40dc565bf5e20627b4f4cd0d53273240ec3
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108319613"
---
# <a name="overview-of-responsibilities-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift の責任の概要

このドキュメントでは、Azure Red Hat OpenShift クラスターに関する Microsoft、Red Hat、お客様の責任について説明します。 Azure Red Hat OpenShift とそのコンポーネントの詳細については、Azure Red Hat OpenShift サービスの定義を参照してください。

Microsoft と Red Hat は Azure Red Hat OpenShift サービスを管理していますが、お客様はクラスターの機能について責任を共有します。 Azure Red Hat OpenShift クラスターは、お客様の Azure サブスクリプションの Azure リソースでホストされ、リモートでアクセスされます。 基になるプラットフォームとデータ セキュリティは、Microsoft と Red Hat が所有しています。

## <a name="overview"></a>概要
<table>
  <tr>
   <td><strong>リソース</strong>
   </td>
   <td><strong><a href="#incident-and-operations-management">インシデント管理と運用管理</a></strong>
   </td>
   <td><strong><a href="#change-management">変更の管理</a></strong>
   </td>
   <td><strong><a href="#identity-and-access-management">ID 管理とアクセスの管理</a></strong>
   </td>
   <td><strong><a href="#security-and-regulation-compliance">セキュリティと規制への準拠</a></strong>
   </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">お客様のデータ</a>
   </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">お客様のアプリケーション</a>
   </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">開発者サービス</a>
   </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
  </tr>
  <tr>
   <td>プラットフォームの監視 </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
  </tr>
  <tr>
   <td>ログ記録 </td>
   <td>Microsoft と Red Hat </td>
   <td>共有 </td>
   <td>共有 </td>
   <td>共有 </td>
  </tr>
  <tr>
   <td>アプリケーション ネットワーク </td>
   <td>共有 </td>
   <td>共有 </td>
   <td>共有 </td>
   <td>Microsoft と Red Hat </td>
  </tr>
  <tr>
   <td>クラスター ネットワーク </td>
   <td>Microsoft と Red Hat </td>
   <td>共有 </td>
   <td>共有 </td>
   <td>Microsoft と Red Hat </td>
  </tr>
  <tr>
   <td>仮想ネットワーク </td>
   <td>共有 </td>
   <td>共有 </td>
   <td>共有 </td>
   <td>共有 </td>
  </tr>
  <tr>
   <td>コントロール プレーン ノード </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
  </tr>
  <tr>
   <td>ワーカー ノード </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
  </tr>
  <tr>
   <td>クラスターのバージョン </td>
   <td>Microsoft と Red Hat </td>
   <td>共有 </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
  </tr>
  <tr>
   <td>容量管理 </td>
   <td>Microsoft と Red Hat </td>
   <td>共有 </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
  </tr>
  <tr>
   <td>仮想ストレージ </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
  </tr>
  <tr>
   <td>物理インフラストラクチャとセキュリティ </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
   <td>Microsoft と Red Hat </td>
  </tr>
</table>


表 1 リソース別の責任


## <a name="tasks-for-shared-responsibilities-by-area"></a>分野別の共同責任に関するタスク 

### <a name="incident-and-operations-management"></a>インシデント管理と運用管理 

お客様と Microsoft と Red Hat は、Azure Red Hat OpenShift クラスターの監視とメンテナンスについて責任を共有します。 お客様は、[お客様のアプリケーション データ](#customer-data-and-applications)およびお客様が構成したカスタム ネットワークのインシデント管理と運用管理について責任を持ちます。

<table>
  <tr>
   <td><strong>リソース</strong>
   </td>
   <td><strong>Microsoft と Red Hat の責任</strong>
   </td>
   <td><strong>お客様の責任</strong>
   </td>
  </tr>
  <tr>
   <td>アプリケーション ネットワーク </td>
   <td>
<ul>

<li>クラウド ロード バランサーとネイティブ OpenShift ルーター サービスを監視し、アラートに応答する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>サービス ロード バランサー エンドポイントの正常性を監視する。

<li>アプリケーション ルートと、それらの背後にあるエンドポイントの正常性を監視する。

<li>Microsoft と Red Hat に障害を報告する。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>仮想ネットワーク
   </td>
   <td>
<ul>

<li>既定のプラットフォーム ネットワークに必要なクラウド ロード バランサー、サブネット、Azure クラウド コンポーネントを監視し、アラートに応答する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>VNet 間接続、VPN 接続、またはプライベート リンク接続を使用して任意で構成したネットワークのトラフィックに潜在的な問題やセキュリティ上の脅威がないか監視する。
</li>
</ul>
   </td>
  </tr>
</table>


表 2 インシデント管理と運用管理に関する共同責任


### <a name="change-management"></a>変更管理

Microsoft と Red Hat は、お客様が制御するクラスターのインフラストラクチャとサービスを変更可能にすること、およびマスター ノード、インフラストラクチャ サービス、ワーカー ノード用に使用できるバージョンを維持することについて責任を持ちます。 お客様は、インフラストラクチャの変更を開始すること、オプションのサービスとネットワーク構成をクラスターにインストールして保守すること、およびお客様のデータとお客様のアプリケーションに対するすべての変更について責任を持ちます。


<table>
  <tr>
   <td><strong>リソース</strong>
   </td>
   <td><strong>Microsoft と Red Hat の責任</strong>
   </td>
   <td><strong>お客様の責任</strong>
   </td>
  </tr>
  <tr>
   <td>ログ記録 </td>
   <td>
<ul>

<li>プラットフォーム監査ログを一元的に集約して監視する。

<li>Azure Monitor for containers を通じて Log Analytics を使用し、アプリケーションのロギングを有効にするためのドキュメントをお客様に提供する。

<li>お客様の要求に応じて監査ログを提供する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>オプションの既定のアプリケーション ロギング オペレーターをクラスターにインストールする。

<li>ロギング サイドカー コンテナーやサードパーティ製のロギング アプリケーションなど、オプションのアプリ ロギング ソリューションをインストール、構成、保守する。

<li>お客様のアプリケーションによって生成されるアプリケーション ログがクラスターの安定性に影響する場合、そのサイズと頻度を調整する。

<li>特定のインシデントを調査するためのサポート ケースを通じて、プラットフォーム監査ログを要求する。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>アプリケーション ネットワーク
   </td>
   <td>
<ul>

<li>パブリック クラウドのロード バランサーを設定する。

<li>ネイティブ OpenShift ルーター サービスを設定する。 ルーターをプライベートとして設定する機能、および最大 1 つのルーター シャードを追加する機能を提供する。

<li>既定の内部ポッドのトラフィック用に OpenShift SDN コンポーネントをインストール、構成、保守する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>NetworkPolicy オブジェクトを使用して、プロジェクトとポッド ネットワーク、ポッド受信、ポッド送信に関する既定以外のポッド ネットワーク アクセス許可を構成する。

<li>特定のサービス用に追加のサービス ロード バランサーを要求して構成する。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>クラスター ネットワーク
   </td>
   <td>
<ul>

<li>パブリック サービス エンドポイントやプライベート サービス エンドポイントなどのクラスター管理コンポーネント、および仮想ネットワーク コンポーネントとの必要な統合を設定する。

<li>ワーカー ノードとマスター ノードの間の内部クラスター通信に必要な内部ネットワーク コンポーネントを設定する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>クラスターがプロビジョニングされたとき、OpenShift クラスター マネージャーを使用して、必要に応じてマシン CIDR、サービス CIDR、ポッド CIDR の既定以外の IP アドレス範囲を指定する。

<li>Azure CLI を使用してクラスターを作成するとき、またはクラスターを作成した後、API サービス エンドポイントがパブリックまたはプライベートになるように要求する。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>仮想ネットワーク
   </td>
   <td>
<ul>

<li>仮想プライベート クラウド、サブネット、ロード バランサー、インターネット ゲートウェイ、NAT ゲートウェイなど、クラスターをプロビジョニングするために必要な仮想ネットワーク コンポーネントを設定および構成する。

<li>お客様が OpenShift クラスター マネージャーを使用して、必要に応じて、オンプレミス リソースを使用した VPN 接続、VNet 間接続、プライベート リンク接続を管理できるようにする。

<li>お客様が、サービス ロード バランサーで使用するパブリック クラウド ロード バランサーを作成してデプロイできるようにする。
</li>
</ul>
   </td>
   <td>
<ul>

<li>VNet 間接続、VPN 接続、プライベート リンク接続など、オプションのパブリック クラウド ネットワーク コンポーネントを設定および保守する。

<li>特定のサービス用に追加のサービス ロード バランサーを要求して構成する。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>クラスターのバージョン
   </td>
   <td>
<ul>

<li>マイナー バージョンとメンテナンス バージョンのアップグレードのスケジュールと状態を伝達する。

<li>マイナー アップグレードとメンテナンス アップグレードに関する変更ログとリリース ノートを発行する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>クラスターのアップグレードを開始する。

<li>マイナー バージョンとメンテナンス バージョンでお客様のアプリケーションをテストして互換性を確認する。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>容量管理
   </td>
   <td>
<ul>

<li>コントロール プレーン (マスター ノード) の使用率を監視する。

<li>サービス品質を維持するためにコントロール プレーン ノードのスケーリングやサイズ変更を行う。

<li>ネットワーク、ストレージ、コンピューティング容量など、お客様のリソースの使用率を監視する。 自動スケーリング機能が有効になっていない場合、クラスター リソースに必要な変更についてお客様に警告する (例: スケーリングする新しいコンピューティング ノード、追加ストレージなど)。
</li>
</ul>
   </td>
   <td>
<ul>

<li>必要に応じて、提供されている OpenShift クラスター マネージャー コントロールを使用して、ワーカー ノードを追加または削除する。

<li>クラスター リソースの要件に関する Microsoft と Red Hat の通知に応答する。
</li>
</ul>
   </td>
  </tr>
</table>


表 3 変更管理に関する共同責任


### <a name="identity-and-access-management"></a>ID 管理とアクセス管理 

ID 管理とアクセス管理には、適切なユーザーのみがクラスター、アプリケーション、インフラストラクチャ リソースにアクセスできるようにするためのすべての責任が含まれます。 これには、アクセス制御メカニズムの提供、認証、承認、リソースへのアクセスの管理などのタスクが含まれます。


<table>
  <tr>
   <td><strong>リソース</strong>
   </td>
   <td><strong>Microsoft と Red Hat の責任</strong>
   </td>
   <td><strong>お客様の責任</strong>
   </td>
  </tr>
  <tr>
   <td>ログ記録 </td>
   <td>
<ul>

<li>プラットフォーム監査ログについて、業界標準に基づく階層化内部アクセス プロセスに準拠する。

<li>ネイティブの OpenShift RBAC 機能を提供する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>プロジェクトへのアクセスおよび拡張機能によるプロジェクトのアプリケーション ログへのアクセスを制御するために、OpenShift RBAC を構成する。

<li>サードパーティまたはカスタムのアプリケーション ロギング ソリューションについては、お客様がアクセス管理の責任を持つ。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>アプリケーション ネットワーク
   </td>
   <td>
<ul>

<li>ネイティブの OpenShift RBAC 機能を提供する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>必要に応じて、ルート構成へのアクセスを制御するために OpenShift RBAC を構成する。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>クラスター ネットワーク
   </td>
   <td>
<ul>

<li>ネイティブの OpenShift RBAC 機能を提供する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>Red Hat アカウントの Red Hat 組織メンバーシップを管理する。

<li>OpenShift クラスター マネージャーにアクセス権を付与するために、Red Hat 組織の組織管理者を管理する。

<li>必要に応じて、ルート構成へのアクセスを制御するために OpenShift RBAC を構成する。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>仮想ネットワーク
   </td>
   <td>
<ul>

<li>OpenShift クラスター マネージャーを通じて、お客様にアクセス制御を提供する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>OpenShift クラスター マネージャーを通じて、パブリック クラウド コンポーネントへのオプションのユーザー アクセスを管理する。
</li>
</ul>
   </td>
  </tr>
</table>


表 4 ID 管理とアクセス管理に関する共同責任


### <a name="security-and-regulation-compliance"></a>セキュリティと規制への準拠 

セキュリティとコンプライアンスには、関連する法律、ポリシー、規制への準拠を確実に行うための責任と管理が含まれます。


<table>
  <tr>
   <td><strong>リソース</strong>
   </td>
   <td><strong>Microsoft と Red Hat の責任</strong>
   </td>
   <td><strong>お客様の責任</strong>
   </td>
  </tr>
  <tr>
   <td>ログ記録 </td>
   <td>
<ul>

<li>セキュリティ イベントの分析のために、Microsoft および Red Hat SIEM にクラスター監査ログを送信する。 フォレンジック分析をサポートするために、監査ログを定義された期間保持する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>セキュリティ イベントについてアプリケーション ログを分析する。 既定のロギング スタックで提供されるよりも長いリテンション期間が必要な場合に、ロギング サイドカー コンテナーまたはサードパーティ製のロギング アプリケーションを使用して、アプリケーション ログを外部エンドポイントに送信する。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>仮想ネットワーク
   </td>
   <td>
<ul>

<li>仮想ネットワーク コンポーネントに潜在的な問題とセキュリティ上の脅威がないか監視する。

<li>追加の監視と保護のために、Microsoft と Red Hat Azure の追加パブリック ツールを使用する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>任意で構成した仮想ネットワーク コンポーネントに潜在的な問題とセキュリティ上の脅威がないか監視する。

<li>必要に応じて、必要なファイアウォール規則やデータセンターの保護を構成する。
</li>
</ul>
   </td>
  </tr>
</table>


表 5. セキュリティと規制への準拠に関する共同責任


## <a name="customer-responsibilities-when-using-azure-red-hat-openshift"></a>Azure Red Hat OpenShift を使用する場合のお客様の責任 


### <a name="customer-data-and-applications"></a>お客様のデータとアプリケーション

お客様は、ご自分で Azure Red Hat OpenShift にデプロイしたアプリケーション、ワークロード、データについて責任を持ちます。 ただし、Microsoft と Red Hat は、お客様がプラットフォーム上のデータとアプリケーションを管理するのに役立つさまざまなツールを提供しています。


<table>
  <tr>
   <td><strong>リソース</strong>
   </td>
   <td><strong>Microsoft と Red Hat による支援の方法</strong>
   </td>
   <td><strong>お客様の責任</strong>
   </td>
  </tr>
  <tr>
   <td>顧客データ </td>
   <td>
<ul>

<li>業界のセキュリティとコンプライアンスの標準による定義に従って、データ暗号化のプラットフォーム レベルでの標準を維持する。 

<li>シークレットなどのアプリケーション データの管理を支援する OpenShift コンポーネントを提供する。

<li>クラスターの外部や、Microsoft および Red Hat Azure の外部にデータを保存して管理するために、サードパーティのデータ サービス (Azure SQL など) と統合できるようにする。
</li>
</ul>
   </td>
   <td>
<ul>

<li>プラットフォームに保存されているお客様のすべてのデータと、お客様のアプリケーションがそのデータを使用および公開する方法について責任を持つ。

<li>etcd 暗号化
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>お客様のアプリケーション
   </td>
   <td>
<ul>

<li>OpenShift コンポーネントがインストールされたクラスターをプロビジョニングする。これにより、お客様は、OpenShift と Kubernetes の API にアクセスして、コンテナー化されたアプリケーションをデプロイおよび管理できます。

<li>OpenShift API へのアクセスを提供する。お客様は、これを使用して、コミュニティ、サードパーティ、Microsoft と Red Hat、Red Hat のサービスをクラスターに追加するためのオペレーターを設定できます。 

<li>お客様のアプリケーションで使用する永続ボリュームをサポートするために、ストレージ クラスとプラグインを提供する。
</li>
</ul>
   </td>
   <td>
<ul>

<li>お客様のアプリケーションとサードパーティ製のアプリケーション、データ、それらのライフサイクル全体について責任を持つ。

<li>お客様がオペレーターまたは外部イメージを使用して、Red Hat、コミュニティ、サードパーティ、独自、またはその他のサービスをクラスターに追加した場合、お客様はそれらのサービスについて、および問題をトラブルシューティングするために適切なプロバイダー (Red Hat など) と連携することについて責任を持つ。

<li>提供されているツールと機能を使用して、<a href="https://docs.openshift.com/aro/4/architecture/understanding-development.html#application-types">構成とデプロイ</a>、<a href="https://docs.openshift.com/aro/4/applications/deployments/deployment-strategies.html">最新状態の保持</a>、<a href="https://docs.openshift.com/dedicated/4/applications/working-with-quotas.html">リソース要求と制限の設定</a>、<a href="https://docs.openshift.com/dedicated/4/getting_started/scaling-your-cluster.html">アプリを実行する十分なリソースを確保するためのクラスターのサイズ変更</a>、<a href="https://docs.openshift.com/dedicated/4/administering_a_cluster/cluster-admin-role.html">アクセス許可の設定</a>、他のサービスとの統合、<a href="https://docs.openshift.com/aro/4/openshift_images/images-understand.html">お客様がデプロイするイメージ ストリームまたはテンプレートの管理</a>、<a href="https://docs.openshift.com/dedicated/4/cloud_infrastructure_access/dedicated-understanding-aws.html">外部サービス</a>、データの保存、バックアップ、復元、および高可用性と回復性ワークロードの管理を行う。

<li>メトリック収集およびアラート作成のためのソフトウェアのインストールと操作など、Azure Red Hat OpenShift で実行されるアプリケーションの監視について責任を持つ。
</li>
</ul>
   </td>
  </tr>
</table>


表 7. お客様のデータ、お客様のアプリケーション、サービスに関するお客様の責任
