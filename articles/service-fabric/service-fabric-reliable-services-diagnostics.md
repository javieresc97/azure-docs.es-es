---
title: "Diagnóstico de Reliable Services con estado | Microsoft Docs"
description: "Funcionalidad de diagnóstico para Reliable Services con estado"
services: service-fabric
documentationcenter: .net
author: AlanWarwick
manager: timlt
editor: 
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2017
ms.author: alanwar
translationtype: Human Translation
ms.sourcegitcommit: 3ed67788fbbe0c4fb820cfd1525d8c9ee5154446
ms.openlocfilehash: 5da5707900b35be69018ba4e9c8efb05d8a74a9d


---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funcionalidad de diagnóstico para Reliable Services con estado
La clase StatefulServiceBase de Reliable Services con estado emite eventos [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) que pueden usarse para depurar el servicio, ofrecer información acerca de cómo funciona el tiempo de ejecución y ayudar a solucionar problemas.

## <a name="eventsource-events"></a>Eventos EventSource
El nombre EventSource de la clase StatefulServiceBase de Reliable Services con estado es "Microsoft-ServiceFabric-Services". Los eventos de este origen de eventos aparecen en la ventana [Eventos de diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) cuando se está [depurando el servicio en Visual Studio](service-fabric-debugging-your-application.md).

Otros ejemplos de herramientas y tecnologías que ayudan a recopilar o ver eventos EventSource son [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnósticos de Microsoft Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) y [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventos
| Nombre del evento | Id. de evento | Nivel | Descripción del evento |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informativo |Emitido cuando se inicia la tarea RunAsync del servicio |
| StatefulRunAsyncCancellation |2 |Informativo |Emitido cuando se cancela la tarea RunAsync del servicio |
| StatefulRunAsyncCompletion |3 |Informativo |Emitido cuando se completa la tarea RunAsync del servicio |
| StatefulRunAsyncSlowCancellation |4 |Warning (Advertencia) |Emitido cuando la tarea RunAsync del servicio tarda demasiado tiempo en completar la cancelación |
| StatefulRunAsyncFailure |5 |Error |Emitido cuando la tarea RunAsync del servicio genera una excepción |

## <a name="interpret-events"></a>Interpretar eventos
Los eventos StatefulRunAsyncInvocation, StatefulRunAsyncCompletion y StatefulRunAsyncCancellation son útiles para que el escritor del servicio comprenda el ciclo de vida de un servicio, así como el momento en que se inicia, se cancela o completa un servicio. Esto puede ser útil al depurar problemas de servicio o al comprender el ciclo de vida de servicio.

Los escritores del servicio deben prestar especial atención a los eventos StatefulRunAsyncSlowCancellation y StatefulRunAsyncFailure, ya que indican problemas con el servicio.

StatefulRunAsyncFailure se genera cada vez que la tarea RunAsync() del servicio produce una excepción. Normalmente, una excepción generada indica un error en el servicio. Además, la excepción hace que se produzca un error en el servicio, por lo que se mueve a otro nodo. Esta operación puede ser cara y puede retrasar las solicitudes entrantes mientras se mueve el servicio. Los escritores del servicio deben determinar la causa de la excepción y si es posible mitigarla.

StatefulRunAsyncSlowCancellation se genera cada vez que una solicitud de cancelación de la tarea RunAsync tarda más de cuatro segundos. Cuando un servicio tarda demasiado en completar la cancelación, afecta a la capacidad del servicio de reiniciarse rápidamente en otro nodo. Esto puede afectar a la disponibilidad general del servicio.

## <a name="next-steps"></a>Pasos siguientes
* [Proveedores de EventSource en PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)



<!--HONumber=Jan17_HO1-->


