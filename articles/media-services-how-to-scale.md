<properties linkid="manage-services-mediaservices-scale-media-service" urlDisplayName="How to scale" pageTitle="How to Scale a media service | Azure Documentation" metaKeywords="" description="Learn how to scale Media Services by specifying the number of On-Demand Streaming Reserved Units and Encoding Reserved Units that you would like your account to be provisioned with." metaCanonical="" services="media-services" documentationCenter="" title="How to Scale a Media Service" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# メディア サービスの規模の設定方法

[WACOM.INCLUDE [disclaimer][]]

メディア サービスは、アカウントのプロビジョニングに使用する**オンデマンド ストリーミング占有ユニット**および**エンコード占有ユニット**の数を指定することで規模を設定できます。

## オンデマンド ストリーミング占有ユニット

オンデマンド ストリーミング占有ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できるほか、[動的パッケージ化機能][]などの追加機能を利用できるようになります。既定では、オンデマンド ストリーミングは、サーバー リソース (コンピューティング、送信容量など) を他のユーザーと共有する共有インスタンス モデルとして構成されます。オンデマンド ストリーミングのスループットを高めるために、オンデマンド ストリーミング占有ユニットの購入をお勧めします。

オンデマンド ストリーミング占有ユニットの数を変更するには、以下の手順を実行します。

1.  [管理ポータル][]で、**[メディア サービス]** をクリックします。次に、メディア サービスの名前をクリックします。

2.  [オリジン] ページを選択します。次に、変更するオリジンをクリックします。

    ![[オリジン] ページ](./media/media-services-how-to-scale/media-services-origin-page.png)

3.  占有ユニットの数を指定するには、[スケール] タブをクリックし、**[占有容量]** スライダーを動かします。

    ![[スケール] ページ](./media/media-services-how-to-scale/media-services-origin-scale.png)

4.  [保存] ボタンを押して、変更を保存します。

    オンデマンド ストリーミングの新しいユニットの割り当ては完了するまでに約 20 分かかります。

    **注:** 現在のところ、オンデマンド ストリーミング占有ユニットの数を正の値からゼロに戻すと、オンデマンド ストリーミングが最大 1 時間無効になります。

    **注:** コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。料金設定の詳細については、「[Azure 料金早見表][]」を参照してください。

## エンコード占有ユニット

用意したエンコード占有ユニットの数が、所定のアカウントで並列処理できるメディア タスクの数になります。たとえば、アカウントの占有ユニットの数が 5 である場合、処理するタスクがある限り、5 個のメディア タスクが並列実行されます。残りのタスクはキューで待機して、実行中のタスクが完了すると直ちにキューから取り出されて順番に処理されます。アカウントに占有ユニットが用意されていない場合、タスクは逐次処理されます。この場合、あるタスクが終了した後、次のタスクが開始するまでの待機時間は、システムのリソースが利用できるかどうかに左右されます。

エンコード占有ユニットの数を変更するには、以下の手順を実行します。

1.  [管理ポータル][]で、**[メディア サービス]** をクリックします。次に、メディア サービスの名前をクリックします。

2.  [プロセッサ] ページを選択します。

    ![[プロセッサ] ページ](./media/media-services-how-to-scale/media-services-encoding-scale.png) 

3.  [保存] ボタンを押して、変更を保存します。

    新しいエンコード占有ユニットは、ほぼ即座に割り当てられます。

    **注:** コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。

## サポート チケットを開く

既定で、各メディア サービス アカウントは最大 25 個のエンコードと 5 個のオンデマンド ストリーミング占有ユニットを設定できます。サポート チケットを開くと、上限の拡大を要求できます。

サポート チケットを開くには、以下の手順を実行します。

1.  [管理ポータル][1]で自分の Azure アカウントにログインします。
2.  [[サポート]](http://www.windowsazure.com/en-us/support/contact/) に移動します。
3.  [サポートの要求] をクリックします。
4.  サブスクリプションを選択します。
5.  サポートの種類として [技術] を選択します。
6.  [チケットの作成] をクリックします。
7.  次のページに示される製品一覧で [Azure Media Services] を選択します。
8.  [問題の種類] として [メディア処理] を選択し、カテゴリとして [占有ユニット] を選択します。
9.  [続行] をクリックして続行します。
10. 次のページにある指示に従って、必要なエンコードまたはオンデマンド ストリーミング占有ユニットの数を入力します。
11. [送信] をクリックして、チケットを開きます。

  [disclaimer]: ../includes/disclaimer.md
  [動的パッケージ化機能]: http://go.microsoft.com/fwlink/?LinkId=276874
  [管理ポータル]: https://manage.windowsazure.com/
  [Azure 料金早見表]: http://go.microsoft.com/fwlink/?LinkId=275107
  [1]: http://manage.windowsazure.com
