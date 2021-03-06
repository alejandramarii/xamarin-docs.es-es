---
title: Estilos dinámicos en Xamarin.Forms
description: En este artículo se explica cómo una aplicación de Xamarin.Forms puede responder a los cambios de estilo dinámicamente en tiempo de ejecución mediante el uso de recursos dinámicos.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 260c215df52eb31139998438cc0eda10a887be65
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55291965"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Estilos dinámicos en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)

_Los estilos no responder a los cambios de propiedad y permanecen sin cambios para la duración de una aplicación. Por ejemplo, después de asignar un estilo a un elemento visual, si se modifica una de las instancias de establecedor, quitar, o una nueva instancia de establecedor agregado, los cambios no se aplicará al elemento visual. Sin embargo, las aplicaciones pueden responder a cambios de estilo dinámicamente en tiempo de ejecución mediante el uso de recursos dinámicos._

El `DynamicResource` extensión de marcado es similar a la `StaticResource` extensión de marcado en ambos utilizan una clave de diccionario para capturar un valor desde un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Sin embargo, mientras el `StaticResource` realiza una búsqueda de diccionario único, el `DynamicResource` mantiene un vínculo a la clave del diccionario. Por lo tanto, si se reemplaza la entrada del diccionario asociada con la clave, el cambio se aplica al elemento visual. Esto permite que los cambios de estilo en tiempo de ejecución que se realiza en una aplicación.

En el ejemplo de código siguiente se muestra *dinámica* estilos en una página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle"
                   TargetType="SearchBar"
                   BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle"
                   TargetType="SearchBar">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Placeholder="These SearchBar controls"
                       Style="{DynamicResource searchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) instancias uso el `DynamicResource` extensión de marcado para hacer referencia a un [ `Style` ](xref:Xamarin.Forms.Style) denominado `searchBarStyle`, que no está definido en el XAML. Sin embargo, dado que el [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propiedades de la `SearchBar` instancias se establecen mediante un `DynamicResource`, falta la clave del diccionario no se produce una excepción.

En su lugar, en el archivo de código subyacente, el constructor crea un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) entrada con la clave `searchBarStyle`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public partial class DynamicStylesPage : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPage ()
    {
        InitializeComponent ();
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
    }

    void OnButtonClicked (object sender, EventArgs e)
    {
        if (originalStyle) {
            Resources ["searchBarStyle"] = Resources ["greenSearchBarStyle"];
            originalStyle = false;
        } else {
            Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
            originalStyle = true;
        }
    }
}
```

Cuando el `OnButtonClicked` se ejecuta el controlador de eventos, `searchBarStyle` cambiará entre `blueSearchBarStyle` y `greenSearchBarStyle`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](dynamic-images/dynamic-style-blue.png "Azul estilo dinámico ejemplo")](dynamic-images/dynamic-style-blue-large.png#lightbox "azul estilo dinámico ejemplo")
[![](dynamic-images/dynamic-style-green.png "verde ejemplo estilo dinámico") ] (dynamic-images/dynamic-style-green-large.png#lightbox "Verde ejemplo estilo dinámico")

En el ejemplo de código siguiente se muestra la página equivalente en C#:

```csharp
public class DynamicStylesPageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        ...
        var searchBar1 = new SearchBar { Placeholder = "These SearchBar controls" };
        searchBar1.SetDynamicResource (VisualElement.StyleProperty, "searchBarStyle");
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button    }
        };
    }
    ...
}
```

En C#, la [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) instancias uso el [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) método para hacer referencia a `searchBarStyle`. El `OnButtonClicked` es idéntico del ejemplo XAML y cuando se ejecuta, el código del controlador de eventos `searchBarStyle` cambiará entre `blueSearchBarStyle` y `greenSearchBarStyle`.

## <a name="dynamic-style-inheritance"></a>Herencia de estilo dinámico

Derivar un estilo de un estilo dinámico no pueden lograrse mediante el [ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) propiedad. En su lugar, el [ `Style` ](xref:Xamarin.Forms.Style) clase incluye el [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propiedad, que se puede establecer en una clave de diccionario cuyo valor puede cambiar dinámicamente.

En el ejemplo de código siguiente se muestra *dinámica* herencia de estilo en una página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle" TargetType="SearchBar" BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle" TargetType="SearchBar">
              ...
            </Style>
            <Style x:Key="tealSearchBarStyle" TargetType="SearchBar" BaseResourceKey="searchBarStyle">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Text="These SearchBar controls" Style="{StaticResource tealSearchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) instancias uso el `StaticResource` extensión de marcado para hacer referencia a un [ `Style` ](xref:Xamarin.Forms.Style) denominado `tealSearchBarStyle`. Esto `Style` establece algunas propiedades adicionales y utiliza el [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propiedad para hacer referencia a `searchBarStyle`. El `DynamicResource` extensión de marcado no es necesaria porque `tealSearchBarStyle` no cambiará, excepto para el `Style` deriva de. Por lo tanto, `tealSearchBarStyle` mantiene un vínculo a `searchBarStyle` y se modifica cuando cambia el estilo de base.

En el archivo de código subyacente, el constructor crea un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) entrada con la clave `searchBarStyle`según el ejemplo anterior que se muestra estilos dinámicos. Cuando el `OnButtonClicked` se ejecuta el controlador de eventos, `searchBarStyle` cambiará entre `blueSearchBarStyle` y `greenSearchBarStyle`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](dynamic-images/dynamic-style-inheritance-blue.png "Ejemplo de herencia de estilo dinámico de azul")](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox "azul de ejemplo de herencia de estilo dinámico")
[![](dynamic-images/dynamic-style-inheritance-green.png "verdes estilo dinámico Ejemplo de herencia")](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox "verde de ejemplo de herencia de estilo dinámico")

En el ejemplo de código siguiente se muestra la página equivalente en C#:

```csharp
public class DynamicStylesInheritancePageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesInheritancePageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var tealSearchBarStyle = new Style (typeof(SearchBar)) {
            BaseResourceKey = "searchBarStyle",
            ...
        };
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = {
                new SearchBar { Text = "These SearchBar controls", Style = tealSearchBarStyle },
                ...
            }
        };
    }
    ...
}
```

El `tealSearchBarStyle` se asigna directamente a la [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propiedad de la [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) instancias. Esto `Style` establece algunas propiedades adicionales y utiliza el [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propiedad para hacer referencia a `searchBarStyle`. El [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) método no es necesario aquí porque `tealSearchBarStyle` no cambiará, excepto para el `Style` deriva de. Por lo tanto, `tealSearchBarStyle` mantiene un vínculo a `searchBarStyle` y se modifica cuando cambia el estilo de base.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos dinámicos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [Trabajar con estilos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Establecedor](xref:Xamarin.Forms.Setter)
