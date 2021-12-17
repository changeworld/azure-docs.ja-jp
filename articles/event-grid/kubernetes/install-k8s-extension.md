---
title: Azure Arc 対応 Kubernetes クラスターに Event Grid をインストールする
description: この記事では、Azure Arc 対応 Kubernetes クラスターに Event Grid をインストールする手順について説明します。
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/26/2021
ms.topic: how-to
ms.openlocfilehash: 149f46d96e7e723c89eb473aa5faea2301bc9d5f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179244"
---
# <a name="install-event-grid-extension-on-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc 対応 Kubernetes クラスターに Event Grid 拡張機能をインストールする
この記事では、[Azure Arc 対応 Kubernetes](../../azure-arc/kubernetes/overview.md) クラスターに Event Grid をインストールする手順について説明します。

簡潔にするため、この記事では "Kubernetes 拡張機能の Event Grid" のことを "Kubernetes の Event Grid" または単に "Event Grid" と表記しています。

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]


## <a name="supported-kubernetes-distributions"></a>サポートされている Kubernetes ディストリビューション
Event Grid のデプロイと実行がサポートされている Kubernetes ディストリビューションは以下の通りです。

1. Azure AKS で[サポートされている Kubernetes ディストリビューション](../../aks/supported-kubernetes-versions.md)。
1. RedHat [OpenShift Container Platform](https://www.openshift.com/products/container-platform)。


## <a name="event-grid-extension"></a>Event Grid 拡張機能
Kubernetes クラスターに Event Grid サービス インスタンスをインストールするには、Azure Arc クラスター拡張機能を作成します。これにより、**Event Grid ブローカー** と **Event Grid 演算子** の両方がデプロイされます。 ブローカーと演算子の関数の詳細については、[Kubernetes コンポーネントの Event Grid](concepts.md#event-grid-on-kubernetes-components) を参照してください。 [Azure Arc クラスター拡張](../../azure-arc/kubernetes/conceptual-extensions.md)機能を使用すると、Azure Arc 対応の Kubernetes クラスターにデプロイされた Event Grid で、Azure Resource Manager (ARM) のコントロール プレーン操作を用いたライフサイクル管理を行うことができるようになります。

> [!NOTE]
> このサービスのプレビュー版では、現在 Event Grid 拡張機能がクラスタースコープでの拡張機能として定義されているため、Kubernetes クラスターでサポートされる Event Grid 拡張機能のインスタンス数は 1 つのみです。 クラスターに複数のインスタンスをデプロイできる Event Grid の名前空間スコープでのデプロイはまだサポートされていません。  拡張機能のスコープの詳細については、[拡張機能インスタンスの作成](../../azure-arc/kubernetes/extensions.md#create-extensions-instance)に関するページを参照して、「``scope``」とを検索してください。

## <a name="prerequisites"></a>前提条件
Event Grid のインストールに進む前に、次の前提条件が満たされていることを確認してください。 

1. [サポートされている Kubernetes ディストリビューション](#supported-kubernetes-distributions)のいずれかで実行されているクラスター。
1. [Azure サブスクリプション](https://azure.microsoft.com/free/)。
1. Event Grid ブローカーとの HTTPS 接続を確立するために使用する [PKI 証明書](#pki-certificate-requirements)。
1. [Azure Arc にクラスターを接続する](../../azure-arc/kubernetes/quickstart-connect-cluster.md)。

## <a name="getting-support"></a>サポートの利用
問題が発生した場合は、一般的な状況についての[トラブルシューティング](#troubleshooting)に関するセクションを参照してください。 それでも問題が発生する場合は、[Azure サポート リクエストを作成](get-support.md#how-to-create-a-support-request)してください。

## <a name="pki-certificate-requirements"></a>PKI 証明書の要件
Event Grid ブローカー (サーバー) では、2 種類のクライアントに対応します。 サーバー認証は、証明書を使用して実行されます。 クライアント認証は、クライアントの種類に応じて証明書または SAS キーのいずれかを使用して行われます。

- Event Grid ブローカーに対するコントロール プレーン要求を行う Event Grid 演算子は、証明書を使用して認証されます。
- Event Grid のトピックにイベントを発行する Event Grid のパブリッシャーは、トピックの SAS キーで認証されます。

Event Grid ブローカーおよび Event Grid 演算子との間で、セキュリティで保護された HTTPS 通信を確立するには、Event Grid 拡張機能のインストール時に PKI 証明書を使用します。 これらの PKI 証明書の一般的な要件は次のとおりです。

1. 証明書とキーは、[x.509](https://en.wikipedia.org/wiki/X.509) 証明書および [Privacy-Enhanced Mail](https://en.wikipedia.org/wiki/Privacy-Enhanced_Mail) PEM でエンコードされている必要があります。
1. インストール時に Event Grid ブローカー (サーバー) 証明書を構成するには、次の情報を指定する必要があります。
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

## <a name="install-using-azure-portal"></a>Azure portal を使用してインストールする

1. Azure portal で、「**Azure Arc**」と検索します (上部のフィールド)
1. **[インフラストラクチャ]** セクションの左側のメニューで **[Kubernetes クラスター]** を選択します
1. クラスターの一覧で、Event Grid をインストールするクラスターを探して選択します。 クラスターの **[概要]** ページが表示されます。

    :::image type="content" source="./media/install-k8s-extension/select-kubernetes-cluster.png" alt-text="[Kubernetes クラスター] を選択する":::
1. 左側のメニューにある **[設定]** グループの **[拡張機能]** を選択します。
1. **[+ 追加]** を選択します。 使用可能な Azure Arc Kubernetes 拡張機能を示すページが表示されます。

    :::image type="content" source="./media/install-k8s-extension/cluster-extensions-add.png" alt-text="クラスター拡張機能 - [追加] ボタン":::    
1. **[新しいリソース]** ページで、 **[Kubernetes 拡張機能の Event Grid]** を選択します。

    :::image type="content" source="./media/install-k8s-extension/select-event-grid-extension.png" alt-text="[Kubernetes 拡張機能の Event Grid] を選択する":::        
1. **[Kubernetes 拡張機能の Event Grid]** ページで、 **[作成]** を選択します。

    :::image type="content" source="./media/install-k8s-extension/select-create-extension.png" alt-text="Kubernetes 拡張機能の作成を選択する":::            
1. **[Event Grid のインストール]** ページの **[基本]** タブで、次の手順に従います。 
    1. **[プロジェクトの詳細]** セクションには、読み取り専用のサブスクリプションとリソース グループの値が表示されます。これは、Azure Arc 拡張機能が、それらがインストールされている接続クラスターと同じ Azure サブスクリプションとリソース グループの下にデプロイされるためです。
    1. **[Event Grid extension name]\(Event Grid の拡張機能名\)** フィールドで名前を指定します。 この名前は、同じ Azure Arc 接続クラスターにデプロイされた他の Azure Arc 拡張機能の中で一意である必要があります。
    1. **[リリース名前空間]** には、Event Grid コンポーネントがデプロイされる Kubernetes 名前空間の名前を指定します。 たとえば、クラスターにデプロイされるすべての Azure Arc 対応サービスに対して、1 つの名前空間を使用することができます。 既定値は **eventgrid-system** です。 指定した名前空間が存在しない場合は、自動的に作成されます。
    1. **[Event Grid ブローカー]** の詳細セクションに、サービスの種類が表示されます。 イベントの送信先となるトピックのエンドポイントを公開するコンポーネントである Event Grid broker は、Kubernetes のサービスの種類である **ClusterIP** として公開されます。 そのため、すべてのトピックに割り当てられた IP は、クラスター用に構成されたプライベート IP 空間を使用します。
    1. ブローカーに使用する、Kubernetes ディストリビューションでサポートされている **ストレージ クラス名** を指定します。 たとえば、AKS を使用している場合は、Azure Standard Storage を使用する `azurefile` を使用できます。 AKS でサポートされている定義済みのストレージ クラスの詳細については、[AKS のストレージ クラス](../../aks/concepts-storage.md#storage-classes)に関する記事を参照してください。 他の Kubernetes ディストリビューションを使用している場合は、サポートされている定義済みのストレージ クラスや、独自のストレージ クラスを指定する方法について、Kubernetes ディストリビューションのドキュメントを参照してください。
    1. **ストレージ サイズ**。 既定値は 1 GiB です。 ストレージのサイズを決定するときは、インジェスト速度を考慮してください。 Event Grid ブローカー上のすべてのトピックに対するイベントのサイズと発行速度 (1 秒あたりのイベント数) を掛け合わせたものを MiB/秒で計測したインジェスト速度は、ストレージを割り当てる際の重要な要素となります。 イベントは本質的に一時的なものであるため、配信されると、それらのイベントに対するストレージの消費は発生しません。 インジェスト速度はストレージを消費する主な要因ではありますが、それだけではありません。 トピックとイベント サブスクリプションの構成を保持するメタデータもストレージ領域を消費しますが、通常は、Event Grid によって取り込まれたり配信されたりするイベントよりも必要なストレージ領域は少なく済みます。
    1. **メモリの制限**。 既定値は 1 GiB です。 
    1. **メモリ要求**。 既定値は 200 MiB です。 このフィールドは編集できません。

        :::image type="content" source="./media/install-k8s-extension/basics-page.png" alt-text="Event Grid 拡張機能のインストール - 基本ページ":::
    1. ページの下部にある **[次へ: 構成]** ボタンを選択します。
1. **[Event Grid のインストール]** ページの **[構成]** タブで、次の手順を行います。 
    1. **HTTP (セキュリティで保護されていない) 通信を有効にする**。 クライアントと Event Grid ブローカーとの通信に、セキュリティで保護されていないチャネルを使用する場合は、このチェックボックスをオンにします。

        > [!IMPORTANT]
        > このオプションを有効にすると、Event Grid ブローカーとのすべての通信で、トランスポートとして HTTP が使用されます。 そのため、発行元のクライアントおよび Event Grid 演算子は、Event Grid broker と安全に通信できません。 このオプションは、開発の初期段階でのみ使用してください。
    1. HTTP 通信を有効にしなかった場合は、入手した PKI 証明書ファイルをそれぞれ選択し、[PKI 証明書の要件](#pki-certificate-requirements)を満たします。

        :::image type="content" source="./media/install-k8s-extension/configuration-page.png" alt-text="Event Grid 拡張機能のインストール - 構成ページ":::
    1. ページの下部にある **[次へ: 監視]** ボタンを選択します。
1. **[Event Grid のインストール]** ページの **[監視]** タブで、次の手順を行います。
    1. **[メトリックを有効にする]** を選択します (省略可能)。 このオプションを選択すると、[Kubernetes の Event Grid] では、トピックおよびイベント サブスクリプションのさまざまなメトリクスが [Prometheus 公開形式](https://prometheus.io/docs/instrumenting/exposition_formats/)で表されます。

        :::image type="content" source="./media/install-k8s-extension/monitoring-page.png" alt-text="Event Grid 拡張機能のインストール - 監視ページ":::    
    1. **[次へ: タグ]** を選択して、 **[タグ]** ページに移動します。 
1. **[タグ]** ページで、次の手順を行います。
    1. 必要に応じて、[タグ](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)を定義します。

        :::image type="content" source="./media/install-k8s-extension/tags-page.png" alt-text="Event Grid 拡張機能のインストール - タグ ページ":::
    1. ページ下部にある **[確認と作成]** を選択します。
1. **[確認および作成]** タブで、 **[作成]** を選択します。
    
    :::image type="content" source="./media/install-k8s-extension/review-create-page.png" alt-text="Event Grid 拡張機能のインストール - 確認と作成ページ":::   
    
    > [!IMPORTANT]
    > Event Grid のインストールは非同期操作であり、Azure portal でデプロイが完了したことを知らせる通知が表示されるまでの時間よりも Kubernetes クラスターで実行される時間が長くなる可能性があります。 "デプロイが完了しました" という通知の表示後、少なくとも 5 分待ってから、カスタムの場所を作成する (次の手順) ようにしてください。 Kubernetes クラスターにアクセスできる場合は、BASH セッションで次のコマンドを実行することで、Event Grid ブローカーと Event Grid 演算子ポッドが実行状態かどうか (インストールが完了したことを示します) を検証できます。

    ```bash
    kubectl get pods -n \<release-namespace-name\>
    ```

    出力例を次に示します。

    ```bash
    NAME                                  READY   STATUS    RESTARTS   AGE
    eventgrid-broker-568f75976-wxkd2      1/1     Running   0          2m28s
    eventgrid-operator-6c4c6c675d-ttjv5   1/1     Running   0          2m28s    
    ```

    > [!IMPORTANT]
    > カスタムの場所は、新しい Event Grid トピックをデプロイする前に作成する必要があります。 カスタムの場所を作成するには、"デプロイが完了しました" という通知が表示された 5 分後に、下部にある **[コンテキスト]** ページを選択します。 あるいは、[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ExtendedLocation%2FCustomLocations) を使用してカスタムの場所を作成します。 詳細については、[カスタムの場所に関するドキュメント](../../azure-arc/kubernetes/custom-locations.md)を参照してください。
1. デプロイが成功すると、 **[拡張機能]** ページに、Event Grid 拡張機能に指定した名前のエントリが表示されます。 **[インストールの状態]** に **[保留中]** と表示されている場合は、数分間待ってから、ツールバーで **[更新]** を選択します。 

    :::image type="content" source="./media/install-k8s-extension/extension-installed.png" alt-text="Event Grid 拡張機能 - インストール済み":::   

## <a name="install-using-azure-cli"></a>Azure CLI を使用してインストールする

1. シェル セッションを開始します。 コンピューターでセッションを開始することも、ブラウザーを開いて [https://shell.azure.com](https://shell.azure.com)にアクセスすることもできます。
1. 構成ファイル ``protected-settings-extension.json`` を作成します。 このファイルは、Event Grid 拡張機能を作成するときにパラメーターとして渡されます。

   次のコマンドと各構成行で、``filename`` を、オペレーター (クライアント) またはブローカー (サーバー) の公開証明書、CA 証明書、またはキーを含む名前に適宜置き換えます。 指定された証明書はすべて、行の折り返しを行わずに Base64 でエンコードする必要があります。 このため、``base64 --wrap=0`` コマンドを使用します。 

    ```bash
    echo "{ 
        \"eventgridoperator.identityCert.base64EncodedIdentityCert\":\"$(base64 <filename> --wrap=0)\",
        \"eventgridoperator.identityCert.base64EncodedIdentityKey\":\"$(base64 <filename> --wrap=0)\",
        \"eventgridoperator.identityCert.base64EncodedIdentityCaCert\":\"$(base64 <filename> --wrap=0)\",
        \"eventgridbroker.service.tls.base64EncodedServerCert\":  \"$(base64 <filename> --wrap=0)\" ,
        \"eventgridbroker.service.tls.base64EncodedServerKey\":  \"$(base64 <filename> --wrap=0)\" ,
        \"eventgridbroker.service.tls.base64EncodedServerCaCert\":  \"$(base64 <filename> --wrap=0)\" 
    }" > protected-settings-extension.json 
    ```
    
    たとえば、ブローカーの公開証明書 (上記の最初の構成項目) が ``client.cer`` という名前である場合、最初の構成行は次のようになります。

    ```bash
    \"eventgridoperator.identityCert.base64EncodedIdentityCert\":\"$(base64 client.cer --wrap=0)\",    
    ```

1. 構成ファイル ``settings-extension.json`` を作成します。 このファイルは、Event Grid 拡張機能を作成するときにパラメーターとして渡されます。
    > [!IMPORTANT]
    > ``ServiceAccount`` と ``serviceType`` の値を変更することはできません。 プレビュー バージョンでは、サポートされている Kubernetes サービスの種類は、``ClusterIP`` のみです。

    ``storageClassName`` では、ブローカーに使用し、お使いの Kubernetes ディストリビューションでサポートされているストレージ クラスを指定します。 たとえば、AKS を使用している場合は、Azure Standard Storage を使用する `azurefile        ` を使用できます。 AKS でサポートされている定義済みのストレージ クラスの詳細については、[AKS のストレージ クラス](../../aks/concepts-storage.md#storage-classes)に関する記事を参照してください。 他の Kubernetes ディストリビューションを使用している場合は、サポートされている定義済みのストレージ クラスや、独自のストレージ クラスを指定する方法について、Kubernetes ディストリビューションのドキュメントを参照してください。

    ``reporterType`` を ``prometheus`` に設定して、[Prometheus 公開形式](https://prometheus.io/docs/instrumenting/exposition_formats/)を使用するトピックとイベント サブスクリプションのメトリクスを有効にします。  

    > [!IMPORTANT] 
    > プレビュー バージョンでは、メトリクスを取得するためのメカニズムとして、Prometheus クライアントを使用する方法のみがサポートされています。 

    ```bash
    echo "{
        \"Microsoft.CustomLocation.ServiceAccount\":\"eventgrid-operator\",
        \"eventgridbroker.service.serviceType\": \"ClusterIP\",
        \"eventgridbroker.dataStorage.storageClassName\": \"<storage_class_name>\",
        \"eventgridbroker.diagnostics.metrics.reporterType\":\"prometheus\"
    }" > settings-extension.json
    ```
    
1. Event Grid のコンポーネントをクラスターにインストールする Kubernetes 拡張機能を作成します。 

   パラメーター ``cluster-name`` および ``resource-group`` では、[クラスターを Azure Arc に接続した](../../azure-arc/kubernetes/quickstart-connect-cluster.md)ときに指定した名前と同じ名前を使用する必要があります。

   ``release-namespace`` は、Event Grid のコンポーネントをデプロイする名前空間です。 既定値は **eventgrid-system** です。 既定値をオーバーライドする値を指定できます。 たとえば、クラスターにデプロイされるすべての Azure Arc 対応サービスに対して、1 つの名前空間を使用することができます。 指定した名前空間が存在しない場合は、自動的に作成されます。

    > [!IMPORTANT]
    > プレビュー バージョンでは、Event Grid 拡張機能を作成または更新するときにサポートされるスコープは、``cluster`` のみです。 つまり、このサービスでは、Kubernetes クラスター上の Event Grid 拡張機能の単一インスタンスのみがサポートされます。名前空間スコープのデプロイはまだサポートされていません。 拡張機能のスコープの詳細については、[拡張機能インスタンスの作成](../../azure-arc/kubernetes/extensions.md#create-extensions-instance)に関するページを参照して、「``scope``」とを検索してください。

    ```azurecli-interactive
    az k8s-extension create --cluster-type connectedClusters --cluster-name <connected_cluster_name> --resource-group <resource_group_of_connected_cluster> --name <event_grid_extension_name> --extension-type Microsoft.EventGrid --scope cluster --auto-upgrade-minor-version true --release-train Stable --release-namespace <namespace_name> --configuration-protected-settings-file protected-settings-extension.json --configuration-settings-file settings-extension.json    
    ```
1. Event Grid 拡張機能が正常にインストールされたことを確認します。

    ```azurecli-interactive
    az k8s-extension show  --cluster-type connectedClusters --cluster-name <connected_cluster_name> --resource-group <resource_group_of_connected_cluster> --name <event_grid_extension_name>
    ```

    Event Grid 拡張機能が正常にデプロイされた場合、``installedState`` プロパティは ``Installed`` になります。 

### <a name="custom-location"></a>カスタムの場所

> [!IMPORTANT]
> カスタムの場所は、新しい Event Grid トピックをデプロイする前に作成する必要があります。 カスタムの場所は、[Azure portal](../../azure-arc/kubernetes/custom-locations.md#create-custom-location) を使用して作成することができます。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="azure-arc-connect-cluster-issues"></a>Azure Arc 接続クラスターに関する問題

**問題**: **Azure Arc** に移動し、左側のメニューで **[Kubernetes クラスター]** をクリックしても、表示されるページに、Event Grid をインストールしたい Kubernetes クラスターが表示されません。

**解決方法**: Kubernetes クラスターが Azure に登録されていません。 「[既存の Kubernetes クラスターを Azure Arc に接続する](../../azure-arc/kubernetes/quickstart-connect-cluster.md)」という記事の手順に従います。このステップで問題が発生した場合は、Azure Arc 対応 Kubernetes チーム宛てに[サポート リクエスト](#getting-support)を送信してください。

### <a name="event-grid-extension-issues"></a>Event Grid 拡張機能に関する問題

**問題**: "Event Grid 拡張機能" をインストールしようとすると、次のメッセージが表示されます。"**無効な操作** - この接続済みの Kubernetes クラスターには、既に Event Grid のインスタンスがインストールされています。 Event Grid の拡張機能はクラスター レベルでスコープが設定されているため、1 つのインスタンスのみをクラスターにインストールできます。"
    
**説明**: Event Grid が既にインストールされています。 Event Grid のプレビュー バージョンでは、クラスターにデプロイできる Event Grid 拡張機能インスタンスの数は 1 つだけです。


## <a name="next-steps"></a>次の手順
[カスタムの場所を作成](../../azure-arc/kubernetes/custom-locations.md)し、クイック スタート「[Kubernetes 上の Azure Event Grid でクラウド イベントを Webhook にルーティングする](create-topic-subscription.md)」の手順に従います。
