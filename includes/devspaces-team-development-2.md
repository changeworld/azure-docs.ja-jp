---
title: インクルード ファイル
description: インクルード ファイル
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 5d66dcaccc6ca2e40fbd516f535ec56c1baf6b17
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195624"
---
### <a name="run-the-service"></a>サービスを実行する

1. F5 キーを押して (またはターミナル ウィンドウで「`azds up`」と入力して)、サービスを実行します。 新たに選択した _dev/scott_ 空間で、サービスが自動的に実行されます。 
1. `azds list-up` をもう一度実行すると、サービスが独自の空間で実行されていることを確認できます。 *mywebapi* のインスタンスが _dev/scott_ 空間で実行されていることがわかります (_dev_ で実行されているバージョンもまだ実行されていますが、リストされていません)。

    ```
    Name                      DevSpace  Type     Updated  Status
    mywebapi                  scott     Service  3m ago   Running
    mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
    webfrontend               dev       Service  26m ago  Running
    ```

1. `azds list-uris` を実行して、*webfrontend* のアクセス ポイント URL を確認します。

    ```
    Uri                                                                        Status
    -------------------------------------------------------------------------  ---------
    http://localhost:53831 => mywebapi.scott:80                                Tunneled
    http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
    ```

1. *scott.s* というプレフィックスの付いた URL を使用してアプリケーションに移動します。 この更新された URL が引き続き解決されることに注意してください。 この URL は _dev/scott_ 空間に固有のものです。 この特別な URL は、"scott URL" に送信された要求で、まず、_dev/scott_ 空間内のサービスへのルーティングが試行され、失敗した場合は、_dev_ 空間内のサービスにフォールバックされることを示します。

<!--
TODO: replace 2 & 3 with below once bug#753164 and PR#158827 get pushed to production.

You can confirm that your service is running in its own space by running `azds list-up` again. First, you'll notice an instance of *mywebapi* is now running in the _dev/scott_ space (the version running in _dev_ is still running but it is not listed). If you run `azds list-uris`, you will notice that the access point URL for *webfrontend* is prefixed with the text "scott.s.". This URL is unique to the _dev/scott_ space. The special URL signifies that requests sent to the "Scott URL" will try to first route to services in the _dev/scott_ space, but if that fails, they will fall back to services in the _dev_ space.

```
Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

```
Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```
-->

![](../articles/dev-spaces/media/common/space-routing.png)

Azure Dev Spaces のこの組み込み機能を使用すれば、共有空間内でコードをテストできます。その場合、各開発者が、自分の空間内にサービスをフル スタックで再作成する必要はありません。 このルーティングを行うには、このガイドの前の手順で示したように、アプリ コードで伝達ヘッダーを転送する必要があります。

### <a name="test-code-in-a-space"></a>空間内でコードをテストする
*mywebapi* の新しいバージョンと *webfrontend* をテストするには、ブラウザーで *webfrontend* のパブリック アクセス ポイントの URL を開き、About ページに移動します。 すると、新しいメッセージが表示されます。

ここで、URL の "scott.s" の部分を削除し、 ブラウザーをリフレッシュします。 (_dev_ で実行されている *mywebapi* バージョンによる) 以前の動作が表示されます。

最新の変更を常に含む _dev_ 空間があり、このチュートリアルのセクションで説明されているようにアプリケーションが DevSpace の空間ベースのルーティングを利用するように設計されていると仮定した場合、大規模なアプリケーションのコンテキスト内で新機能をテストする際に Dev Spaces がいかに役立つかは容易に想像できます。 "_すべての_" サービスをプライベート空間にデプロイする代わりに、_dev_ から派生するプライベート空間を作成し、実際に作業しているサービスのみを "アップ" することができます。 Dev Spaces ルーティング インフラストラクチャでは、_dev_ 空間で実行されている最新バージョンを既定で使用する一方で、プライベート空間で検出できる限りのサービスを利用して、残りの処理が行われます。 さらにすばらしいことに、"_複数の_" 開発者が互いに影響を与えることなく、自分の空間で同時に、異なるサービスをアクティブに開発することができます。

### <a name="well-done"></a>お疲れさまでした。
ファースト ステップ ガイドを修了しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Azure でマネージド Kubernetes クラスターを使用して Azure Dev Spaces をセットアップする。
> * コンテナー内のコードを繰り返し開発する。
> * 2 つのサービスを個別に開発し、Kubernetes の DNS サービス検索を使用して別のサービスを呼び出す。
> * チーム環境でコードを生産的に開発してテストする。
> * Dev Spaces を使用して機能のベースラインを確立し、より大規模なマイクロサービス アプリケーションのコンテキスト内で、分離された変更を簡単にテストする

Azure Dev Spaces の概要を理解できたら、[開発空間をチーム メンバーと共有](../articles/dev-spaces/how-to/share-dev-spaces.md)し、共同作業を始めましょう。

## <a name="clean-up"></a>クリーンアップ
すべての開発スペースとその内部で実行されているサービスを含め、クラスター上の Azure Dev Spaces インスタンスを完全に削除するには、`az aks remove-dev-spaces` コマンドを使用します。 この操作は元に戻せないことに注意してください。 Azure Dev Spaces のサポートをクラスターに再度追加することはできますが、始めからやり直すようになります。 古いサービスとスペースは復元されません。

次の例では、アクティブなサブスクリプションの Azure Dev Spaces コントローラーを一覧表示し、リソース グループ "myaks-rg" の AKS クラスター "myaks" に関連付けられている Azure Dev Spaces コントローラーを削除します。

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```