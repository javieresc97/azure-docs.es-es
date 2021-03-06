---
title: "Creación de su primera función con la CLI de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo crear su primera función de Azure para su ejecución sin servidor mediante la CLI de Azure."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 05/02/2017
ms.topic: hero-article
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: erikre
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 3dc0e1b26c95ac6583dd3b1068b36deb54f7ac5a
ms.contentlocale: es-es
ms.lasthandoff: 05/18/2017

---

# <a name="create-your-first-function-using-the-azure-cli"></a>Creación de su primera función con la CLI de Azure

Este tutorial de inicio rápido le guía en el uso de Azure Functions para crear su primera función. Use la CLI de Azure para crear una Function App, que es la infraestructura sin servidor que hospeda su función. El propio código de la función se implementa desde un repositorio de ejemplo de GitHub.    

Puede seguir los pasos siguientes con un equipo Mac, Windows o Linux. 

## <a name="prerequisites"></a>Requisitos previos 

Antes de ejecutar este ejemplo, debe tener lo siguiente:

+ Una cuenta de [GitHub](https://github.com) activa. 
+ [La CLI de Azure instalada](https://docs.microsoft.com/cli/azure/install-azure-cli). 
+ Una suscripción de Azure activa.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla. 

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran recursos de Azure como instancias de Function App, bases de datos y cuentas de almacenamiento.

En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup`:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
## <a name="create-an-azure-storage-account"></a>Creación de una cuenta de almacenamiento de Azure

Functions usa una cuenta de Azure Storage para mantener el estado y otra información sobre sus funciones. Cree una cuenta de almacenamiento en el grupo de recursos que ha creado con el comando [az storage account create](/cli/azure/storage/account#create).

En el siguiente comando, sustituya su propio nombre de la cuenta de almacenamiento único de manera global donde vea el marcador de posición `<storage_name>`. Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden contener números y letras minúsculas.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

Después de que se haya creado la cuenta de almacenamiento, la CLI de Azure muestra información similar al ejemplo siguiente:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```

## <a name="create-a-function-app"></a>Creación de una aplicación de función

Debe tener una Function App para hospedar la ejecución de las funciones. La Function App proporciona un entorno para la ejecución sin servidor de su código de función. Le permite agrupar funciones como una unidad lógica para facilitar la administración, la implementación y el uso compartido de recursos. Cree una Function App con el comando [az functionapp create](/cli/azure/functionapp#create). 

En el siguiente comando, sustituya su propio nombre de la Function App único donde vea el marcador de posición `<app_name>` y el nombre de la cuenta de almacenamiento para `<storage_name>`. El `<app_name>` se usa como el dominio DNS predeterminado para la Function App y, por ello, el nombre debe ser único en todas las aplicaciones de Azure. 

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup --consumption-plan-location westeurope
```
De manera predeterminada, una Function App se crea con el plan de hospedaje Consumo, lo que significa que los recursos se agregan de manera dinámica según sea necesario mediante sus funciones y solo paga cuando las funciones se ejecutan. Para obtener más información, vea [Choose the correct hosting plan](functions-scale.md) (Elegir el plan de hospedaje correcto). 

Después de que se haya creado la Function App, la CLI de Azure muestra información similar al siguiente ejemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Ahora que tiene una Function App, puede implementar el código de función actual del repositorio de ejemplo de GitHub.

## <a name="deploy-your-function-code"></a>Implementar el código de función  

Existen varias maneras de crear el código de función en su nueva Function App. Este tema se conecta a un repositorio de ejemplo de GitHub. Como antes, en el código siguiente reemplace el marcador de posición `<app_name>` por el nombre de la Function App que ha creado. 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --repo-url https://github.com/Azure-Samples/functions-quickstart --branch master --manual-integration
```
Después de que se haya establecido el origen de implementación, la CLI de Azure muestra información similar al ejemplo siguiente (se han quitado los valores NULL para tener una mayor legibilidad):

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "location": "West Europe",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

## <a name="test-the-function"></a>Prueba de la función

Use cURL para probar la función implementada en un equipo Mac o Linux o use Bash en Windows. Ejecute el siguiente comando cURL, reemplazando el marcador de posición `<app_name>` por el nombre de su Function App. Anexe la cadena de consulta `&name=<yourname>` a la dirección URL.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![La respuesta de función que se muestra en un explorador.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-curl.png)  

Si no tiene cURL disponible en su línea de comandos, simplemente escriba la misma dirección URL en la dirección de su explorador web. De nuevo, reemplace el marcador de posición `<app_name>` por el nombre de su Function App, y anexe la cadena de consulta `&name=<yourname>` a la dirección URL y ejecute la solicitud. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![La respuesta de función que se muestra en un explorador.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)  

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de inicio rápido de esta colección se basan en los valores de esta. Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido o tutoriales, no elimine los recursos creados en esta guía de inicio rápido. Si no va a continuar, use el siguiente comando para eliminar todos los recursos creados mediante esta guía de inicio rápido:

```azurecli-interactive
az group delete --name myResourceGroup
```
Cuando se le solicite, escriba `y`.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

