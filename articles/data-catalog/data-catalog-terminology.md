<properties
   pageTitle="Azure Data Catalog terminology | Microsoft Azure"
   description="This article provides an introduction to concepts and terms used in Azure Data Catalog documentation."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>


# <a name="azure-data-catalog-terminology"></a>Azure Data Catalog terminology

## <a name="catalog"></a>Catalog

The Azure Data Catalog is a cloud-based metadata repository in which data sources and data assets can be registered. The catalog serves as a central storage location for structural metadata extracted from data sources and for descriptive metadata added by users.

## <a name="data-source"></a>Data source

A data source is a system or container that manages data assets. Examples include SQL Server databases, Oracle databases, SQL Server Analysis Services databases (tabular or multidimensional) and SQL Server Reporting Services servers.

## <a name="data-asset"></a>Data asset

Data assets are objects contained within data sources that can be registered with the catalog. Examples include SQL Server tables and views, Oracle tables and views, SQL Server Analysis Services measures, dimensions and KPIs, and SQL Server Reporting Services reports.

## <a name="data-asset-location"></a>Data asset location

The catalog stores the location of a data source or data asset, which can be used to connect to the source using a client application. The format and details of the location vary based on the data source type. For example, a SQL Server table can be identified by its four part name – server name, database name, schema name, object name – while a SQL Server Reporting Services Report can be identified by its URL.

## <a name="structural-metadata"></a>Structural metadata

Structural metadata is the metadata extracted from a data source that describes the structure of a data asset. This includes the assets location, its object name and type, and additional type-specific characteristics. For example, the structural metadata for tables and views includes the names and data types for the object’s columns.

## <a name="descriptive-metadata"></a>Descriptive metadata

Descriptive metadata is metadata that describes the purpose or intent of a data asset. Typically descriptive metadata is added by catalog users using the Azure Data Catalog portal, but it can also be extracted from the data source during registration. For example, the Azure Data Catalog registration tool will extract descriptions from the Description property in SQL Server Analysis Services and SQL Server Reporting Services, and from the [ms_description extended property](https://technet.microsoft.com/library/ms190243.aspx) in SQL Server databases, if these properties have been populated with values.

## <a name="request-access"></a>Request access

A data asset's descriptive metadata can include information on how to request access to the data asset or data source. This information is presented with the data asset location, and can include one or more of the following options:

- The email address of the user or team responsible for granting access to the data source.
- The URL of the documented process that users must follow to gain access to the data source.
- The URL of an identity and access management tool (such as Microsoft Identity Manager) that can be used to gain access to the data source.
- A free-text entry that describes how users can gain access to the data source.

## <a name="preview"></a>Preview

A preview in Azure Data Catalog is a snapshot of up to 20 records that can be extracted from the data source during registration, and stored in the catalog with the data asset metadata. The preview can help users who discover a data asset better understand its function and purpose. In other words, seeing sample data can be more valuable than seeing just the column names and data types.
Previews are only supported for tables and views, and must be explicitly selected by the user during registration.

## <a name="data-profile"></a>Data Profile

A data profile in Azure Data Catalog is a snapshot of table-level and column-level metadata about a registered data asset that can be extracted from the data source during registration, and stored in the catalog with the data asset metadata. The data profile can help users who discover a data asset better understand its function and purpose. Similar to previews, data profiles must be explicitly selected by the user during registration.

> [AZURE.NOTE] Extracting a data profile can be a costly operation for large tables and views, and may significantly increase the time required to register a data source.

## <a name="user-perspective"></a>User perspective

In Azure Data Catalog, any user can provide descriptive metadata for a registered data asset. Each user has a distinct perspective on the data and its use. For example, the administrator responsible for a server may provide the details of its service level agreement (SLA) or backup windows; a data steward may provide links to documentation for the business processes the data supports; and an analyst may provide a description in the terms that are most relevant to other analysts, and which can be most valuable to those users who need to discover and understand the data.

Each of these perspectives are inherently valuable, and with Azure Data Catalog each user can provide the information that is meaningful to them, while all users can use that information to understand the data and its purpose.

## <a name="expert"></a>Expert

An expert is a user who has been identified as having an informed “expert” perspective for a data asset. Any user can add themselves or another user as an expert for an asset. Being listed as an expert does not convey any additional privileges in Azure Data Catalog; it allows users to easily locate those perspectives that are most likely to be useful when reviewing an asset’s descriptive metadata.

## <a name="owner"></a>Owner

An owner is a user who has additional privileges for managing a data asset in Azure Data Catalog. Users can take ownership of registered data assets, and owners can add other users as co-owners. For more information see  [How to manage data assets](data-catalog-how-to-manage.md)  
> [AZURE.NOTE] Ownership and management are available only in the Standard Edition of Azure Data Catalog.

## <a name="registration"></a>Registration

Registration is the act of extracting data asset metadata from a data source and copying it to the Azure Data Catalog service. Data assets that have been registered can then be annotated and discovered.

## <a name="see-also"></a>See also

- [What is Azure Data Catalog?](data-catalog-what-is-data-catalog.md) - This article provides an overview of the Azure Data Catalog service, the value it provides, and the scenarios it supports.

- [Get started with Azure Data Catalog](data-catalog-get-started.md) - This article provides an end-to-end tutorial that shows you how to use Azure Data Catalog for data source discovery.  



<!--HONumber=Oct16_HO2-->


