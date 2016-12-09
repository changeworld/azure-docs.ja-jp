---
title: "Azure Toolkit for Eclipse を使用してセッション アフィニティを有効にする"
description: "Azure Toolkit for Eclipse を使用してセッション アフィニティを有効にする方法について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 88c595ec-7d85-40bd-9078-8d6be7b3f0fa
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a7320a7e5052ffb4171307bc8636182447ddfc50


---
# <a name="enable-session-affinity"></a>セッション アフィニティを有効にする
Azure toolkit for Eclipse の中で、ロールに対する HTTP セッション アフィニティ (スティッキー セッション) を有効にできます。 次の図は、セッション アフィニティ機能を有効にするために使用する **負荷分散** のプロパティ ダイアログを示しています。

![][ic719492]

## <a name="to-enable-session-affinity-for-your-role"></a>ロールに対するセッション アフィニティを有効にするには
1. Eclipse の [Project Explorer (プロジェクト エクスプローラー)] でロールを右クリックし、**[Azure]**、**[Load Balancing (負荷分散)]** の順にクリックします。
2. **[Properties for WorkerRole1 Load Balancing]** ダイアログで次のように操作します。
   1.  **[Enable HTTP session affinity (sticky sessions) for this role]**
   2. **[Input endpoint to use (使用する入力エンドポイント)]** で、使用する入力エンドポイント (例: **http (パブリック: 80、プライベート: 8080)**) を選択します。 アプリケーションは、HTTP エンドポイントとしてこのエンドポイントを使用する必要があります。 ロールに対して複数のエンドポイントを有効にすることができますが、スティッキー セッションをサポートするにはその中の 1 つだけを選択できます。
   3. アプリケーションをリビルドします。

有効にすると、1 つ以上のロール インスタンスがある場合、特定のクライアントからの HTTP 要求は、同じロール インスタンスによって処理され続けます。

Eclipse Toolkit では、各ロール インスタンスに Application Request Routing (ARR) と呼ばれる特別な IIS モジュールをインストールすることによってこれを可能にします。 ARR は、HTTP 要求を適切なロール インスタンスに再ルーティングします。 ツールキットは、着信 HTTP トラフィックが ARR ソフトウェアに最初にルーティングされるように、選択されたエンドポイントを自動的に再構成します。 さらに、ツールキットは、Java サーバーがリッスンするように構成される新しい内部エンドポイントを作成します。 このエンドポイントが、HTTP トラフィックを適切なロール インスタンスに再ルーティングするためにARR によって使用されます。 このように、マルチインスタンス デプロイの各ロール インスタンスが他のすべてのインスタンスに対するリバース プロキシとして機能することで、スティッキー セッションが有効になります。

## <a name="notes-about-session-affinity"></a>セッション アフィニティに関する注意事項
* セッション アフィニティは、コンピューティング エミュレーターでは機能しません。 設定は、コンピューティング エミュレーターのビルド処理や実行を妨げることなくコンピューティング エミュレーターに適用できますが、機能自体はコンピューティング エミュレーターでは機能しません。
* セッション アフィニティを有効にすると、Azure でのデプロイで使用されるディスク容量が増加します。これは、Azure クラウドでサービスが開始されたときに、追加ソフトウェアがダウンロードされインストールされるためです。
* 各ロールの初期化時間が長くなります。
* 上記のようにトラフィック リルーターとして機能する内部エンドポイントが追加されます。

セッション アフィニティが有効であるときにセッション データを維持する方法の例については、[セッション アフィニティを使用してセッション データを維持する方法][セッション アフィニティを使用してセッション データを維持する方法]に関するページを参照してください。

## <a name="see-also"></a>関連項目
[Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]

[Azure の Hello World アプリケーションを Eclipse で作成する][Azure の Hello World アプリケーションを Eclipse で作成する]

[Azure Toolkit for Eclipse のインストール][Azure Toolkit for Eclipse のインストール] 

[セッション アフィニティを使用してセッション データを維持する方法][セッション アフィニティを使用してセッション データを維持する方法]

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター][Azure Java デベロッパー センター]を参照してください。

<!-- URL List -->

[Azure Java デベロッパー センター]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure の Hello World アプリケーションを Eclipse で作成する]: http://go.microsoft.com/fwlink/?LinkID=699533
[セッション アフィニティを使用してセッション データを維持する方法]: http://go.microsoft.com/fwlink/?LinkID=699539
[Azure Toolkit for Eclipse のインストール]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->



<!--HONumber=Nov16_HO3-->


