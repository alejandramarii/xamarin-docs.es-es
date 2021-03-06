---
title: Alto de la barra de NavigationPage en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el Android específicos de la plataforma que establece el alto de la barra de navegación de una NavigationPage.
ms.prod: xamarin
ms.assetid: C8A73B64-FE70-408A-A72E-8AF147F0C52C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: e803f138c0be85517bbafbfa6e9d39f39abc7e1d
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209160"
---
# <a name="navigationpage-bar-height-on-android"></a>Alto de la barra de NavigationPage en Android

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este Android específicos de plataforma establece el alto de la barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Se consume en XAML estableciendo el [ `NavigationPage.BarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) propiedad enlazable en un valor entero:

```xaml
<NavigationPage ...
                xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
                android:NavigationPage.BarHeight="450">
    ...
</NavigationPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

public class AndroidNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public AndroidNavigationPageCS()
    {
        On<Android>().SetBarHeight(450);
    }
}
```

El `NavigationPage.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en la compatibilidad de aplicaciones Android. El [ `NavigationPage.SetBarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.SetBarHeight(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.NavigationPage},System.Int32)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) espacio de nombres, se usa para establecer el alto de la barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Además, el [ `NavigationPage.GetBarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.GetBarHeight(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.NavigationPage})) método puede utilizarse para devolver el alto de la barra de navegación en el `NavigationPage`.

El resultado es que el alto de la barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) se pueden establecer:

![](navigationpage-bar-height-images/navigationpage-barheight.png "NavigationPage alto de la barra de navegación")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
