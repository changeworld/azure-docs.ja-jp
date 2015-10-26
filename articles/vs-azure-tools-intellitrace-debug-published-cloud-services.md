<properties 
   pageTitle="IntelliTrace および Visual Studio を使用した発行済みのクラウド サービスのデバッグ | Microsoft Azure"
   description="IntelliTrace および Visual Studio を使用した発行済みのクラウド サービスのデバッグ"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/14/2015"
   ms.author="patshea" />



# IntelliTrace および Visual Studio を使用した発行済みのクラウド サービスのデバッグ

##概要

IntelliTrace を使用すると、ロール インスタンスを Azure で実行する際に広範なデバッグ情報を記録できます。問題の原因を調べる必要がある場合は、IntelliTrace ログを使用して、コードが Azure で実行されているかのように Visual Studio から調査することができます。実際には、IntelliTrace は Azure アプリケーションが Azure のクラウド サービスとして実行されている際にキー コードの実行および環境データを記録し、その記録されたデータを Visual Studio で再生できるようにします。代わりに、リモート デバッグを使用して、Azure で実行されているクラウド サービスに直接接続することができます。「[クラウド サービスのデバッグ](http://go.microsoft.com/fwlink/p/?LinkId=623041)」を参照してください。

>[AZURE.IMPORTANT]IntelliTrace は、デバッグ シナリオのみを対象としており、運用環境のデプロイには使用できません。

>[AZURE.NOTE]Visual Studio Enterprise がインストールされており、Azure アプリケーションが .NET Framework 4 以降のバージョンを対象としている場合に IntelliTrace を使用することができます。IntelliTrace は、Azure ロールの情報を収集します。これらのロールの仮想マシンは、常に 64 ビット オペレーティング システムを実行します。

## IntelliTrace の Azure アプリケーションを構成するには

IntelliTrace を Azure アプリケーションで有効にするには、Visual Studio Azure プロジェクトからアプリケーションを作成し、発行する必要があります。IntelliTrace は Azure に発行する前に Azure アプリケーション向けに構成する必要があります。IntelliTrace を構成せずにアプリケーションを発行したが、後で構成が必要になった場合は、Visual Studio からもう一度アプリケーションを発行する必要があります。詳細については、「[Azure Tools を使用したクラウド サービスの発行](http://go.microsoft.com/fwlink/p/?LinkId=623012)」を参照してください。

1. Azure アプリケーションをデプロイする準備ができたら、プロジェクトのビルド ターゲットが **[デバッグ]** に設定されていることを確認します。

1. ソリューション エクスプ ローラーで Azure プロジェクトのショートカット メニューを開き、**[発行]** を選択します。
 
    [Azure アプリケーションの公開] ウィザードが表示されます。

1. クラウドへの発行時にアプリケーションの IntelliTrace ログを収集するには、**[IntelliTrace を有効にする]** チェック ボックスをオンにします。

    >[AZURE.NOTE]Azure アプリケーションを発行するときに、IntelliTrace またはプロファイルを有効にすることができます。両方を有効にすることはできません。

1. 基本の IntelliTrace 構成をカスタマイズするには、**[設定]** ハイパーリンクを選択します。

    次の図に示すように、[IntelliTrace の設定] ダイアログが表示されます。どのイベントを記録するか、呼び出し情報を収集するかどうか、どのモジュールとプロセスのログを収集するか、どれくらいの領域を記録に割り当てるかを指定できます。IntelliTrace の詳細については、[IntelliTrace によるデバッグ](http://go.microsoft.com/fwlink/?LinkId=214468)に関するページを参照してください。

    ![VST\_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

IntelliTrace ログは、IntelliTrace 設定で指定された最大サイズの循環ログ ファイルです(既定のサイズは 250 MB)。IntelliTrace ログは、仮想マシンのファイル システム内のファイルに収集されます。ログを要求すると、スナップショットがその時点で取得され、ローカル コンピューターにダウンロードされます。

Azure のアプリケーションが Azure に発行されると、次の図のようにサーバー エクスプ ローラーの Azure の計算ノードから IntelliTrace が有効になっているかどうかを判断することができます。

![VST\_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## ロール インスタンスの IntelliTrace ログのダウンロード

ロール インスタンスの IntelliTrace ログは、**サーバー エクスプローラー**の **[Cloud Services]** ノードからダウンロードすることができます。対象のインスタンスが表示されるまで **[Cloud Services]** ノードを展開し、そのインスタンスのショートカット メニューを開いて **[IntelliTrace ログの表示]** を選択します。IntelliTrace ログは、ローカル コンピューター上のディレクトリのファイルにダウンロードされます。IntelliTrace ログを要求するたびに、新しいスナップショットが作成されます。

ログをダウンロードすると、Visual Studio の [Azure のアクティビティ ログ] ウィンドウに操作の進行状況が表示されます。次の図に示すように、操作の行項目を展開して詳細を表示することができます。

![VST\_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

IntelliTrace ログのダウンロード中も、Visual Studio での作業を続行することができます。ログのダウンロードが完了すると、ログが Visual Studio で自動的に開きます。

>[AZURE.NOTE]IntelliTrace ログには、フレームワークが生成し、後から処理する例外が含まれる場合があります。内部のフレームワーク コードでは、このような例外がロールの起動時の正常な処理として生成されるため、無視しても問題ありません。

## 関連項目

[クラウド サービスのデバッグ](https://msdn.microsoft.com/library/ee405479.aspx)

<!---HONumber=Oct15_HO3-->