<properties
   pageTitle="F5 キーを使用した Visual Studio での Service Fabric アプリケーションのデバッグ"
   description="Visual Studio とローカル開発クラスターを使用して、サービスの信頼性とパフォーマンスを改善する。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2015"
   ms.author="jesseb"/>

# F5 キーを使用した Visual Studio での Service Fabric アプリケーションのデバッグ

ローカル コンピューターの開発クラスターで Service Fabric アプリケーションをデプロイしデバッグすることにより、時間と費用を節約できます。Visual Studio では、アプリケーションをローカル クラスターにデプロイして、アプリケーションのすべてのインスタンスにデバッガーを自動的に接続できます。

1. 「[Service Fabric 開発環境の設定](service-fabric-get-started.md)」にある手順に従って、ローカル開発クラスターを開始してください。

2. **[F5]** キーを押して、**[デバッグ]**、**[デバッグの開始]** の順にクリックします

    ![アプリケーションのデバッグを開始する][startdebugging]

3. コードにブレークポイントを設定し、**[デバッグ]** メニューのコマンドをクリックしてアプリケーションをステップ実行します。

    > [AZURE.NOTE]Visual Studio は、アプリケーションのすべてのインスタンスにアタッチします。コードのステップ実行中に、ブレークポイントに複数のプロセスがヒットして、同時セッションになる場合があります。ヒットしたブレークポイントを無効にし、ブレークポイントをスレッド ID の条件付きにするか、診断イベントを使用します。

4. **[診断イベント]** ウィンドウが自動的に開き、診断イベントをリアルタイムで表示します。

    ![リアルタイムに診断イベントを表示する][diagnosticevents]

5. **[診断イベント]** ウィンドウは、サーバー エクスプローラーで開くこともできます。**[Azure]** の下で、**[Service Fabric クラスター]**、**[診断イベントの表示...]** の順に右クリックします。

    ![診断イベント ウィンドウを開く][viewdiagnosticevents]

6. 診断イベントは自動的に生成される **ServiceEventSource.cs** でも確認でき、アプリケーション コードから呼び出されます。

    ```csharp
    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, Service.ServiceTypeName);
    ```

7. **[診断イベント]** ウィンドウは、フィルター処理、一時停止、およびリアルタイムのイベント検査をサポートしています。このフィルターは、イベント メッセージとその内容の単純な文字列検索です。

    ![フィルター処理、一時停止と再開、またはリアルタイムのイベント検査][diagnosticeventsactions]

8. サービスのデバッグは、その他のアプリケーションのデバッグと似ています。通常 Visual Studio で簡単なデバッグ用のブレークポイントを設定できます。信頼性の高いコレクションが複数のノードにレプリケートされている場合でも、IEnumerable は実装されているため、デバッグ中に Visual Studio の結果ビューを使用して内部保存されているものを確認できます。コードの任意の場所にブレークポイントを設定するだけです。

    ![アプリケーションのデバッグを開始する][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

- [Service Fabric サービスのテスト](service-fabric-test-your-service-index.md)。
- [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)。

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
 

<!---HONumber=Oct15_HO3-->