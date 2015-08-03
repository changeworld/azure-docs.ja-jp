<properties 
	pageTitle="Remote Desktop Gateway and Azure Multi-Factor Authentication Server using RADIUS" 
	description="これは、RADIUS を使用してリモート デスクトップ (RD) ゲートウェイと Azure Multi-Factor Authentication Server をデプロイする際に役立つ Azure Multi-Factor Authentication のページです。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="billmath"/>

# Remote Desktop Gateway and Azure Multi-Factor Authentication Server using RADIUS

多くの場合、リモート デスクトップ ゲートウェイは、ローカルの NPS を使用してユーザーを認証します。このドキュメントでは、リモート デスクトップ ゲートウェイから RADIUS 要求を (ローカルの NPS を通じて) Multi-Factor Authentication Server にルーティングする方法について説明します。

Multi-Factor Authentication Server は別個のサーバーにインストールする必要があります。これにより、RADIUS 要求を NPS に戻してリモート デスクトップ ゲートウェイ サーバーに送信します。NPS でユーザー名とパスワードが検証されると、Multi-Factor Authentication Server に応答が返され、ゲートウェイに結果を返す前に認証の 2 つ目の要素が実行されます。





## RD ゲートウェイの構成

RD ゲートウェイは、RADIUS 認証を Azure Multi-Factor Authentication Server に送信するように構成する必要があります。RD ゲートウェイがインストールおよび構成されて動作を開始したら、RD ゲートウェイのプロパティに移動します。[RD CAP ストア] タブに移動し、NPS を実行するローカル サーバーではなく、NPS を実行する中央サーバーを使用するように変更します。RADIUS サーバーとして 1 つ以上の Azure Multi-Factor Authentication Server を追加し、共有シークレットを各サーバーに指定します。





## NPS の構成

RD ゲートウェイは、NPS を使用して Azure Multi-Factor Authentication に RADIUS 要求を送信します。RD ゲートウェイが多要素認証が完了する前にタイムアウトしないように、タイムアウトするまでの時間を変更する必要があります。NPS を構成するには、次の手順に従います。

1. NPS で、左の列にある [RADIUS クライアント] と [RADIUS サーバー] のメニューを展開し、[リモート RADIUS サーバー グループ] をクリックします。TS ゲートウェイ サーバー グループのプロパティに移動します。表示される RADIUS サーバーを編集し、[負荷分散] タブに移動します。[要求が破棄されたとみなされる応答待ち時間 (秒数)] と [サーバーが利用不可能とみなされる要求間隔 (秒数)] を 30 ～ 60 秒にします。[認証/アカウント] タブをクリックし、指定されている RADIUS ポートと、Multi-Factor Authentication Server がリッスンするポートが一致していることを確認します。
2. また、NPS は Azure Multi-Factor Authentication Server から戻される RADIUS 認証を受信するように構成する必要があります。左側のメニューで [RADIUS クライアント] をクリックします。Azure Multi-Factor Authentication Server を RADIUS クライアントとして追加します。フレンドリ名を選択し、共有シークレットを指定します。
3. 左側のナビゲーションの [ポリシー] セクションを展開し、[接続要求ポリシー] をクリックします。これには、RD ゲートウェイの構成時に作成された、TS GATEWAY AUTHORIZATION POLICY と呼ばれる接続要求ポリシーが含まれています。このポリシーは、Multi-Factor Authentication Server に RADIUS 要求を転送します。
4. このポリシーをコピーして新しいポリシーを作成します。新しいポリシーに、クライアントのフレンドリ名を、手順 2 の Azure Multi-Factor Authentication Server の RADIUS クライアントに設定したフレンドリ名と一致させる条件を追加します。認証プロバイダーをローカル コンピューターに変更します。このポリシーは、RADIUS 要求を Azure Multi-Factor Authentication Server から受信した際に、RADIUS 要求を Multi-Factor Authentication Server に戻してループ状態にするのではなく、ローカルで認証が行われるようにします。ループ状態を回避するのには、Multi-Factor Authentication Server に要求を転送する元のポリシーの上にこの新しいポリシーを配置する必要があります。

## Azure Multi-Factor Authentication の構成


--------------------------------------------------------------------------------



Azure Multi-Factor Authentication Server は、RD ゲートウェイと NPS 間の RADIUS プロキシとして構成されます。これは、RD ゲートウェイ サーバーとは別個のドメインに参加しているサーバーにインストールする必要があります。Azure Multi-Factor Authentication Server を構成するには、次の手順に従います。

1. Azure Multi-Factor Authentication Server を開き、[RADIUS 認証] アイコンをクリックします。[RADIUS 認証を有効にする] チェック ボックスをオンにします。
2. [クライアント] タブで、ポートが NPS の構成と一致していることを確認し、[追加...] ボタンをクリックします。RD ゲートウェイ サーバーの IP アドレス、アプリケーション名 (省略可能)、および共有シークレットを追加します。共有シークレットは、Azure Multi-Factor Authentication Server と RD ゲートウェイの両方で同じである必要があります。
3. [ターゲット] タブをクリックし、[RADIUS サーバー] ラジオ ボタンを選択します。
4. [追加…] ボタンをクリックします。IP アドレス、共有シークレット、および NPS サーバーのポートを入力します。一元的な NPS を使用していない限り、RADIUS クライアントと RADIUS ターゲットは同じになります。共有シークレットは、NPS サーバーの RADIUS クライアント セクションの 1 つのセットアップと一致している必要があります。 

![RADIUS 認証](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

<!---HONumber=July15_HO4-->