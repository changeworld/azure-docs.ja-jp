---
title: "Azure Metadata Service のスケジュールされたイベント | Microsoft Docs"
description: "影響力の強いイベントが発生する前に、Virtual Machine で対処します。"
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/10/2016
ms.author: zivr
translationtype: Human Translation
ms.sourcegitcommit: c7f552825f3230a924da6e5e7285e8fa7fa42842
ms.openlocfilehash: 541709ca17b96f8334e67dbdbbd9a10eefffa06b


---
# <a name="azure-metadata-service---scheduled-events"></a>Azure Metadata Service: スケジュールされたイベント

Azure Metadata Service では、Azure でホストされている Virtual Machine に関する情報を発見することができます。 公開されているカテゴリの&1; つであるスケジュールされたイベントによって、今後のイベント (再起動など) に関する情報が表示されるため、アプリケーションはそのイベントに対して準備をして、中断を最小限に抑えることができます。 これは、PaaS および IaaS を含むすべての Azure Virtual Machine の種類に対応しています。 このサービスにより、Virtual Machine が予防的タスクを実行する時間が確保され、イベントの影響を最小限に抑えることができます。 たとえば、インスタンスの再起動がスケジュールされていることが確認された場合、中断を回避するために、サービスはセッションの排出、新しいリーダーの選択、またはデータのコピーを行う可能性があります。



## <a name="introduction---why-scheduled-events"></a>はじめに - スケジュールされたイベントが使用される理由

スケジュールされたイベントを使用することにより、Virtual Machine の可用性に影響をおよぼす可能性のある今後のイベントを確認 (発見) でき、予防的操作を行うことによって、サービスへの影響を最小限に抑えることができます。
状態を維持するためにレプリケーションの手法を使用するマルチ インスタンスのワークロードでは、複数のインスタンスで頻繁に機能停止が発生する可能性があります。 このような機能停止により、負荷の高いタスク (インデックスの再構築など) や、レプリカの損失が発生することがあります。
その他の多くの場合では、グレースフル シャット ダウン シーケンスを使用すると、全体的なサービスの利用可能性が向上します。 たとえば、実行中のトランザクションの完了 (またはキャンセル)、クラスター内の他の VM へのタスクの再割り当て (手動フェールオーバー)、ロード バランサー プールからの Virtual Machine の削除などです。
今後のイベントについて管理者に通知したり、単にそのようなイベントのログ記録をつけたりすることで、クラウドでホストされているアプリケーションのサービスの向上につながる場合があります。

Azure Metadata Service では、以下のユース ケース内のスケジュールされたイベントが表示されます。
-   "影響力の強い" メンテナンス (ホスト OS ロールアウトなど) を開始したプラットフォーム
-   "影響力の弱い" メンテナンス (インプレース VM 移行など) を開始したプラットフォーム
-   対話型の呼び出し (ユーザーの再起動や VM の再デプロイなど)



## <a name="scheduled-events---the-basics"></a>スケジュールされたイベントの基本  

Azure Metadata Service によって、VM 内部からの REST エンドポイントを使用した Virtual Machine の実行に関する情報が表示されます。 情報は、VM の外部に公開されないように、ルーティングできない IP 経由で提供されます。

### <a name="scope"></a>Scope 
スケジュールされたイベントは、クラウド サービス内のすべての Virtual Machine または可用性セット内のすべての Virtual Machine に表示されます。 そのため、イベント内の **[リソース]** フィールドをチェックして、影響を受ける VM を特定する必要があります。

### <a name="discover-the-endpoint"></a>エンドポイントの検出
Virtual Machine が Virtual Network (VNet) 内で作成されている場合、メタデータ サービスはルーティングできない IP (169.254.169.254) 経由で提供されます。

Cloud Services (PaaS) に Virtual Machine が使用されている場合、メタデータ サービスのエンドポイントはレジストリを使用して検出される可能性があります。

    {HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure\DeploymentManagement}

### <a name="versioning"></a>バージョン管理 
メタデータ サービスでは、http://{ip}/metadata/{version}/scheduledevents の形式でバージョン管理された API が使用されます。サービスが、http://{ip}/metadata/latest/scheduledevents で提供されている最新のバージョンを使用するようにすることをお勧めします。

### <a name="using-headers"></a>ヘッダの使用
メタデータ サービスのクエリを実行するときは、ヘッダー *Metadata: true* を指定する必要があります。 

### <a name="enable-scheduled-events"></a>スケジュールされたイベントの有効化
スケジュールされたイベントを初めて呼び出すときに、Azure はこの機能を Virtual Machine で暗黙的に有効化します。 そのため、最初の呼び出しには最大で&1; 分間の遅延が生じる可能性があります。 


## <a name="using-the-api"></a>API を使用する

### <a name="query-for-events"></a>イベントのクエリ
次の呼び出しを行うだけで、スケジュールされたイベントのクエリを実行できます。

    curl -H Metadata:true http://169.254.169.254/metadata/latest/scheduledevents

応答には、スケジュールされたイベントの配列が含まれています。 空の配列は、現在スケジュールされているイベントがないことを意味します。
スケジュールされたイベントがある場合は、応答にイベントの配列が含まれます。 

    {
     "Events":[
          {
                "EventId":{eventID},
                "EventType":"Reboot" | "Redeploy" | "Pause",
                "ResourceType":"VirtualMachine",
                "Resources":[{resourceName}],
                "EventStatus":"Scheduled" | "Started",
                "NotBefore":{timeInUTC},              
         }
     ]
    }

EventType は、以下の場合に Virtual Machine が受けると想定される影響をキャプチャします。
- 一時停止: Virtual Machine は数秒間の一時停止がスケジュールされています。 メモリ、開いているファイル、ネットワーク接続への影響はありません。
- 再起動: Virtual Machine は再起動がスケジュールされています (メモリをワイプします)。
- 再デプロイ: Virtual Machine は別のノードへの移動がスケジュールされています (一時ディスクは失われます)。 

イベントがスケジュールされている (Status = Scheduled) 場合、Azure は (NotBefore フィールドで指定された) イベントを開始できる時間を共有します。

### <a name="starting-an-event-expedite"></a>イベントの開始 (高速化)

今後予定されているイベントを確認し、グレースフル シャットダウンのロジックを完了すると、**POST** 呼び出しを行うことによって Azure の処理が (可能な場合は) 高速になるように指示することができます。 
 

## <a name="powershell-sample"></a>PowerShell のサンプル 

次のサンプルでは、スケジュールされたイベントのメタデータ サーバーを読み取り、受信確認を行う前に、アプリケーション イベント ログに記録します。

```PowerShell
$localHostIP = "169.254.169.254"
$ScheduledEventURI = "http://"+$localHostIP+"/metadata/latest/scheduledevents"

# Call Azure Metadata Service - Scheduled Events 
$scheduledEventsResponse =  Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $ScheduledEventURI -Method get 

if ($json.Events.Count -eq 0 )
{
    Write-Output "++No scheduled events were found"
}

for ($eventIdx=0; $eventIdx -lt $scheduledEventsResponse.Events.Length ; $eventIdx++)
{
    if ($scheduledEventsResponse.Events[$eventIdx].Resources[0].ToLower().substring(1) -eq $env:COMPUTERNAME.ToLower())
    {    
        # YOUR LOGIC HERE 
         pause "This Virtual Machine is scheduled for to "+ $scheduledEventsResponse.Events[$eventIdx].EventType

        # Acknoledge the event to expedite
        $jsonResp = "{""StartRequests"" : [{ ""EventId"": """+$scheduledEventsResponse.events[$eventIdx].EventId +"""}]}"
        $respbody = convertto-JSon $jsonResp
       
        Invoke-RestMethod -Uri $ScheduledEventURI  -Headers @{"Metadata"="true"} -Method POST -Body $jsonResp 
    }
}


``` 


## <a name="c-sample"></a>C\# のサンプル 
次のコードは、メタデータ サービスと通信するための API を表示するクライアントのものです
```csharp
   public class ScheduledEventsClient
    {
        private readonly string scheduledEventsEndpoint;
        private readonly string defaultIpAddress = "169.254.169.254"; 

        public ScheduledEventsClient()
        {
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/latest/scheduledevents", defaultIpAddress);
        }
        /// Retrieve Scheduled Events 
        public string GetDocument()
        {
            Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Metadata", "true");
                return webClient.DownloadString(cloudControlUri);
            }   
        }

        /// Issues a post request to the scheduled events endpoint with the given json string
        public void PostResponse(string jsonPost)
        {
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Content-Type", "application/json");
                webClient.UploadString(scheduledEventsEndpoint, jsonPost);
            }
        }
    }

```
スケジュールされたイベントは、次のデータ構造を使用して解析できます 

```csharp
    public class ScheduledEventsDocument
    {
        public List<CloudControlEvent> Events { get; set; }
    }

    public class CloudControlEvent
    {
        public string EventId { get; set; }
        public string EventStatus { get; set; }
        public string EventType { get; set; }
        public string ResourceType { get; set; }
        public List<string> Resources { get; set; }
        public DateTime NoteBefore { get; set; }
    }

    public class ScheduledEventsApproval
    {
        public List<StartRequest> StartRequests = new List<StartRequest>();
    }

    public class StartRequest
    {
        [JsonProperty("EventId")]
        private string eventId;

        public StartRequest(string eventId)
        {
            this.eventId = eventId;
        }
    }

```

クライアントを使用してイベントを取得、処理、および確認するサンプル プログラム   

```csharp
public class Program
    {
    static ScheduledEventsClient client;
    static void Main(string[] args)
    {
        while (true)
        {
            client = new ScheduledEventsClient();
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval();
            foreach (CloudControlEvent ccevent in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(ccevent.EventId));
            }
            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.PostResponse(approveEventsJsonDocument);
            }

            Console.WriteLine("Complete. Press enter to repeat\n\n");
            Console.ReadLine();
            Console.Clear();
        }
    }

    private static void HandleEvents(List<CloudControlEvent> events)
    {
        // Add logic for handling events here
    }
}

```

## <a name="python-sample"></a>Python サンプル 

```python


#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url="http://169.254.169.254/metadata/latest/scheduledevents"
headers="{Metadata:true}"
this_host=socket.gethostname()

def get_scheduled_events():
   req=urllib2.Request(metadata_url)
   req.add_header('Metadata','true')
   resp=urllib2.urlopen(req)
   data=json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid=evt['EventId']
        status=evt['EventStatus']
        resources=evt['Resources'][0]
        eventype=evt['EventType']
        restype=evt['ResourceType']
        notbefore=evt['NotBefore'].replace(" ","_")
        if this_host in evt['Resources'][0]:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            print "++ Add you logic here"

def main():
   data=get_scheduled_events()
   handle_scheduled_events(data)
   

if __name__ == '__main__':
  main()
  sys.exit(0)


```
## <a name="next-steps"></a>次のステップ 
[Azure での仮想マシンに対する計画的なメンテナンス](./virtual-machines-linux-planned-maintenance.md)



<!--HONumber=Jan17_HO1-->


