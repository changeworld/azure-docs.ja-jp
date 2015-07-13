
<properties 
    pageTitle="Azure RemoteApp の新機能。"
    description="Azure RemoteApp の変更点と改善点について説明します。" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/30/2015" 
    ms.author="elizapo" />



# RemoteApp の新機能

RemoteApp の利点の 1 つは、常に改善の努力がされていることです。改善を行うたびに、どのような変更を加えたかをここでお知らせします。


## 2015 年 6 月

多くの機能が変更されました。 6 月、チームは非常に忙しい日々を過ごしました。

- Azure RemoteApp [ランディング ページ](https://www.remoteapp.windowsazure.com/)が変更されました。チェックしてください。 
- サブスクリプションの一部として利用できるすべてのイメージでソフトウェアを更新しました。
- ハイブリッド コレクションを改善しました。強制トンネリング対応となり、コレクションの作成前に IP サブネット サイズを確認します。
- Web カメラの場合、* ワイルドカードが適切に機能しないことが判明しました。代わりに、インスタンス ID または GUID を指定する必要があります。リダイレクト情報を更新し、これを反映する予定です。
- それにより、Azure ギャラリーからテンプレート イメージを作成するとき、イメージにカスタムのウイルス対策ソフトウェアを追加できます。

7 月にさらに多くの変更点をロールアウトします。近いうちに新しい更新で戻ってきます。

## 2015 年 5 月

最初にこのトピックを作ってから長い時間が経過し、さまざまな機能が追加されました。そのため、このリストは少し異なり、3 月の初めから 5 月の終わりまでを対象にしています。新しい機能:

- すべてを自動化 - Azure RemoteApp に [Azure PowerShell モジュールのコマンドレット](remoteapp-tutorial-arawithpowershell.md)が与えられました。 
- [Azure 仮想マシンから Azure RemoteApp イメージを作成します](remoteapp-image-on-azurevm.md)。カスタム イメージを Azure にアップロードする作業が大幅に速くなりました。
- RemoteApp VNET ではなく Azure VNET を使用し、企業ネットワーク リソースを Azure に接続します。[ハイブリッド コレクションの指示](remoteapp-create-hybrid-deployment.md)を更新しました。段階的に Azure VNET を作成できます (その手順 1)。
- VNET とに関しては、VNET サイズの制限と制約に関する[新しい指針](remoteapp-vnetsizing.md)を確認してください。
- また、制限に関してですが、[サービスの上限と初期値](remoteapp-servicelimits.md)とは何でしょうか。

Azure RemoteApp について詳細を知りたいですか。 数週間前、RemoteApp チームは大勢で Ignite に出動しました。Eric の動画、「[Microsoft Azure RemoteApp の管理の基礎](http://channel9.msdn.com/Events/Ignite/2015/BRK3868)」をご覧ください。

Azure RemoteApp が実際に使われている様子を確認したいですか。 「[どこからでもあらゆるデバイスであらゆるアプリを実行する](remoteapp-anyapp.md)」チュートリアルをご覧ください。ユーザーとアクセスを強要する方法が紹介されています。データベース ファイルを共有することもできます。同じ [Office 365](remoteapp-tutorial-o365anywhere.md) をあらゆるデバイスで実行する方法に関するチュートリアルも用意しています。

お読みいただいてありがとうございました。翌月はさらに多くの更新を届けます。

<!---HONumber=July15_HO1-->