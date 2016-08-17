<properties 
	pageTitle="クラウド サービスを構成する (ポータル) | Microsoft Azure" 
	description="Azure のクラウド サービスの構成方法について説明します。クラウド サービスの構成の更新方法と、ロール インスタンスへのリモート アクセスの構成方法を紹介します。これらの例では、Azure ポータルを使用します。" 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/27/2016"
	ms.author="adegeo"/>

# Cloud Services の構成方法

> [AZURE.SELECTOR]
- [Azure ポータル](cloud-services-how-to-configure-portal.md)
- [Azure クラシック ポータル](cloud-services-how-to-configure.md)

クラウド サービスで最もよく使用される設定は Azure ポータルで構成できます。また、構成ファイルを直接更新する場合は、サービス構成ファイルをダウンロードして内容を更新し、更新したファイルをアップロードして、クラウド サービスの構成を更新します。どちらの方法でも、構成の更新はすべてのロール インスタンスに適用されます。

また、クラウド サービス ロールまたはリモート デスクトップのインスタンスを管理することもできます。

Azure で構成の更新中に 99.95% の可用性を保証できるのは、各ロールに少なくとも 2 つのロール インスタンスがある場合だけです。この場合、1 台の仮想マシンでクライアントからの要求を処理し、もう 1 台で更新を行うことができます。詳細については、「[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」を参照してください。

## クラウド サービスの変更

[Azure ポータル](https://portal.azure.com/)を開いた後、クラウド サービスに移動します。ここから、多くの部分を管理します。

![[設定] ページ](./media/cloud-services-how-to-configure-portal/cloud-service.png)

**[設定]** または **[すべての設定]** リンクからは **[設定]** ブレードが開き、**プロパティ**の設定、**構成**の変更、**証明書**の管理、**アラート ルール**のセットアップ、このクラウド サービスへのアクセス権を持つ**ユーザー**の管理を行うことができます。

![Azure クラウド サービス設定ブレード](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

>[AZURE.NOTE]
**Azure ポータル**を使用して、クラウド サービスで使用するオペレーティング システムを変更することはできません。この設定を変更するには、[Azure クラシック ポータル](http://manage.windowsazure.com/)を使用する必要があります。詳細については、[ここ](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file)を参照してください。

## Monitoring

クラウド サービスにアラートを追加できます。**[設定]**、**[アラート ルール]**、**[アラートの追加]** の順にクリックします。

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

ここでは、アラートをセットアップできます。**[メトリック]** ボックスの一覧で、次の種類のデータのアラートを設定できます。

- ディスクの読み取り
- ディスクの書き込み
- ネットワーク受信
- ネットワーク送信
- CPU の割合

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### メトリック タイルから監視を構成する

**[設定]** の **[アラート ルール]** を使用する代わりに、**[クラウド サービス]** ブレードの **[監視]** セクションのメトリック タイルのいずれかをクリックできます。

![クラウド サービスの監視](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

ここからは、タイルで使用するグラフをカスタマイズしたり、アラート ルールを追加したりできます。


## 再起動、再イメージ化、またはリモート デスクトップ

現時点では、**Azure ポータル**を使用してリモート デスクトップを構成することはできません。ただし、[Azure クラシック ポータル](cloud-services-role-enable-remote-desktop.md)、[PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)、または [Visual Studio](../vs-azure-tools-remote-desktop-roles.md) を使用すれば設定できます。

最初に、クラウド サービス インスタンスをクリックします。

![クラウド サービス インスタンス](./media/cloud-services-how-to-configure-portal/cs-instance.png)

開かれるブレードでは、リモート デスクトップ接続の開始、インスタンスのリモート再起動、インスタンスのリモートでの再イメージ化 (新しいイメージで開始) を行うことができます。

![クラウド サービス インスタンスのボタン](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)



## .cscfg を再構成する

[サービス構成 (cscfg)](cloud-services-model-and-package.md#cscfg) ファイルを使用してクラウド サービスを再構成することが必要な場合があります。.cscfg ファイルをダウンロードし、変更して、アップロードする必要があります。

1. **[設定]** アイコンまたは **[すべての設定]** リンクをクリックして、**[設定]** ブレードを開きます。

    ![[設定] ページ](./media/cloud-services-how-to-configure-portal/cloud-service.png)

2. **[構成]** 項目をクリックします。

    ![[構成] ブレード](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)

3. **[ダウンロード]** ボタンをクリックします。

    ![ダウンロード](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

4. サービス構成ファイルを更新した後、次のステップでファイルをアップロードして構成の更新内容を適用します。

    ![アップロード](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
    
5. .cscfg ファイルを選択し、**[OK]** をクリックします。

			
## 次のステップ

* 方法: [クラウド サービスをデプロイする](cloud-services-how-to-create-deploy-portal.md)
* [カスタム ドメイン名を構成する](cloud-services-custom-domain-name-portal.md)
* [クラウド サービスを管理する](cloud-services-how-to-manage-portal.md)
* [SSL 証明書を構成する](cloud-services-configure-ssl-certificate-portal.md)

<!---HONumber=AcomDC_0803_2016-->