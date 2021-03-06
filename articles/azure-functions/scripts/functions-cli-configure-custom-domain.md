---
title: "Ejemplo de script de la CLI de Azure: asignación de un dominio personalizado a una aplicación de función | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: asignación de un dominio personalizado a una aplicación de función en Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/01/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: cd7ab0bbe92fa32d23a841b0b17bee8510f6b406
ms.contentlocale: es-es
ms.lasthandoff: 05/15/2017

---
# <a name="map-a-custom-domain-to-a-function-app"></a>Asignación de un dominio personalizado a una aplicación de función

Este script de ejemplo crea una aplicación de función con recursos relacionados y le asigna `www.<yourdomain>`. Para asignarla a un dominio personalizado, la aplicación de función se debe crear en un plan de App Service y no en un plan de consumo. Azure Functions solo admite la asignación de un dominio personalizado mediante un registro D.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Asignación de un dominio personalizado a una aplicación de función")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Crea una cuenta de almacenamiento necesaria para la aplicación de función. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crea un plan de App Service necesario para asignar un dominio personalizado. |
| [az functionapp create]() | Crea una aplicación de función. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Asigna un dominio personalizado a una aplicación de función. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de CLI para Functions en la [documentación de Azure Functions]().

