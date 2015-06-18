<properties 
   pageTitle="フェールオーバーの負荷分散の構成"
   description="この記事では、Traffic Manager でフェールオーバーの負荷分散を構成する方法について説明します。"
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# フェールオーバーの負荷分散の構成

多くの場合、組織ではサービスの信頼性を維持する必要があります。そのために、会社のプライマリ サービスがダウンした場合に備えてバックアップ サービスを提供します。サービスのフェールオーバーの一般的なパターンでは、同一サービスのセットを用意し、プライマリ サービスにトラフィックを送信しながら、1 つ以上のバックアップ サービスで構成したリストを維持します。この種類のバックアップは、以下の手順に従って、Azure Cloud Services および Azure Websites を使用して構成できます。

Azure Websites では、Web サイトのモードにかかわらず、データセンター (「リージョン」と呼びます) 内の Web サイト用に、フェールオーバー負荷分散機能があらかじめ用意されています。Traffic Manager を使用すると、別のデータセンター内の Web サイトに対してフェールオーバー負荷分散を指定できます。

## フェールオーバーの負荷分散を構成するには:

1. 管理ポータルの左ペインで、**[Traffic Manager]** アイコンをクリックして [Traffic Manager] ウィンドウを開きます。まだ Traffic Manager プロファイルを作成していない場合は、[Traffic Manager プロファイルの管理](traffic-manager-manage-profiles.md)に関するページに記載された手順に従って、基本的な Traffic Manager プロファイルを作成してください。
2. 管理ポータルの [Traffic Manager] ペインで、変更するエンドポイント設定が含まれた Traffic Manager プロファイルを見つけ、プロファイル名の右にある矢印をクリックします。これにより、プロファイルの設定ページが開きます。
3. プロファイル ページで、ページの上部にある **[エンドポイント]** をクリックし、構成に含めるクラウド サービスと Web サイトの両方 (エンドポイント) が存在することを確認します。プロファイルにエンドポイントを追加または削除する手順については、「[Traffic Manager でのエンドポイントの管理](traffic-manager-endpoints.md).」を参照してください。
4. プロファイル ページで、上部にある **[構成]** をクリックし、構成ページを開きます。
5. **[負荷分散方法の設定]** で、負荷分散方法が **[フェールオーバー]** に設定されていることを確認します。他の方法に設定されている場合は、ドロップダウン リストから **[フェールオーバー]** をクリックします。
6. **[フェールオーバー優先度リスト]** で、エンドポイントのフェールオーバー順序を調整します。**フェールオーバー**負荷分散方法を選択する場合、選択したエンドポイントの順序が重要です。最上位のエンドポイントがプライマリ エンドポイントになります。上下の矢印を使用して、必要に応じて順序を変更します。Windows PowerShell を使用してフェールオーバーの優先度を設定する方法については、[Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880) に関するページを参照してください。
7. **[監視の設定]** が適切に構成されていることを確認します。監視を構成することで、オフラインになっているエンドポイントにトラフィックが送信されなくなります。エンドポイントを監視するには、パスとファイル名を指定する必要があります。スラッシュ "/" は、相対パスの有効な入力値で、ファイルがルート ディレクトリ (既定のディレクトリ) にあることを意味します。監視の詳細については、[Traffic Manager の監視](traffic-manager-monitoring.md).に関するページを参照してください。
8. 構成の変更が完了したら、ページの下部にある **[保存]** をクリックします。
9. 構成の変更をテストします。詳細については、「[Traffic Manager の設定のテスト](traffic-manager-testing-settings.md)」を参照してください。
10. Traffic Manager プロファイルが設定されて機能したら、権限のある DNS サーバー上の DNS レコードを編集して、会社のドメイン名が Traffic Manager ドメイン名を参照するようにします。これを行う方法の詳細については、[会社のインターネット ドメインが Traffic Manager ドメインをポイントするように設定する方法](traffic-manager-point-internet-domain.md).に関するページを参照してください。

## 関連項目

[Traffic Manager での負荷分散方法について](traffic-manager-load-balancing-methods.md)

[Traffic Manager を構成する方法](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Traffic Manager の概要](../traffic-manmager-overview.md)

[クラウド サービス](http://go.microsoft.com/fwlink/?LinkId=314074)

[Web サイト](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Traffic Manager の操作 (REST API リファレンス)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager コマンドレット](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!--HONumber=49--> 