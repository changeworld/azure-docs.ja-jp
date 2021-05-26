---
title: Azure Arc 対応 Kubernetes クラスターに Event Grid をインストールする
description: この記事では、Azure Arc 対応 Kubernetes クラスターに Event Grid をインストールする手順について説明します。
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: 43879b5124c49ba3d0c4494e2d3a1eba95e617ff
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386563"
---
# <a name="install-event-grid-extension-on-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc 対応 Kubernetes クラスターに Event Grid 拡張機能をインストールする
この記事では、[Azure Arc 対応 Kubernetes](../../azure-arc/kubernetes/overview.md) クラスターに Event Grid をインストールする手順について説明します。

簡潔にするため、この記事では "Kubernetes 拡張機能の Event Grid" のことを "Kubernetes の Event Grid" または単に "Event Grid" と表記しています。

[!INCLUDE [event-grid-preview-feature-note.md](../../../includes/event-grid-preview-feature-note.md)]


## <a name="supported-kubernetes-distributions"></a>サポートされている Kubernetes ディストリビューション
Event Grid のデプロイと実行がサポートされている Kubernetes ディストリビューションは以下の通りです。

1. Azure AKS で[サポートされている Kubernetes ディストリビューション](../../aks/supported-kubernetes-versions.md)。
1. RedHat [OpenShift Container Platform](https://www.openshift.com/products/container-platform)。

今後、[ユーザーからのフィードバック](https://feedback.azure.com/forums/909934-azure-event-grid)や、[Azure Arc 対応 Kubernetes のサポート状況](../../azure-arc/kubernetes/validation-program.md)に応じて、さらに多くのディストリビューションがオンボードされる予定です。

## <a name="event-grid-extension"></a>Event Grid 拡張機能
Kubernetes クラスターに Event Grid サービス インスタンスをインストールするには、Azure Arc クラスター拡張機能を作成します。これにより、**Event Grid ブローカー** と **Event Grid 演算子** の両方がデプロイされます。 ブローカーと演算子の関数の詳細については、[Kubernetes コンポーネントの Event Grid](concepts.md#event-grid-on-kubernetes-components) を参照してください。 [Azure Arc クラスター拡張](../../azure-arc/kubernetes/conceptual-extensions.md)機能を使用すると、Azure Arc 対応の Kubernetes クラスターにデプロイされた Event Grid で、Azure Resource Manager (ARM) のコントロール プレーン操作を用いたライフサイクル管理を行うことができるようになります。

> [!NOTE]
> このサービスのプレビュー版では、現在 Event Grid 拡張機能がクラスタースコープでの拡張機能として定義されているため、Kubernetes クラスターでサポートされる Event Grid 拡張機能のインスタンス数は 1 つのみです。 クラスターに複数のインスタンスをデプロイできる Event Grid の名前空間スコープでのデプロイはまだサポートされていません。  拡張機能のスコープの詳細については、[拡張機能インスタンスの作成](../../azure-arc/kubernetes/extensions.md#create-extensions-instance)に関するページを参照して、「``scope``」とを検索してください。

## <a name="prerequisites"></a>前提条件
Event Grid のインストールに進む前に、次の前提条件が満たされていることを確認してください。 

1. [サポートされている Kubernetes ディストリビューション](#supported-kubernetes-distributions)のいずれかで実行されているクラスター。
1. [Azure サブスクリプション](https://azure.microsoft.com/en-us/free/)。
1. Event Grid ブローカーとの HTTPS 接続を確立するために使用する [PKI 証明書](#pki-certificate-requirements)。
1. [Azure Arc にクラスターを接続する](../../azure-arc/kubernetes/quickstart-connect-cluster.md)。

## <a name="getting-support"></a>サポートの利用
問題が発生した場合は、一般的な状況についての[トラブルシューティング](#troubleshooting)に関するセクションを参照してください。 それでも問題が発生する場合は、[Azure サポート リクエストを作成](get-support.md#how-to-create-a-support-request)してください。

## <a name="pki-certificate-requirements"></a>PKI 証明書の要件
Event Grid ブローカー (サーバー) では、2 種類のクライアントに対応します。 サーバー認証は、証明書を使用して実行されます。 クライアント認証は、クライアントの種類に応じて証明書または SAS キーのいずれかを使用して行われます。

- Event Grid ブローカーに対するコントロール プレーン要求を行う Event Grid 演算子は、証明書を使用して認証されます。
- Event Grid のトピックにイベントを発行する Event Grid のパブリッシャーは、トピックの SAS キーで認証されます。

Event Grid ブローカーと Event Grid 演算子とのセキュリティで保護された HTTPS 通信を確立するために、Event Grid 拡張機能のインストール時に PKI 証明書を使用します。 これらの PKI 証明書の一般的な要件は次のとおりです。

1. 証明書とキーは、[x.509](https://en.wikipedia.org/wiki/X.509) 証明書および [Privacy-Enhanced Mail](https://en.wikipedia.org/wiki/Privacy-Enhanced_Mail) PEM でエンコードされている必要があります。
1. インストール時に Event Grid broker (サーバー) 証明書を構成するには、次のものを指定する必要があります。
    1. CA 証明書
    1. 公開証明書
    1. 秘密キー
1. Event Grid 演算子 (クライアント) 証明書を構成するには、次の情報を指定する必要があります。
    1. CA 証明書
    1. 公開証明書
    1. 秘密キー

    発行元のクライアントでは、イベントをトピックに発行するときに、Event Grid ブローカー CA 証明書を使用してサーバーを検証できます。

    > [!IMPORTANT]
    > クライアントに関連付けられているドメインは、異なる認証局から発行された複数のパブリック証明書を持つことができますが、Kubernetes 上の Event Grid では、Event Grid のインストール時にクライアント用の単一の CA 証明書をアップロードすることしかできません。 そのため、証明書チェーンの検証を成功させ、TLS セッションを正常に確立するためには、Event Grid 演算子の証明書を、同じ CA によって発行 (署名) する必要があります。
1. サーバーとクライアントの証明書の共通名 (CN) を構成する際には、認証機関の証明書に記載されている CN と同じものを使用しないでください。

    > [!IMPORTANT] 
    > 概念実証の初期段階では、自己署名証明書を使用することもできますが、一般的には、認証機関 (CA) によって署名された適切な PKI 証明書を入手して使用する必要があります。

## <a name="install-event-grid-on-kubernetes-extension"></a>Kubernetes 拡張機能に Event Grid をインストールする

1. Azure portal で、「**Azure Arc**」と検索します (上部のフィールド)
1. **[インフラストラクチャ]** セクションの左側のメニューで **[Kubernetes クラスター]** を選択します
1. クラスターの一覧で、Event Grid をインストールするクラスターを探して選択します。 クラスターの **[概要]** ページが表示されます。
1. 左側のメニューにある **[設定]** グループの **[拡張機能]** を選択します。
1. **[+ 追加]** を選択します。 使用可能な Azure Arc Kubernetes 拡張機能を示すページが表示されます。
1. **[イベント グリッド]** を選びます。
1. Azure Arc を使用した Kubernetes 上の Event Grid に関するページで **[作成]** を選択します。
1. **[Event Grid のインストール]** ページの **[基本]** タブが表示されます。 **[プロジェクトの詳細]** セクションには、読み取り専用のサブスクリプションとリソース グループの値が表示されます。これは、Azure Arc 拡張機能が、それらがインストールされている接続クラスターと同じ Azure サブスクリプションとリソース グループの下にデプロイされるためです。
1. **[Event Grid extension name]\(Event Grid の拡張機能名\)** フィールドで名前を指定します。 この名前は、同じ Azure Arc 接続クラスターにデプロイされた他の Azure Arc 拡張機能の中で一意である必要があります。
1. **[リリース名前空間]** には、Event Grid コンポーネントがデプロイされる Kubernetes 名前空間の名前を指定します。 既定値は **eventgrid-system** です。 指定した名前空間が存在しない場合は、自動で作成されます。
1. **[Event Grid ブローカー]** の詳細セクションに、サービスの種類が表示されます。 イベントの送信先となるトピックのエンドポイントを公開するコンポーネントである Event Grid broker は、Kubernetes のサービスの種類である **ClusterIP** として公開されます。 そのため、すべてのトピックに割り当てられた IP は、クラスター用に構成されたプライベート IP 空間を使用します。
1. ブローカーに使用する、Kubernetes ディストリビューションでサポートされている **ストレージ クラス名** を指定します。 たとえば、AKS を使用している場合は、Azure Standard Storage を使用する `azurefile` を使用できます。 AKS でサポートされている定義済みのストレージ クラスの詳細については、[AKS のストレージ クラス](../../aks/concepts-storage.md#storage-classes)に関する記事を参照してください。 他の Kubernetes ディストリビューションを使用している場合は、サポートされている定義済みのストレージ クラスや、独自のストレージ クラスを指定する方法について、Kubernetes ディストリビューションのドキュメントを参照してください。
1. **ストレージ サイズ**。 既定値は 1 GiB です。 ストレージのサイズを決定するときは、インジェスト速度を考慮してください。 Event Grid ブローカー上のすべてのトピックに対するイベントのサイズと発行速度 (1 秒あたりのイベント数) を掛け合わせたものを MiB/秒で計測したインジェスト速度は、ストレージを割り当てる際の重要な要素となります。 イベントは一時的なものであるため、配信されると、それらのイベントに対するストレージの消費は発生しません。 インジェスト速度はストレージ領域を消費する主な要因ではありますが、これがすべてではありません。 トピックとイベント サブスクリプションの構成を保持するメタデータもストレージ領域を消費しますが、通常は、Event Grid によって取り込まれたり配信されたりするイベントよりも必要なストレージ領域は少なく済みます。
1. **メモリの制限**。 既定値は 1 GiB です。 
1. **メモリ要求**。 既定値は 200 MiB です。 このフィールドは編集できません。

    :::image type="content" source="./media/install-k8s-extension/basics-page.png" alt-text="Event Grid 拡張機能のインストール - 基本ページ":::
1. ページの下部にある **[次へ: 構成]** ボタンを選択します。
1. **HTTP (セキュリティで保護されていない) 通信を有効にする**。 クライアントと Event Grid ブローカーとの通信に、セキュリティで保護されていないチャネルを使用する場合は、このチェックボックスをオンにします。

    > [!IMPORTANT]
    > このオプションを有効にすると、Event Grid ブローカーとのすべての通信で、トランスポートとして HTTP が使用されます。 そのため、発行元のクライアントおよび Event Grid 演算子は、Event Grid broker と安全に通信できません。 このオプションは、開発の初期段階でのみ使用してください。
1. HTTP 通信を有効にしなかった場合は、入手した PKI 証明書ファイルをそれぞれ選択し、[PKI 証明書の要件](#pki-certificate-requirements)を満たします。

    :::image type="content" source="./media/install-k8s-extension/configuration-page.png" alt-text="Event Grid 拡張機能のインストール - 構成ページ":::
1. ページの下部にある **[次へ: 監視]** ボタンを選択します。
1. このオプションをオンにして、**メトリックを有効** にします。 Kubernetes 上の Event Grid では、トピックおよびイベント サブスクリプションのさまざまなメトリックが [Prometheus の出力形式](https://prometheus.io/docs/instrumenting/exposition_formats/)で公開されます。

    :::image type="content" source="./media/install-k8s-extension/monitoring-page.png" alt-text="Event Grid 拡張機能のインストール - 監視ページ":::    
1. **[次へ: タグ]** を選択して、 **[タグ]** ページに移動します。 必要に応じて、[タグ](/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)を定義します。

    :::image type="content" source="./media/install-k8s-extension/tags-page.png" alt-text="Event Grid 拡張機能のインストール - タグ ページ":::
1. ページ下部にある **[確認と作成]** を選択します。
1. **[確認および作成]** タブで、 **[作成]** を選択します。
    
    :::image type="content" source="./media/install-k8s-extension/review-create-page.png" alt-text="Event Grid 拡張機能のインストール - 確認と作成ページ":::   
    
    > [!IMPORTANT]
    > Event Grid のインストールは非同期操作であり、Azure portal でデプロイが完了したことを知らせる通知が表示されるまでの時間よりも Kubernetes クラスターで実行される時間が長くなる可能性があります。 "デプロイが完了しました" という通知の表示後、少なくとも 5 分待ってから、カスタムの場所を作成する (次の手順) ようにしてください。 Kubernetes クラスターにアクセスできる場合は、BASH セッションで次のコマンドを実行することで、Event Grid ブローカーと Event Grid 演算子ポッドが実行状態かどうか (インストールが完了したことを示します) を検証できます。

    ```bash
    kubectl get pods -n \<release-namespace-name\>
    ```
    > [!IMPORTANT]
    > カスタムの場所は、新しい Event Grid トピックをデプロイする前に作成する必要があります。 カスタムの場所を作成するには、"デプロイが完了しました" という通知が表示された 5 分後に、下部にある **[コンテキスト]** ページを選択します。 あるいは、[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ExtendedLocation%2FCustomLocations) を使用してカスタムの場所を作成します。
1. デプロイが成功すると、 **[拡張機能]** ページに、Event Grid 拡張機能に指定した名前のエントリが表示されます。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="azure-arc-connect-cluster-issues"></a>Azure Arc 接続クラスターに関する問題

**問題**: **Azure Arc** に移動し、左側のメニューで **[Kubernetes クラスター]** をクリックしても、表示されるページに Event Grid をインストールしたい Kubernetes クラスターが表示されません。

**解決方法**: Kubernetes クラスターが Azure に登録されていません。 「[既存の Kubernetes クラスターを Azure Arc に接続する](../../azure-arc/kubernetes/quickstart-connect-cluster.md)」という記事の手順に従います。このステップで問題が発生した場合は、Azure Arc 対応 Kubernetes チーム宛てに[サポート リクエスト](#getting-support)を送信してください。

### <a name="event-grid-extension-issues"></a>Event Grid 拡張機能に関する問題

**問題**: "Event Grid 拡張機能" をインストールしようとすると、次のメッセージが表示されます。"**無効な操作** - この接続済みの Kubernetes クラスターには、既に Event Grid のインスタンスがインストールされています。 Event Grid の拡張機能はクラスター レベルでスコープが設定されているため、1 つのインスタンスのみをクラスターにインストールできます。"
    
**説明**: Event Grid が既にインストールされています。 Event Grid のプレビュー バージョンでは、クラスターにデプロイできる Event Grid 拡張機能インスタンスの数は 1 つだけです。


## <a name="next-steps"></a>次の手順
[Kubernetes 上の Azure Event Grid でクラウド イベントを Webhook にルーティングする](create-topic-subscription.md)方法に関するクイックスタートを参照してください。

