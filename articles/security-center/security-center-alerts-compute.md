---
title: Azure Security Center でのクラウド ネイティブ コンピューティングの脅威検出 | Microsoft Docs
description: この記事では、Azure Security Center で使用可能なクラウド ネイティブ コンピューティングのアラートについて説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: memildin
ms.openlocfilehash: 6b6acb0ae1452795fe02906779b920e4b41f9a55
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748395"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Azure Security Center でのクラウド ネイティブ コンピューティングの脅威検出

Azure Security Center はネイティブ ソリューションとして、内部ログに対する独自の可視性を備え、複数のターゲットに対する攻撃手法を識別します。 この記事では、次の Azure サービスで使用可能なアラートについて説明します。

* [Azure App Service](#app-services)
* [Azure コンテナー](#azure-containers) 

> [!NOTE]
> Azure Government およびソブリン クラウド リージョンでは現在、これらのサービスを利用できません。

## Azure App Service<a name="app-services"></a>

Security Center では、クラウドのスケールを使用して、App Service で実行されるアプリケーションをターゲットとした攻撃が識別されます。 攻撃者は、Web アプリケーションをプローブして弱点を発見し、悪用します。 Azure で実行されているアプリケーションに対する要求は、特定の環境にルーティングされる前に、複数のゲートウェイを通過し、そこで検査され、ログに記録されます。 その後、このデータは、悪用や攻撃者を特定し、後で使用される新しいパターンを学習するために使用されます。

Security Center では、Azure がクラウド プロバイダーとして備える可視性を使用して、App Service の内部ログが分析され、複数のターゲットに対する攻撃手法が識別されます。 たとえば、広範囲にわたるスキャンや分散型の攻撃などの手法です。 この種の攻撃は通常、IP の小さなサブセットから発生し、複数のホスト上の類似のエンドポイントをクロールするパターンを示します。 攻撃では脆弱なページやプラグインが検索され、単一のホストの観点からは攻撃を特定できません。

Security Center では、基になるサンド ボックスおよび VM にアクセスすることもできます。 メモリ フォレンジクスと組み合わせることで、インフラストラクチャは、流行している新しい攻撃から、お客様のマシンの侵害まで、状況を通知できます。 したがって、Web アプリが悪用された後に Security Center がデプロイされたとしても、進行中の攻撃を検出できる可能性があります。

> [!div class="mx-tableFixed"]

|アラート:|説明|
|---|---|
|**Suspicious WordPress theme invocation detected (不審な WordPress テーマの呼び出しが検出されました)**|App Service のアクティビティ ログは、App Service リソースに対するコード インジェクション アクティビティの可能性を示しています。<br/> この不審なアクティビティは、WordPress テーマを操作してサーバー側でのコード実行をサポートし、操作されたテーマ ファイルを呼び出すための直接の Web 要求を行うアクティビティに似ています。 この種のアクティビティは、WordPress での攻撃活動の一部である可能性があります。|
|**Connection to web page from anomalous IP address detected (異常な IP アドレスからの Web ページへの接続が検出されました)**|App Service のアクティビティ ログは、これまで接続されたことのないソース アドレスから機密性の高い Web ページへの接続を示しています。 この接続は、何者かが Web アプリの管理ページにブルート フォース攻撃を試みていることを示している可能性があります。 また、正当なユーザーが新しい IP アドレスを使用した結果である可能性もあります。|
|**An IP that connected to your Azure App Service FTP Interface was found in Threat Intelligence (脅威インテリジェンスで、Azure App Service の FTP インターフェイスに接続した IP が見つかりました)**|App Service の FTP ログ分析で、脅威インテリジェンス フィードで見つかったソース アドレスからの接続が検出されました。 この接続中に、ユーザーが、一覧表示されているページにアクセスしました。|
|**Web fingerprinting detected (Web フィンガー プリントが検出されました)**|App Service のアクティビティ ログは、App Service リソースに対する Web フィンガー プリント アクティビティの可能性を示しています。 <br/>この不審なアクティビティは、Blind Elephant と呼ばれるツールに関連しています。 このツールは、Web サーバーのフィンガープリントを取得し、インストールされているアプリケーションとそのバージョンを検出しようとします。 攻撃者はこのツールを使用して Web アプリケーションを調べ、脆弱性を見つけることがよくあります。|
|**Suspicious access to possibly vulnerable web page detected (脆弱な可能性がある Web ページへの不審なアクセスが検出されました)**|App Service のアクティビティ ログは、機密性が高いと思われる Web ページがアクセスされたことを示しています。 <br/>この不審なアクティビティは、Web スキャナーのアクセス パターンに似たアクセス パターンを持つソース アドレスから発生しています。 この種のアクティビティは、多くの場合、攻撃者がネットワークをスキャンして、機密性が高いか、脆弱な Web ページへのアクセス権を取得しようとする試みと関連しています。|
|**PHP file in upload folder (アップロード フォルダー内の PHP ファイル)**|App Service のアクティビティ ログは、アップロード フォルダー内にある不審な PHP ページに何かがアクセスしたことを示しています。 <br/>この種のフォルダーには通常、PHP ファイルは含まれません。 この種のファイルが存在する場合は、任意のファイル アップロードの脆弱性を利用した悪用を示している可能性があります。|
|**An attempt to run Linux commands on a Windows App Service (Windows App Service での Linux コマンドの実行試行)**|App Service のプロセスの分析で、Windows App Service での Linux コマンドの実行試行が検出されました。 このアクションは、Web アプリケーションにより実行されていました。 この動作は、一般的な Web アプリケーションの脆弱性を悪用する活動でよく見られます。|
|**Suspicious PHP execution detected (疑わしい PHP の実行が検出されました)**|マシン ログは、不審な PHP プロセスが実行されていることを示しています。 このアクションには、PHP プロセスを使用してコマンドラインからオペレーティング システム コマンドまたは PHP コードを実行する試みが含まれていました。 この動作は、正当である可能性もありますが、Web アプリケーションでは、この動作は Web シェルで Web サイトを感染させようとするなどの悪意のあるアクティビティを示している可能性があります。|
|**Process execution from temporary folder (一時フォルダーからのプロセスの実行)**|App Service のプロセスの分析で、アプリの一時フォルダーからのプロセスの実行が検出されました。 この動作は、正当である可能性もありますが、Web アプリケーションでは、この動作は悪意のあるアクティビティを示している可能性があります。|
|**Attempt to run high privilege command detected (高い特権のコマンドの実行試行が検出されました)**|App Service のプロセスの分析で、高い特権が必要なコマンドの実行試行が検出されました。 このコマンドは、Web アプリケーションのコンテキストで実行されました。 この動作は、正当である可能性もありますが、Web アプリケーションでは、この動作は悪意のあるアクティビティを示している可能性があります。|

## Azure コンテナー <a name="azure-containers"></a>

Security Center は、コンテナー化された環境に対するリアルタイムの脅威検出機能を備えており、不審なアクティビティに対してはアラートを生成します。 ユーザーは、この情報を使用して、迅速にセキュリティの問題を修復し、コンテナーのセキュリティを強化することができます。

さまざまなレベルの脅威が検出されます。 

* **ホスト レベル** - Security Center のエージェント (Standard レベルで利用可能。詳細については、[価格](security-center-pricing.md)に関するページを参照) が、Linux に対する不審なアクティビティを監視します。 ノードまたはそこで実行されているコンテナーを発生源とする不審なアクティビティについては、エージェントによってアラートがトリガーされます。 そのようなアクティビティとしては、たとえば、Web シェルの検出や既知の不審な IP アドレスとの接続が挙げられます。

    コンテナー化された環境のセキュリティについて、より詳しい分析情報を得るために、エージェントは、コンテナー固有の分析情報を監視します。 特権コンテナーの作成、API サーバーへの不審なアクセス、Docker コンテナー内での SSH (Secure Shell) サーバーの実行などのイベントが発生すると、アラートがトリガーされます。

    >[!NOTE]
    > ホストにエージェントをインストールしなかった場合、脅威検出によってもたらされるメリットとアラートは限定されます。 その場合でも、ネットワーク分析や悪意のあるサーバーとの通信に関連したアラートは通知されます。

* **AKS クラスター レベル** - Kubernetes の監査ログ分析に基づく脅威検出の監視が行われます。 この**エージェントレス**の監視を有効にするには、 **[価格と設定]** ページから、ご利用のサブスクリプションに Kubernetes オプションを追加してください ([価格](security-center-pricing.md)に関するページを参照)。 このレベルのアラートを生成するために、Security Center は、AKS によって取得されたログを使用して、AKS のマネージド サービスを監視します。 このレベルのイベントとしては、たとえば、Kubernetes ダッシュボードの露出、高い特権ロールの作成、機密情報にまつわるマウントの作成が挙げられます。

    >[!NOTE]
    > Azure Kubernetes Service のアクションやデプロイについての検出アラートが Security Center から生成されるのは、サブスクリプションの設定で Kubernetes オプションが有効にされた後になります。 

また、Microsoft のセキュリティ研究員から成るグローバル チームも、脅威の状況を絶えず監視しています。 コンテナー固有のアラートや脆弱性は、それらが検出された時点で追加されます。


### <a name="aks-cluster-level-alerts"></a>AKS クラスター レベルのアラート

> [!div class="mx-tableFixed"]

|アラート:|説明|
|---|---|
|**PREVIEW - Role binding to the cluster-admin role detected (プレビュー - クラスター管理者ロールへのロール バインドが検出されました)**|Kubernetes 監査ログ分析によって、管理者特権をもたらす、クラスター管理者ロールへの新しいバインドが検出されました。 不必要な管理者特権を提供すると、クラスターで特権エスカレーションの問題が発生する可能性があります。|
|**PREVIEW - Exposed Kubernetes dashboard detected (プレビュー - 公開された Kubernetes ダッシュボードが検出されました)**|Kubernetes 監査ログ分析によって、LoadBalancer サービスによる Kubernetes ダッシュボードの公開が検出されました。 ダッシュボードが公開されると、クラスター管理への認証されていないアクセスが可能になり、セキュリティ上の脅威が生じます。|
|**PREVIEW - New high privileges role detected (プレビュー - 新しい高い特権のロールが検出されました)**|Kubernetes 監査ログ分析によって、高い特権を持つ新しいロールが検出されました。 高い特権を持つロールにバインドすると、クラスター内のユーザーやグループに、昇格された特権が付与されます。 不必要に昇格された特権を提供すると、クラスターで特権エスカレーションの問題が発生する可能性があります。|
|**PREVIEW - New container in the kube-system namespace detected (プレビュー - kube-system 名前空間で新しいコンテナーが検出されました)**|Kubernetes 監査ログ分析によって、kube-system 名前空間に、通常はこの名前空間で実行されない新しいコンテナーが検出されました。 kube-system 名前空間にユーザー リソースを含めることはできません。 攻撃者はこの名前空間を使用して、悪意のあるコンポーネントを隠すことができます。|
|**PREVIEW - Digital currency mining container detected (プレビュー - デジタル通貨マイニング コンテナーが検出されました)**|Kubernetes 監査ログ分析によって、デジタル通貨マイニング ツールに関連付けられているイメージを含むコンテナーが検出されました。|
|**PREVIEW - Privileged container detected (プレビュー - 特権コンテナーが検出されました)**|Kubernetes 監査ログ分析によって、新しい特権コンテナーが検出されました。 特権コンテナーは、ノードのリソースにアクセスできるため、コンテナー間の分離が破壊されます。 侵害された場合、攻撃者はノードにアクセスするために、特権コンテナーを使用できます。|
|**PREVIEW - Container with a sensitive volume mount detected (プレビュー - 機密のボリューム マウントを持つコンテナーが検出されました)**|Kubernetes 監査ログ分析によって、機密のボリューム マウントを持つ新しいコンテナーが検出されました。 検出されたボリュームは、機密性の高いファイルまたはフォルダーをノードからコンテナーにマウントする hostPath 型です。 コンテナーが侵害された場合、攻撃者はこのマウントを使用して、ノードにアクセスできます。|



### <a name="host-level-alerts"></a>ホスト レベルのアラート

> [!div class="mx-tableFixed"]

|アラート:|説明|
|---|---|
|**Privileged Container Detected (特権コンテナーが検出されました)**|マシンのログは、アクセス許可された Docker コンテナーが実行していることを示しています。 特権コンテナーは、ホストのリソースへのフル アクセスを持ちます。 侵害された場合、攻撃者はホスト マシンへのアクセス許可を得るために、アクセス許可されたコンテナーを使用できます。|
|**Privileged command run in container (コンテナーで特権コマンドが実行されました)**|マシンのログは、Docker コンテナーで特権コマンドが実行されたことを示しています。 特権コマンドには、ホスト マシンに対する拡張特権があります。|
|**Exposed Docker daemon detected (公開されている Docker デーモンが検出されました)**|マシンのログは、Docker デーモン (dockerd) が TCP ソケットを公開していることを示します。 既定では、Docker の構成で、TCP ソケットが有効である場合には暗号化または認証は使用されません。 そのため、関連するポートへのアクセス権を持つすべてのユーザーが、Docker デーモンへのフル アクセスを取得できます。|
|**SSH server is running inside a container (コンテナー内で SSH サーバーが実行されています)**|マシンのログは、Docker コンテナー内で SSH サーバーが実行されていることを示しています。 この動作が意図的である場合もありますが、多くの場合は、コンテナーの構成が正しくないか違反していることを示しています。|
|**Container with a miner image detected (マイナー イメージを持つコンテナーが検出されました)**|マシンのログは、デジタル通貨マイニングに関連付けられているイメージを実行している Docker コンテナーの実行を示しています。 この動作は、リソースが悪用されていることを示している可能性があります。|
|**Suspicious request to Kubernetes API (Kubernetes API への疑わしい要求)**|マシンのログは、Kubernetes API に対して疑わしい要求が行われたことを示しています。 要求は、Kubernetes ノードから送信されました。ノードで実行されているいずれかのコンテナーからのものである可能性があります。 この動作が意図的である場合もありますが、侵害されたコンテナーをノードが実行していることを示している可能性があります。|
|**Suspicious request to the Kubernetes Dashboard (Kubernetes ダッシュボードへの疑わしい要求)**|マシンのログは、Kubernetes ダッシュボードに対して疑わしい要求が行われたことを示しています。 要求は、Kubernetes ノードから送信されました。ノードで実行されているいずれかのコンテナーからのものである可能性があります。 この動作が意図的である場合もありますが、侵害されたコンテナーをノードが実行していることを示している可能性があります。|