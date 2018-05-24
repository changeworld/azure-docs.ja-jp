---
title: C# で Azure Service Fabric のリライアブル サービスを作成する
description: Visual Studio を使用して、Azure Service Fabric を基盤とした Reliable Services アプリケーションを作成、デプロイ、デバッグします。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/14/2018
ms.author: ryanwi
ms.openlocfilehash: 7e64bc34f5c39edaf87cc732d7c4702655df0e3e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212672"
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>C# で最初の Service Fabric ステートフル Reliable Services アプリケーションを作成する

Windows に初めての .NET 対応 Azure Service Fabric アプリケーションをわずか数分でデプロイする方法について説明します。 完了すると、Reliable Services アプリケーションがデプロイされたローカル クラスターが稼働している状態になります。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、[開発環境がセットアップ](service-fabric-get-started.md)されていることを確認してください。 このプロセスでは、Service Fabric SDK と Visual Studio 2017 または 2015 のインストールが必要です。

## <a name="create-the-application"></a>アプリケーションを作成する

1. 管理者として Visual Studio を起動します。

2. Ctrl キー、Shift キー、N キーを同時に押して、プロジェクトを作成します。

3. **[新しいプロジェクト]** ダイアログ ボックスで、**[クラウド]** > **[Service Fabric アプリケーション]** の順に選択します。

4. アプリケーションに **MyApplication** という名前を付けます。 **[OK]** をクリックします。

   ![Visual Studio の [新しいプロジェクト] ダイアログ ボックス][1]

5. 次のダイアログ ボックスでは、任意の種類の Service Fabric アプリケーションを作成できます。 このクイック スタートでは、**[.Net Core 2.0]** > **[ステートフル サービス]** を選択します。

6. サービスに **MyStatefulService** という名前を付けます。 **[OK]** をクリックします。

    ![Visual Studio の [新しいサービス] ダイアログ ボックス][2]

    Visual Studio によって、アプリケーション プロジェクトとステートフル サービス プロジェクトが作成され、 ソリューション エクスプローラーに表示されます。

    ![アプリケーションとステートフル サービスの作成後に表示されるソリューション エクスプローラー][3]

    このアプリケーション プロジェクト (**MyApplication**) には、コードが一切存在しません。 代わりに、一連のサービス プロジェクトが参照されます。 また、このアプリケーション プロジェクトにはほかに次の 3 種類のコンテンツがあります。

    * **発行プロファイル**  
    さまざまな環境にデプロイするためのプロファイルです。

    * **スクリプト**  
    アプリケーションをデプロイまたはアップグレードするための PowerShell スクリプトです。

    * **アプリケーション定義**  
*ApplicationPackageRoot* に、アプリケーションの構成を定義する ApplicationManifest.xml ファイルが配置されています。 関連するアプリケーション パラメーター ファイルは *ApplicationParameters* に配置されています。このファイルを使用して、環境に合わせたパラメーターを指定できます。 関連する発行プロファイルで指定されたアプリケーション パラメーター ファイルが Visual Studio によって選択されます。
    
サービス プロジェクトのコンテンツの概要については、「 [Service Fabric の Reliable Services の概要](service-fabric-reliable-services-quick-start.md)」を参照してください。

## <a name="deploy-and-debug-the-application"></a>アプリケーションをデプロイしデバッグする

アプリケーションができたので、今度は次の手順に従って実行、デプロイ、デバッグを行います。

1. Visual Studio で F5 キーを押して、デバッグ用にアプリケーションをデプロイします。

    >[!NOTE]
    >初めてアプリケーションをローカルで実行してデプロイすると、Visual Studio によってデバッグ用にローカル クラスターが作成されます。 これには時間がかかる場合があります。 Visual Studio の出力ウィンドウにクラスターの作成状態が表示されます。

    クラスターの準備が整うと、SDK に含まれているローカル クラスター システム トレイ マネージャー アプリケーションから通知が表示されます。
    
    ![ローカル クラスター システム トレイ通知][4]

    アプリケーションが起動すると、Visual Studio によって診断イベント ビューアーが自動的に呼び出されます。ここでは、サービスからのトレース出力を確認することができます。
    
    ![診断イベント ビューアー][5]

    >[!NOTE]
    >診断イベント ビューアーによる追跡は、イベントによって自動的に開始されます。 診断イベント ビューアーを手動で構成する必要がある場合は、最初にプロジェクト **MyStatefulService** にある `ServiceEventSource.cs` ファイルを開きます。 `ServiceEventSource` クラスの先頭にある `EventSource` 属性の値をコピーします。 次の例ではイベント ソースの名前が `"MyCompany-MyApplication-MyStatefulService"` になっていますが、これは状況によって異なる場合があります。
>
    >![サービスのイベント ソース名の特定][service-event-source-name]


2. **[ETW プロバイダー]** ダイアログ ボックスを開きます。 次に、**[診断イベント]** タブにある歯車アイコンを選択します。コピーしたイベント ソースの名前を **[ETW プロバイダー]** 入力ボックスに貼り付けます。 終わったら、**[適用]** ボタンを選択します。 これにより、イベントのトレースが自動的に開始されます。

    ![診断イベント ソース名の設定][setting-event-source-name]

    これで、[診断イベント] ウィンドウにイベントが表示されます。

    ステートフル サービス テンプレートでは、**MyStatefulService.cs** の `RunAsync` メソッドでインクリメントされているカウンター値が表示されます。

3. コードが実行されているノードなど、詳細を確認するには、イベントのいずれかを展開します。 この例では、**\_Node\_0** になっていますが、使用するマシンによって異なる可能性があります。
   
    ![診断イベント ビューアーの詳細][6]

4. ローカル クラスターには、1 台のマシンでホストされている 5 つのノードが含まれています。 運用環境では、ホストされているノードは物理マシンまたは仮想マシン 1 台につき 1 つだけです。 Visual Studio デバッガーの実行中にマシンが消失する状況をシミュレートするために、ローカル クラスター上のノードのいずれかをダウンさせてみましょう。

5. **[ソリューション エクスプローラー]** ウィンドウで、**MyStatefulService.cs** を開きます。 

6. `RunAsync` メソッドを探し、メソッドの 1 行目にブレークポイントを設定します。

    ![ステートフル サービスの RunAsync メソッド内のブレークポイント][7]

7. Service Fabric Explorer ツールを起動するために、**[ローカル クラスター マネージャー]** システム トレイ アプリケーションを右クリックし、**[ローカル クラスターの管理]** を選択します。

    ![ローカル クラスター マネージャーから Service Fabric Explorer を起動する][systray-launch-sfx]

    [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) では、クラスターが視覚的に表現されます。 クラスターにデプロイされたアプリケーションのセットや、クラスターを構成する物理ノードのセットも表示されます。

8. 左側のウィンドウで、**[クラスター]** > **[ノード]** の順に展開し、コードが実行されているノードを見つけます。 次に、マシンの再起動をシミュレートするため、**[アクション]** > **[非アクティブにする (再起動)]** の順にクリックします。

    ![Service Fabric Explorer でノードを停止する][sfx-stop-node]

    すぐに、Visual Studio にブレークポイント ヒットが表示されます。これは、1 つのノードで行っていた計算が別のノードにシームレスにフェールオーバーするためです。

9. 次に、診断イベント ビューアーに戻り、メッセージを確認します。 イベントが実際には別のノードから取得されている場合でも、カウンターが継続的にインクリメントされています。

    ![フェールオーバー後の診断イベント ビューアー][diagnostic-events-viewer-detail-post-failover]

## <a name="clean-up-the-local-cluster-optional"></a>ローカル クラスターをクリーンアップする (省略可能)

このローカル クラスターは実際のローカル クラスターなので注意してください。 デバッガーを停止すると、アプリケーション インスタンスが削除され、アプリケーションの種類の登録が解除されます。 ただし、クラスターは引き続きバックグラウンドで実行されます。 ローカル クラスターを停止する準備ができたら、次のいずれかのオプションを実行します。

### <a name="keep-application-and-trace-data"></a>アプリケーションとトレース データを保持する

クラスターをシャットダウンします。具体的には、**[ローカル クラスター マネージャー]** システム トレイ アプリケーションを右クリックし、**[ローカル クラスターの停止]** を選択します。

### <a name="delete-the-cluster-and-all-data"></a>クラスターとすべてのデータを削除する

クラスターを削除します。具体的には、**[ローカル クラスター マネージャー]** システム トレイ アプリケーションを右クリックし、 **[ローカル クラスターの削除]** を選択します。 

このオプションを選択すると、次にアプリケーションを実行したときに、クラスターが再デプロイされます。 しばらくの間ローカル クラスターを使用しない場合や、リソースを解放する必要がある場合に、このオプションを選択してください。

## <a name="next-steps"></a>次の手順
Reliable Services の詳細については、[こちら](service-fabric-reliable-services-introduction.md)を参照してください。
<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
