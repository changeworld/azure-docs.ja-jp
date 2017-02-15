---
title: "Visual Studio での Azure プライベート クラウドへのアクセス | Microsoft Docs"
description: "Visual Studio を使用してプライベート クラウドのリソースにアクセスする方法について説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 26fea09810e4a5ed1dc19123a5354905ec3e37ea


---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Visual Studio での Azure プライベート クラウドへのアクセス
## <a name="overview"></a>Overview
既定では、Visual Studio は Azure パブリック クラウドの REST エンドポイントをサポートします。 ただし、このことは、Visual Studio を Azure プライベート クラウドと共に使用している場合に問題となる場合があります。 証明書を使用すると、Azure プライベート クラウドの REST エンドポイントにアクセスするように Visual Studio を構成できます。 この証明書は、Azure 発行設定ファイルを使用して入手できます。

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Visual Studio で Azure プライベート クラウドにアクセスするには
1. プライベート クラウドの [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)で、発行設定ファイルをダウンロードするか、管理者に連絡して発行設定ファイルを入手します。 パブリック版の Azure では、このファイルをダウンロードするためのリンクは [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/) です (ダウンロードしたファイルには .publishsettings という拡張子が付きます)。
2. Visual Studio の**サーバー エクスプローラー**で、**[Azure]** ノードを選択し、ショートカット メニューの **[サブスクリプションの管理]** コマンドを選択します。
   
    ![Manage subscriptions command](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)
3. **[Microsoft Azure サブスクリプションの管理]** ダイアログ ボックスで、**[証明書]** タブを選択し、**[インポート]** ボタンをクリックします。
   
    ![Importing Azure certificates](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)
4. **[Microsoft Azure サブスクリプションのインポート]** ダイアログ ボックスで、発行設定ファイルを保存したフォルダーを参照してファイルを選択し、**[インポート]** ボタンをクリックします。 発行設定ファイル内の証明書が Visual Studio にインポートされます。 これで、プライベート クラウドのリソースを操作できるようになりました。
   
    ![Importing publish settings](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## <a name="next-steps"></a>次のステップ
[Visual Studio から Azure クラウド サービスへの発行](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[方法: 発行設定とサブスクリプション情報のダウンロードとインポート](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)




<!--HONumber=Nov16_HO3-->


