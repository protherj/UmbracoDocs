---
icon: square-exclamation
description: Learn how to implement an IP to location provider.
---

# Capture location data

The localization information is displayed under the **Location** tab in the **Analytics** section of Umbraco Engage dashboard.

Umbraco Engage Analytics natively supports storing and reporting localization information for incoming traffic. Localization refers to identifying the (physical) origin of an incoming request. Web requests from a visitor's browser do not contain location information, so you must provide an implementation for this.

Most localization services, such as Maxmind, use IP addresses to perform a (rough) lookup. The information is compiled into a database where lookups can be performed. However IP addresses do not contain any information regarding their (physical) origin, rather they only identify a device on the internet. Localization information for any given IP address is tracked manually and can change overtime. We recommend either using an external service or acquiring a copy of a GeoIP database for localization lookup purposes.

## Implementation

Once you have a service that can provide localization information, integrating it into Umbraco Engage is straightforward.

For this purpose, implement the interface **Umbraco.Engage.Business.Analytics.Processing.Extractors.IRawPageviewLocationExtractor**. This interface allows the localization information for a pageview, defined as a single visitor's visit to a specific point in time. The pageview contains the property **IpAddress** which can be used for Geo IP lookup.

First, define a class that implements **ILocation**, to hold the localization information that will be returned through the interface in our implementation:

{% code overflow="wrap" lineNumbers="true" %}

```cs
using Umbraco.Engage.Business.Analytics.Processed;
public class GeoIpLocation : ILocation {
    public string Country { get; set; }
    public string County { get; set; }
    public string Province { get; set; }
    public string City { get; set; }
}
```

{% endcode %}

Next, implement the location extractor to read and validate the incoming IP address and filter out local IP addresses with the native **IsLoopback** method. Then, call your Geo IP localization implementation:

{% code overflow="wrap" lineNumbers="true" %}

```cs
using Umbraco.Engage.Business.Analytics.Processing.Extractors;
public class MyCustomLocationExtractor : IRawPageviewLocationExtractor
{
    public ILocation Extract(IRawPageview rawPageview)
    {
        if (!IPAddress.TryParse(rawPageview?.IpAddress, out var ipAddress) || IPAddress.IsLoopback(ipAddress)) return null;
    
        string country, county, province, city;
    
        //...
        // Perform your own GEO IP lookup here
        // ...
    
        var location = new GeoIpLocation
        {
            Country = country,
            County = county,
            Province = province,
            City = city
        };
    
        return location;
    }
}
```

{% endcode %}

Lastly, let the IoC container know to use your implementation for the **IRawPageviewLocationExtractor**. Umbraco Engage has a default implementation of this service, which only returns null. This default service is registered using Umbraco's **RegisterUnique** method. To override this service, call RegisterUnique **after** the Umbraco Engage dependencies have been initialized, which is **after** the **UmbracoEngageApplicationComposer**:

{% code overflow="wrap" lineNumbers="true" %}

```cs
using Umbraco.Engage.Business.Analytics.Processing.Extractors;
using Umbraco.Engage.Common.Composing;
using Umbraco.Core;
using Umbraco.Core.Composing;
    
[ComposeAfter(typeof(UmbracoEngageApplicationComposer))]
public class UmbracoEngageComposer: IComposer
{
    public void Compose(IUmbracoBuilder builder)
    {
        builder.services.AddUnique<IRawPageviewLocationExtractor, MyCustomLocationExtractor>();
    }
}
```

{% endcode %}

After implementing this, Umbraco Engage will begin collecting and displaying localization information for pageviews. This can be viewed in the Analytics section of the Umbraco Engage dashboard.

{% hint style="info" %}
If the custom implementation returns **null**, **ILocation** will display as "Unknown".
{% endhint %}

{% hint style="info" %}
The LocationExtractor only processes new pageviews and will not apply retroactively to historical data.
{% endhint %}

If the pageviews contain location information, the table with countries is displayed:

<figure><img src="../../.gitbook/assets/image (1) (4).png" alt="Location table - missing data error"><figcaption><p>Location table with data</p></figcaption></figure>

From the country, you can drill down to the city. This will then filter the displayed graph and table data to only display session and pageview information for the selected country. Even though Umbraco Engage does support the storage for county and province, the UI only supports displaying data by country and city.
