<properties
   pageTitle="Azure クラスターの証明書の更新 | Microsoft Azure"
   description="Azure Key Vault への新しい証明書のアップロード、および既存の Service Fabric クラスターの証明書の更新と削除の方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# Azure Service Fabric クラスターの証明書の追加と削除

Service Fabric では、クラスター作成時に証明書のセキュリティを構成する場合、プライマリとセカンダリの証明書を指定できます。既定では、作成時に指定した証明書がプライマリになります。クラスターの作成後に、新しい証明書をセカンダリの証明書として追加したり、既存の証明書を削除することができます。Service Fabric での X.509 証明書 の使用に関する詳細については、 [クラスターのセキュリティ シナリオ](service-fabric-cluster-security.md)に関する記事を参照してください。

>[AZURE.NOTE] セキュリティで保護されたクラスターの場合、常に有効な (失効していない、期限切れではない) 証明書 (プライマリまたはセカンダリ) を 1 つ以上デプロイする必要があります。デプロイしないと、クラスターにアクセスできなくなります。

## セカンダリ証明書を追加する
セカンダリ証明書として別の証明書を追加するには、Azure Key Vault にその証明書をアップロードしてから、クラスター内の VM にデプロイします。詳細については、「[Deploy certificates to VMs from a customer-managed key vault」](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)(ユーザーが管理する Key Vault から VM に証明書をデプロイする) を参照してください。

1. [Key Vault に X.509 証明書をアップロードする](service-fabric-secure-azure-cluster-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault)
2. [Azure ポータル](https://portal.azure.com/) にサインインし、この証明書を追加するクラスター リソースを参照します。
3. **[設定]** で、証明書の設定をクリックし、セカンダリ証明書の拇印を入力します。
4. **[保存]** をクリックします。デプロイが開始され、正常に完了すると、クラスターに対する管理操作にプライマリ証明書かセカンダリ証明書を使用できるようになります。

![Azure ポータルの証明書拇印のスクリーン ショット][SecurityConfigurations\_02]

## 証明書を削除する
古い証明書を削除してクラスターで使用されないようにするには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com/) にサインインし、クラスターのセキュリティ設定に移動します。
2. 古い証明書を削除します。
3. **[保存]** をクリックして新しいデプロイを開始します。デプロイが完了すると、削除した証明書がクラスターへの接続に使用されることはなくなります。

## 次のステップ
クラスター管理の詳細については、次の記事を参照してください。

- [Service Fabric クラスターのアップグレード プロセスと機能](service-fabric-cluster-upgrade.md)
- [クライアント用のロールベースのアクセスの設定](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0720_2016-->