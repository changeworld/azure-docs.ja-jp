<properties 
   pageTitle="IPv6 の概要 | Microsoft Azure"
   description="Azure での IPv6 アドレスの使用について説明します。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="telmos" />

# Azure での IPv6 のサポート

IPv6 プロトコルは、パブリック インターネットでの 32 ビット IPv4 アドレスの制限に対応するため、Internet Engineering Task Force (IETF) によって 1998 年に最初に導入されました。インターネットに接続されるモバイル デバイスの数の増加と、モノのインターネット (IoT) の登場により、パブリック インターネットでは IPv6 アドレスが頻繁に使用されるようになっています。

>[AZURE.IMPORTANT] Azure での IPv6 は現在プライベート プレビューであり、すべてのユーザーが使用できる状態ではありません。すべてのユーザーがこの機能を利用できるようになったら、このページは更新されます。

リソース マネージャーのデプロイの Azure IaaS VM で、パブリック インターネットとの発信/着信接続に IPv6 アドレスを使用できます。次の一覧では、Azure での IPv6 の機能について説明します。

- **リソース マネージャーでの IaaS VM**
	- VM には、IPv4 と IPv6 両方のエンドポイントがあります。
	- VM は、着信 (ロード バランサーと NAT) 接続用および発信 (SNAT) 接続用に、ロード バランサーのパブリック IP 経由でインターネットに接続できます。
	- VM は、IPv4 エンドポイントを使用してすべての Azure サービスに接続します。
	- VM は、East-West 通信には IPv4 エンドポイントのみを使用します。
- **Azure DNS**
	- Azure DNS は IPv6 をサポートし、IPv4 A レコードと共に AAAA レコードを提供します。
	- Azure DNS は、AAAA と A レコードの両方が存在するときは、ルックアップに対して両方でクエリに応答します。 
- **Office 365**
	- Office 365 サービスは IPv6 をサポートします。
	- ExpressRoute 回線を使用して、Office365 内の IPv6 リソースにオンプレミスのネットワークから接続できます。

## リソース マネージャーでの IaaS VM

IPv6 による接続は、リソース マネージャー デプロイ モデルを使用することによってのみ利用できます。リソース マネージャー デプロイのすべての VM には IPv4 エンドポイントと IPv6 エンドポイントがありますが、次のように、IPv6 エンドポイントはロード バランサー経由でパブリック インターネットと接続する場合にのみ使用できます。

![IPv6 接続](./media/virtual-network-ipv6-overview/figure1.png)

## Azure DNS

Azure DNS で IPv6 AAAA レコードをホストできます。Azure DNS は、リソースのクエリを受信すると、そのホスト名で使用可能なすべてのレコードで応答します。たとえば、特定のホスト名 (例: www.contoso.com) に A レコードと AAAA レコードがある場合、Azure DNS はクエリへの応答として両方のアドレスを送信します。Azure DNS または AAAA レコードをサポートする他の DNS サーバーから応答を受信したとき、接続に IPv4 または IPv6 のどちらのエンドポイントを使用するかは、DNS クライアントが決定します。

>[AZURE.NOTE] Windows コンピューターは、常に、IPv4 接続を試みる前に IPv6 を使用して接続を試みます。

## Office 365

ExpressRoute 回線と Microsoft ピアリングを使用することにより、オンプレミスのネットワークと Office365 の間で IPv6 接続を使用できます。Microsoft ピアリングでの接続における [ExpressRoute の動作方法](../expressroute/expressroute-introduction.md)を理解しておいてください。

## 次のステップ

- [Azure DNS](../dns/dns-overview.md) について学習します。
- [ExpressRoute](../expressroute/expressroute-introduction.md) について学習します。

<!---HONumber=AcomDC_0309_2016-->