---
title: "Más información sobre Azure Application Insights | Microsoft Docs"
description: "Aquí se presentan algunas características que puede explorar una vez que sepa utilizar Application Insights."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f9d586a140b27f672f8cff463ba0607e2bd844f
ms.openlocfilehash: 6f2a184242f3f69bdc4a15ac02c095a45c723565
ms.contentlocale: es-es
ms.lasthandoff: 02/08/2017


---
# <a name="more-telemetry-from-application-insights"></a>Más telemetría desde Application Insights
Una vez que haya [agregado Application Insights a su código de ASP.NET](app-insights-asp-net.md), puede realizar varias acciones para obtener más datos de telemetría. 

| Acción | Lo que obtiene|
|---|---|
|(Servidores IIS) [Instale Monitor de estado](http://go.microsoft.com/fwlink/?LinkId=506648) en cada máquina del servidor.<br/>(Aplicaciones web de Azure) En el panel de control de Azure de la aplicación web, abra la hoja Application Insights.| [**Contadores de rendimiento**](app-insights-performance-counters.md).<br/>[**Excepciones**](app-insights-asp-net-exceptions.md): seguimiento de la pila detallado.<br/>[**Dependencias**](app-insights-asp-net-dependencies.md).|
|[Agregue el fragmento de código de JavaScript en las páginas web](app-insights-javascript.md).|[Rendimiento de página](app-insights-web-track-usage.md), excepciones de explorador y rendimiento de AJAX. Telemetría del lado cliente personalizada.|
|[Cree pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md).|Obtención de alertas si el sitio deja de estar disponible.|
|[Asegúrese de que se genera buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) mediante MSBuild.|[Generación de anotaciones en gráficos de métricas](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/).
|[Cree eventos y métricas personalizados](app-insights-api-custom-events-metrics.md).|Recuento de métricas y eventos empresariales, seguimiento de uso detallado y mucho más.|
|[Genere un perfil del sitio activo.](https://aka.ms/AIProfilerPreview)|Intervalos de función detallados de la aplicación web activa.|







