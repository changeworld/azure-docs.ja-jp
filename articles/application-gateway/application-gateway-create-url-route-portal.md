---
title: Create a Path-based rule for an application gateway by using the portal | Microsoft Docs
description: Learn how to create a Path-based rule for an application gateway by using the portal
services: application-gateway
documentationcenter: na
author: georgewallace
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2016
ms.author: gwallace

---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>Create a Path-based rule for an application gateway by using the portal
> [!div class="op_single_selector"]
> * [Azure portal](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> 
> 

URL Path-based routing enables you to associate routes based on the URL path of Http request. It checks if there is a route to a back-end pool configured for the URL lists in Application Gateway and send the network traffic to the defined back-end pool. A common use for URL-based routing is to load balance requests for different content types to different back-end server pools.

URL-based routing introduces a new rule type to application gateway. Application gateway has two rule types: basic and Path-Based rules. Basic rule type provides round-robin service for the back-end pools while Path-Based rules in addition to round robin distribution, also takes path pattern of the request URL into account while choosing the backend pool.

## <a name="scenario"></a>Scenario
The following scenario goes through creating a Path-based rule in an existing application gateway.
The scenario assumes that you have already followed the steps to [Create an Application Gateway](application-gateway-create-gateway-portal.md).

![url route][scenario]

## <a name="<a-name="createrule"></a>create-the-path-based-rule"></a><a name="createrule"></a>Create the Path-based rule
A Path-based rule requires its own listener, before creating the rule be sure to verify you have an available listener to use.

### <a name="step-1"></a>Step 1
Navigate to http://portal.azure.com and select an existing application gateway. Click **Rules**

![Application Gateway overview][1]

### <a name="step-2"></a>Step 2
Click **Path-based** button to add a new Path-based rule.

### <a name="step-3"></a>Step 3
The **Add path-based rule** blade has two sections. The first section is where you defined the listener, the name of the rule and the default path settings. The default path settings are for routes that do not fall under the custom path-based route. The second section of the **Add path-based rule** blade is where you define the path-based rules themselves.

**Basic Settings**

* **Name** - This is a friendly name to the rule that is accessible in the portal.
* **Listener** - This is the listener that is used for the rule.
* **Default backend pool** - This setting is the setting that defines the back-end to be used for the default rule
* **Default HTTP settings** - This setting is the setting that defines the HTTP settings to be used for the default rule.

**Path-based rules**

* **Name** - This is a friendly name to path-based rule.
* **Paths** - This setting defines the path the rule will look for when forwarding traffic
* **Backend Pool** - This setting is the setting that defines the back-end to be used for the rule
* **HTTP setting** - This setting is the setting that defines the HTTP settings to be used for the rule.

> [!IMPORTANT]
> Paths: The list of path patterns to match. Each must start with / and the only place a "\*" is allowed is at the end. Valid examples are /xyz, /xyz* or /xyz/*.  
> 
> 

![Add path-based rule blade with information filled out][2]

Adding a path-based rule to an existing application gateway is an easy process through the portal. Once a path-based rule has been created, it can be edited to add additional rules easily. 

![adding additional path-based rules][3]

## <a name="next-steps"></a>Next steps
To learn how to configure SSL Offloading with Azure Application Gateway see [Configure SSL Offload](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png


<!--HONumber=Oct16_HO2-->


