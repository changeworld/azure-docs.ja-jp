---
title: "Azure クラウドで負荷分散サービスを使用する | Microsoft Docs"
description: "このチュートリアルでは、Azure の負荷分散のポートフォリオである、Traffic Manager、Application Gateway、ロード バランサーを使用したシナリオの作成方法を説明します。"
services: traffic-manager
documentationcenter: 
author: liumichelle
manager: vitinnan
editor: 
ms.assetid: f89be3be-a16f-4d47-bcae-db2ab72ade17
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 90918f6bb23f0f4e3c009506e1b12c028c45f02b

---

# <a name="using-load-balancing-services-in-the-azure-cloud"></a>Azure クラウドで負荷分散サービスを使用する

## <a name="introduction"></a>はじめに

Microsoft Azure では、ネットワーク トラフィックの分散や負荷分散の方法を管理するための複数のサービスを利用できます。 これらのサービスは個別に使用できるほか、特定のニーズに基づいてそれらのメソッドを組み合わせて最適なソリューションをビルドすることができます。

このチュートリアルでは、最初に顧客のユース ケースを定義し、Azure の負荷分散ポートフォリオである Traffic Manager、Application Gateway、ロード バランサーを使用して堅牢で高いパフォーマンスを得るための方法を確認します。 その後、地理的に冗長性を持たせ、トラフィックを VM に分散し、さまざまな種類のリクエストを管理できるデプロイメントを作成する手順を詳しく説明します。

概念レベルでは、これらの各サービスは、負荷分散の階層において個別の役割を果たします。

1. **Traffic Manager** には、グローバルな DNS グローバル負荷分散機能が備わっています。  これは受信 DNS 要求を調べ、ユーザーが選択したルーティング ポリシーに従って、正常なエンドポイントを使用して応答できます。 ルーティング方法には、遅延の観点から最も近いエンドポイントに要求元を送信するパフォーマンス ルーティング、他のエンドポイントをバックアップとして 1 つのエンドポイントにすべてのトラフィックを誘導する優先順位ルーティング、各エンドポイントに割り当てられている重み付けに基づいてトラフィックを分散させる重み付けラウンド ロビン ルーティングのオプションがあります。 クライアントはそのエンドポイントに直接接続します。 Azure Traffic Manager は、エンドポイントに異常が発生したことを検出すると、クライアントを別の正常なインスタンスに誘導します。 サービスの詳細については、[Azure Traffic Manager のドキュメント](traffic-manager-overview.md)をご覧ください。
2. **Application Gateway** は、アプリケーション配信コントローラー (ADC) をサービスとして提供することで、さまざまなレイヤー 7 負荷分散機能をアプリケーションで利用できるようにします。 これにより、CPU を集中的に使用する SSL ターミネーションをお客様が Application Gateway にオフロードし、Web ファームの生産性を最適化できます。 着信トラフィックのラウンド ロビン分散、Cookie ベースのセッション アフィニティ、URL パス ベースのルーティング、単一の Application Gateway の背後で複数の Web サイトをホストする機能など、その他のレイヤー 7 ルーティング機能も用意されています。 Application Gateway は、インターネット接続ゲートウェイ、または内部的にのみ使用されるゲートウェイのいずれかとして構成できるほか、この両方を組み合わせて使用することも可能です。 Application Gateway は Azure によって完全に管理され、非常にスケーラブルで、高い可用性を備えています。 管理しやすいように診断機能とログ機能が豊富に用意されているほか、
3. **ロード バランサー**は Azure SDN スタックに含まれている機能です。 ロード バランサーには、すべての UDP と TCP プロトコル向けの高パフォーマンス、低待機時間のレイヤー 4 負荷分散サービスが備わっています。  受信接続と送信接続を管理します。  負荷分散されるパブリックおよび内部のエンドポイントを構成したり、TCP を使用して受信接続をバックエンド プールの送信先にマッピングする規制や、サービスの可用性を管理するための HTTP の正常性プローブ オプションを定義したりできます。

## <a name="scenario"></a>シナリオ

このシナリオの例では、イメージと動的に表示される Web ページの 2 種類のコンテンツを提供する簡単な Web サイトを使用します。 この Web サイトには地理的な冗長性が必要であり、最も近い場所にいる (待機時間が最短の) ユーザーにサービスを提供する必要があります。 アプリケーション開発者が、/images/* のパターンに一致する URL を決定します。これらの URL は、残りの Web ファームではなく、VM の専用プールから提供されます。

さらに、動的コンテンツを提供する既定の VM プールは、高可用性クラスターでホストされているバックエンドのデータベースと通信する必要があります。 すべてのデプロイメントは Azure Resource Manager を通じてプロビジョニングされます。

Traffic Manager、Application Gateway、ロード バランサーを使用すると、次のような Web サイトにおけるデザインの目標を達成できます。

1. **複数の地理的な冗長性**: Traffic Manager を使用すると、1 つのリージョンがダウンしたときに、次の最適なリージョンにトラフィックがシームレスにルーティングされます。アプリケーション所有者による操作は必要ありません。
2. **待機時間の短縮**: 顧客は Azure Traffic Manager によって最寄りのリージョンに自動的に誘導されるため、Web ページのコンテンツを要求したときの待機時間が短縮されます。
3. **独立したスケーラビリティ**: Web アプリケーションのワークロードをコンテンツの種類によって分けておくことで、アプリケーション所有者は、要求ワークロードを互いに影響を与えることなく拡張できます。 Application Gateway では、指定した規制とアプリケーションの正常性に基づいて、適切なプールにトラフィックをルーティングできます。
4. **内部負荷分散**: ロード バランサーを高可用性クラスターの前に配置することで、データベースのアクティブで正常なエンドポイントのみがアプリケーションに公開されます。  さらに、データベース管理者は、フロントエンド アプリケーションから独立してアクティブとパッシブのレプリカをクラスター全体に分散させることによって、ワークロードを最適化できます。  ロード バランサーは接続を高可用性クラスターに提供し、正常なデータベースのみが接続要求を受け取るようにします。

次の図は、このシナリオのアーキテクチャを示します。

![シナリオ図のイメージ](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> この例は、Azure が提供する多数の負荷分散サービスの 1 つの例に過ぎません。 Azure Traffic Manager、Application Gateway、ロード バランサーは、負荷分散のニーズに合わせて組み合わせて使用できます。 たとえば、SSL オフロードやレイヤー 7 の処理が必要ない場合は、Application Gateway の代わりにロード バランサーを使用できます。

## <a name="setting-up-the-load-balancing-stack"></a>負荷分散スタックの設定

### <a name="step-1-create-a-traffic-manager-profile"></a>手順 1: Traffic Manager プロファイルを作成する

1. Azure Portal に移動し、**[新規]** をクリックして、"Traffic Manager プロファイル" の Marketplace を検索します。
2. [Traffic Manager プロファイルの作成] ブレードで、Traffic Manager プロファイルの作成に必要な基本的な情報を入力します。

   * **[名前]** - Traffic Manager プロファイルに DNS プレフィックス名を指定します。
   * **[ルーティング方法]** - トラフィックのルーティング方法のポリシーを選択します。トラフィック ルーティング方法の詳細については、「[Traffic Manager でのトラフィック ルーティング方法について](traffic-manager-routing-methods.md)」をご覧ください。
   * **[サブスクリプション]** - プロファイルが含まるサブスクリプション
   * **[リソース グループ]** - Traffic Manager プロファイルを含むリソース グループ。新規または既存のリソース グループを使用できます。
   * **[リソース グループの場所]** - Traffic Manager サービスはグローバルなサービスであり 1 つの場所に向けられたものではありませんが、Traffic Manager プロファイルに関連付けられているメタデータが存在するグループにはリージョンを指定する必要があります。 この場所は、プロファイルの実行時の可用性については影響ありません。

3. **[作成]** をクリックすると、Traffic Manager プロファイルが生成されます

    ![Traffic Manager ブレードの作成](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>手順 2: Application Gateway を作成する

1. Azure Portal に移動し、左側のメニューから **[新規]** > **[ネットワーク]** > **[Application Gateway]** の順にクリックします。
2. 次に、アプリケーション ゲートウェイに関する基本情報を入力します。 完了したら、[OK] をクリックします。 基本設定に必要な情報は次のとおりです。

   * **[名前]** - アプリケーション ゲートウェイの名前。
   * **[SKU サイズ]** - アプリケーション ゲートウェイのサイズ。S、M、L のオプションを使用できます。
   * **[インスタンス数]** - インスタンスの数。この値は、2 ～ 10 の数値で指定する必要があります。
   * **[リソース グループ]** - アプリケーション ゲートウェイを保持するリソース グループ。既存のリソース グループを指定することも、新しいグループを指定することもできます。
   * **[場所]** - アプリケーション ゲートウェイのリージョン。リソース グループと同じ場所にします。 この場所は、仮想ネットワークとパブリック IP はゲートウェイと同じ場所に存在する必要があるため、重要です。

3. 次に、Application Gateway の仮想ネットワーク、サブネット、フロントエンド IP、リスナー構成を定義します。 このシナリオでは、フロント エンド IP アドレスは **[パブリック]** に設定されているため、後で Traffic Manager プロファイルにエンドポイントとして追加できます。
4. 次に、Application Gateway のリスナー構成を設定します。 http を使用する場合は、何も構成する必要がないため、**[OK]** をクリックします。 https を使用する場合は、追加の構成が必要です。「[Application Gateway を作成する](../application-gateway/application-gateway-create-gateway-portal.md)」を参照し、手順 9 から開始してください。

#### <a name="configure-url-routing-for-application-gateways"></a>Application Gateway の URL ルーティングを構成する

パス ベースの規制で構成される Application Gateway は、バックエンド プールを選択する際に、ラウンド ロビン分散だけでなく、要求 URL のパスのパターンを使用します。 このシナリオでは、"/images/\*" 含む URL をイメージ サーバー プールに誘導するための、パス ベースの規制を追加します。 Application Gateway のパス ベースの URL ルーティングの詳細については、「[Create a Path-based rule for an Application Gateway (Application Gateway のパス ベースの規制を作成する)](../application-gateway/application-gateway-create-url-route-portal.md)」をご覧ください。

![Web 層の図](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. リソース グループから、前の処理手順で作成した Application Gateway のインスタンスに移動します。
2. [設定] で **[バックエンド プール]** を選択し、[追加] を選択して Web 層のバックエンド プールに関連付ける VM を追加します。
3. "バックエンド プールの追加" ブレードに、バックエンド プールの名前と、プールに存在するすべてのマシンの IP アドレスを入力します。 このシナリオでは、仮想マシンの 2 つのバックエンド サーバー プールを接続します。

    ![Application Gateway バックエンド プールの追加](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. 次に、Application Gateway の [設定] で、**[規制]** を選択し、**[パス ベース]** ボタンをクリックして新しい規制を追加します。

    ![Application Gateway パスの規制の追加](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. [パス ベースの規制を追加する] ブレードで、次の情報を入力して規制を構成します。

   * 基本設定: 
     + **[名前]** - ポータルでアクセス可能な規制の表示名
     + **[リスナー]** - 規制で使用するリスナー
     + **[既定のバックエンド プール]** - 既定の規則に使用するバックエンド プール
     + **[既定の HTTP 設定]** - 既定の規則で使用する HTTP 設定
   * パス ベースの規則:
     + **[名前]** - パス ベースの規則の表示名
     + **[パス]** - トラフィックの転送に使用するパスの規則
     + **[バックエンド プール]** - この規則で使用するバックエンド プール
     + **[HTTP 設定]** - この規則で使用する HTTP 設定

     > [!IMPORTANT]
     > パス: 有効なパスはスラッシュ "/" で始める必要があります。 ワイルドカード "\*" は末尾にのみ使用できます。 有効な例: /xyz、/xyz\*、/xyz/\*

     ![Application Gateway パス規則ブレードの追加](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>手順 3: Traffic Manager のエンドポイントに Application Gateway を追加する

このシナリオでは、Traffic Manager を、別のリージョンにある Application Gateway (前述の手順で構成したもの) のインスタンスに接続します。 Application Gateway の構成は終わっているため、次の手順では Traffic Manager プロファイルに Application Gateway を接続します。

1. Traffic Manager プロファイルのインスタンスに移動します (リソース グループ内を探すか、[すべてのリソース] から Traffic Manager プロファイルの名前を検索します)。
2. このブレードから、**[エンドポイント]**、**[追加]** の順に選択して、新しいエンドポイントを追加します。

    ![Traffic Manager エンドポイントの追加](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. [エンドポイントの追加] ブレードに、新しいエンドポイントを作成するために必要な情報を入力します。

   * **[タイプ]** - 負荷分散を行うエンドポイントのタイプ。このシナリオでは、上述の手順で構成した Application Gateway インスタンスに接続するため、Azure エンドポイントになります。
   * **[名前]** - エンドポイントの名前
   * **[ターゲット リソースの種類]** - パブリック IP アドレスを選択し、下の [ターゲット リソース] には、上述の手順で構成した Application Gateway のパブリック IP を選択します。

     ![Traffic Manager エンドポイントの追加](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. ここからは、Traffic Manager プロファイルの DNS (この例では、TrafficManagerScenario.trafficmanager.net) でアクセスすることによって、設定をテストできます。 要求を再送信したり、別のリージョンに作成した VM/Web サーバーをアップ/ダウンさせたり、Traffic Manager プロファイルの設定を変更して、設定をテストできます。

### <a name="step-4-create-the-load-balancer"></a>手順 4: ロード バランサーを作成する

このシナリオでは、ロード バランサーは Web 層から高可用性クラスター内のデータベースに接続を分散します。

高可用性データベース クラスターが SQL AlwaysOn を使用している場合は、1 つまたは複数の [Always On 可用性グループ リスナー](../virtual-machines/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を構成する方法をご覧ください。

内部ロード バランサーの構成方法について詳しくは、「[Create an Internal load balancer in the Azure portal (Create an Internal load balancer in the Azure Portal で内部ロード バランサーを作成する)](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)」をご覧ください。

1. Azure Portal に移動し、左側のメニューから **[新規]** > **[ネットワーク]** > **[ロード バランサー]** の順にクリックします。
2. [ロード バランサーの作成] ブレードで、ロード バランサーの名前を入力します。
3. **[タイプ]** を [内部] に設定し、適切な仮想ネットワークと、ロード バランサーが入るサブネットを選択します。
4. **[IP アドレスの割り当て ]** で、[動的] または [静的] を選択します。
5. 次に、**[リソース グループ]** で、ロード バランサーのリソース グループを選択します。
6. **[場所]** で、ロード バランサーの該当するリージョンを選択します
7. 最後に、**[作成]** をクリックして、ロード バランサーを生成します。

#### <a name="connect-backend-database-tier-to-load-balancer"></a>バックエンド データベース層をロード バランサーに接続する

1. リソース グループから、上述の手順で作成したロード バランサーを見つけます。
2. [設定] で、**[バックエンド プール]****[追加]** の順にクリックし、新しいバックエンド プールを追加します。
3. [バックエンド プールの追加] ブレードで、バックエンド プールの名前を入力します。
4. ここからは、バックエンド プールに個々のマシンを追加したり、バックエンド プールに可用性セットを追加したりできるようになります。

   ![ロード バランサー プールの追加](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

#### <a name="configure-a-probe"></a>プローブを構成する

1. ロード バランサーの [設定] で **[プローブ]**、**[追加]** の順に選択し、新しいプローブを追加します。
2. [プローブの追加] ブレードで、プローブの **[名前]** を入力します。
3. プローブの **[プロトコル]** を選択します。 データベースの場合は、HTTP プローブではなく TCP プローブを使用する場合が多くなります。   ロード バランサー プローブの詳細については、「[Understand load balancer probes (ロード バランサーのプローブの概要)](../load-balancer/load-balancer-custom-probe-overview.md)」をご覧ください。
4. 次に、プローブにアクセスするときに使用するデータベースの **[ポート]** を入力します。
5. **[間隔]** で、アプリケーションをプローブする頻度を指定します。
6. **[異常しきい値]** に、バックエンドの VM が異常とみなされるまでの、プローブの連続失敗回数を指定します。
7. **[OK]** をクリックして、プローブを作成します。

   ![ロード バランサーのプローブ](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

#### <a name="configure-load-balancing-rules"></a>負荷分散規則の構成

1. ロード バランサーの [設定] で **[負荷分散規則]****[追加]** の順にクリックして、規則を作成します。
2. [負荷分散規則の追加] ブレードで、負荷分散規則の **[名前]** を入力します。
3. ロード バランサーの **[フロントエンド IP アドレス]****[プロトコル]****[ポート]**を選択します。
4. **[バックエンド ポート]** で、バックエンド プールで使用するポートを指定します。
5. **[バックエンド プール]**を選択し、上述の手順で作成し、規則を適用する **[プローブ]** を選択します。
6. **[セッション永続化]**で、セッションを永続化する方法を選択します。
7. **[アイドル タイムアウト]** で、アイドル タイムアウトまでの分数を指定します。
8. **[Floating IP]** の [無効] または [有効] を選択します。
9. **[OK]** をクリックして規則を作成します。

### <a name="step-5-connect-web-tier-vms-to-load-balancer"></a>手順 5: Web 層の VM をロード バランサーに接続する

これで、Web 層の VM で実行されるアプリケーションの IP アドレスとロード バランサーのフロントエンド ポートが構成でき、データベースに接続できるようになりました。 この構成は、これらの VM で実行されるアプリケーション専用のものです。 接続先 IP アドレスとポートの構成に関する詳細は、アプリケーションのマニュアルをご覧ください。 フロントエンドの IP アドレスを探すには、Azure Portal のロード バランサー設定ブレードのフロントエンド IP プールに移動します。

![ロード バランサー フロントエンド IP プール](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>次のステップ

* [Traffic Manager の概要](traffic-manager-overview.md)
* [Application Gateway の概要](../application-gateway/application-gateway-introduction.md)
* [Azure Load Balancer の概要](../load-balancer/load-balancer-overview.md)



<!--HONumber=Nov16_HO3-->


