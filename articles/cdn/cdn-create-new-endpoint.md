<properties
	 pageTitle="Azure CDN の使用"
	 description="このトピックでは、Azure の Content Delivery Network (CDN) を有効にする方法を説明します。このチュートリアルでは、新しい CDN プロファイルとエンドポイントの作成手順について説明します。"
	 services="cdn"
	 documentationCenter=""
	 authors="camsoper"
	 manager="erikre"
	 editor=""/>
<tags
	 ms.service="cdn"
	 ms.workload="media"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="05/11/2016" 
	 ms.author="casoper"/>

# Azure CDN の使用  

このトピックでは、CDN プロファイルと CDN エンドポイントを新しく作成することによって Azure CDN を有効にする手順を紹介しています。

>[AZURE.IMPORTANT] CDN の機能の概要と、機能の一覧については、「[CDN の概要](./cdn-overview.md)」を参照してください。

## 新しい CDN プロファイルを作成する

CDN プロファイルは、CDN エンドポイントのコレクションです。各プロファイルには、1 つ以上の CDN エンドポイントが含まれます。複数のプロファイルを使って、インターネット ドメイン、Web アプリケーション、またはその他の一部の基準別に CDN エンドポイントを整理する必要が生じる場合があります。

> [AZURE.NOTE] 既定では、1 つの Azure サブスクリプションは 4 つの CDN プロファイルに限定されます。各 CDN プロファイルは 10 個の CDN エンドポイントに制限されます。
>
> CDN の価格は、CDN プロファイル レベルで適用されます。Azure CDN の価格レベルを組み合わせて使用する場合は、複数の CDN プロファイルが必要になります。

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## 新しい CDN エンドポイントを作成する

**新しい CDN エンドポイントを作成するには**

1. [Azure ポータル](https://portal.azure.com)で、CDN プロファイルに移動します。これは、前の手順でダッシュボードにピン留めしている可能性があります。ピン留めしていない場合は、**[参照]**、**[CDN プロファイル]** の順にクリックし、エンドポイントの追加先のプロファイルをクリックします。

    CDN プロファイル ブレードが表示されます。

    ![CDN プロファイル][cdn-profile-settings]

2. **[エンドポイントの追加]** ボタンをクリックします。

    ![[エンドポイントの追加] ボタン][cdn-new-endpoint-button]

    **[エンドポイントの追加]** ブレードが表示されます。

    ![[エンドポイントの追加] ブレード][cdn-add-endpoint]

3. この CDN エンドポイントの**名前**を入力します。この名前は、ドメイン `<endpointname>.azureedge.net` でキャッシュされたリソースにアクセスする際に使用します。

4. **[配信元の種類]** ドロップダウンで、配信元の種類を選択します。Azure ストレージ アカウントの場合は **[ストレージ]** を、Azure クラウド サービスの場合は **[クラウド サービス]** を、Azure Web アプリの場合は **[Web アプリ]** を選択します。それ以外の、Azure または他の場所にホストされたパブリックにアクセス可能な Web サーバーが配信元である場合は、**[カスタムの配信元]** を選択します。

	![CDN 配信元の種類](./media/cdn-create-new-endpoint/cdn-origin-type.png)
		
5. **[配信元ホスト名]** ドロップダウンで、配信元のドメインを選択または入力します。ドロップダウンに、手順 4 で指定した種類の利用可能な配信元がすべて一覧表示されます。**[配信元の種類]** として *[カスタムの配信元]* を選択した場合は、カスタムの配信元のドメインを入力します。

6. **[元のパス]** ボックスに、キャッシュするリソースへのパスを入力するか、空のままにして、手順 5 で指定したドメインでのリソースをキャッシュするようにします。

7. **[配信元のホスト ヘッダー]** で、CDN の各要求で送信するホスト ヘッダーを入力するか、既定値をそのまま使用します。

	> [AZURE.WARNING] Azure Storage や Web Apps など、配信元の種類によっては、ホスト ヘッダーを配信元のドメインに合わせる必要があります。ホスト ヘッダーがそのドメインと異なっていることをご利用の配信元で要求される場合以外は、既定値をそのまま使用してください。

8. **[プロトコル]** と **[配信元ポート]** に、配信元のリソースへのアクセスに使用するプロトコルとポートを指定します。少なくとも 1 つのプロトコル (HTTP または HTTPS) を選択する必要があります。
	
	> [AZURE.NOTE] **[配信元ポート]** は、エンドポイントが配信元から情報を取得するときに使用されるポートにのみ影響します。エンドポイントそのものは、**配信元ポート**に関係なく、エンド クライアントが既定の HTTP ポートと HTTPS ポート (80 と 443) を介してのみ利用できます。
	>
	> **Azure CDN from Akamai** エンドポイントでは、配信元の TCP ポート範囲全体が許可されません。許可されない配信元ポートの一覧については、「[Azure CDN from Akamai behavior details (Azure CDN from Akamai の動作の詳細)](cdn-akamai-behavior-details.md)」を参照してください。
	>
	> HTTPS を使用して CDN コンテンツにアクセスする場合、次の制約があります。
	> 
	> - CDN によって提供される SSL 証明書を使用する必要があります。サード パーティの証明書はサポートされません。
	> - CDN から提供されたドメイン (`<endpointname>.azureedge.net`) を使用して、HTTPS コンテンツにアクセスする必要があります。現時点では CDN がカスタム証明書をサポートしていないために、HTTPS サポートではカスタム ドメイン名 (CNAME) を使用できません。

9. **[追加]** ボタンをクリックして、新しいエンドポイントを作成します。

10. エンドポイントが作成されると、プロファイルのエンドポイントの一覧に表示されます。一覧には、キャッシュされたコンテンツへのアクセスに使用する URL と元のドメインが表示されます。

    ![CDN エンドポイント][cdn-endpoint-success]

    > [AZURE.IMPORTANT] 登録内容が CDN に反映されるまでに時間がかかるため、エンドポイントは、すぐには利用できません。<b>Azure CDN from Akamai</b> プロファイルの場合、通常、反映は 1 分以内で完了します。<b>Azure CDN from Verizon</b> プロファイルの場合、通常、反映は 90 分以内に完了しますが、もっと時間がかかる場合もあります。
	>	 
	> エンドポイントの構成が POP に反映される前に CDN のドメイン名を利用しようとすると、HTTP 404 応答コードがユーザーに表示されます。エンドポイントを作成してから数時間が経過しても、404 が応答として返される場合は、「[404 状態を返す CDN エンドポイントのトラブルシューティング](cdn-troubleshoot-endpoint.md)」を参照してください。


##関連項目
- [クエリ文字列による要求のキャッシュ動作の制御](cdn-query-string.md)
- [CDN コンテンツをカスタム ドメインにマッピングする方法](cdn-map-content-to-custom-domain.md)
- [Azure CDN エンドポイント上のアセットを事前に読み込む](cdn-preload-endpoint.md)
- [Azure CDN エンドポイントの消去](cdn-purge-endpoint.md)
- [404 状態を返す CDN エンドポイントのトラブルシューティング](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png

<!---HONumber=AcomDC_0518_2016-->