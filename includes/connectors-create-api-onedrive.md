---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524211"
---
## <a name="prerequisites"></a>前提条件

* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) アカウント 

ロジック アプリで OneDrive アカウントを使用するには、OneDrive アカウントに接続するロジック アプリを承認してください。  これは、Azure ポータルのロジック アプリ内で簡単に実行できます。 

次の手順に従って、OneDrive アカウントに接続するロジック アプリを承認します。

1. ロジック アプリを作成します。 Logic Apps デザイナーで、ドロップダウン リストから **[Microsoft が管理している API を表示]** を選択し、検索ボックスに「onedrive」と入力します。 トリガーまたはアクションの 1 つを選択します。  
   ![[Microsoft のマネージド API を表示] というタイトルのダイアログ ボックスには、"onedrive" と表示されている検索ボックスがあります。 以下は、4 つのトリガーの一覧です。 一覧の 1 つ目は [OneDrive - ファイルが作成されたとき] です。これが選択されています。](./media/connectors-create-api-onedrive/onedrive-1.png)
2. これまでに OneDrive への接続を作成したことがない場合は、OneDrive の資格情報を使用してサインインするよう求められます。  
   ![[OneDrive - ファイルが作成されたとき] というタイトルのダイアログ ボックスには、[サインイン] というラベルの付いたボタンがあります。](./media/connectors-create-api-onedrive/onedrive-2.png)
3. **[サインイン]** を選択し、ユーザー名とパスワードを入力します。 **[サインイン]** をクリックします。  
   ![[サインイン] というタイトルのダイアログ ボックスには、"Microsoft アカウントを使う" ようにという指示が表示されます。 [メール アドレスまたは電話番号] と [パスワード] というラベルが付いた 2 つのテキスト ボックスがあります。また、[サインインしたままにする] というラベルが付いたチェック ボックスと、[サインイン] というラベルの付いたボタンもあります。](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    この資格情報は、接続するロジック アプリの承認と、OneDrive アカウントのデータへのアクセスに使用されます。 
4. **[はい]** を選択して、OneDrive アカウントの使用をロジック アプリに承認します。  
   ![[このアプリがあなたの情報にアクセスすることを許可しますか?] というタイトルのダイアログ ボックス で、次の 4 つのことを行うためのアクセス許可が要求されます。1) "自動的にサインインする"、2) "あなたのメール アドレスにアクセスする"、3) "あなたの情報にいつでもアクセスする"、および 4) "OneDrive ファイルにアクセスする"。 アクセス許可を付与するための [はい] ボタンと、拒否するための [いいえ] ボタンがあります。 これらのアプリケーションのアクセス許可を変更するためのリンクがあります。](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. 接続が作成されたことを確認します。 これで、ロジック アプリで他の手順に進むことができます。  
   ![[ファイルが作成されたとき] というタイトルのダイアログ ボックスには、関連付けられている参照ボタンを持つ [フォルダー] というタイトルのテキスト ボックスがあります。](./media/connectors-create-api-onedrive/onedrive-5.png)

