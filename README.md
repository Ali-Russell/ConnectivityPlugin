## Connectivity Plugin for Xamarin and Windows

Simple cross platform plugin to check connection status of mobile device, gather connection type, bandwidths, and more.

### Setup
* Available on NuGet: http://www.nuget.org/packages/Xam.Plugin.Connectivity [![NuGet](https://img.shields.io/nuget/v/Xam.Plugin.Connectivity.svg?label=NuGet)](https://www.nuget.org/packages/Xam.Plugin.Connectivity/)
* Install into your PCL project and Client projects.

Build Status: 
* [![Build status](https://ci.appveyor.com/api/projects/status/k6l4x6ovp5ysfbar?svg=true)](https://ci.appveyor.com/project/JamesMontemagno/connectivityplugin)
* CI NuGet Feed: https://ci.appveyor.com/nuget/connectivityplugin

**Platform Support**

|Platform|Supported|Version|
| ------------------- | :-----------: | :------------------: |
|Xamarin.iOS|Yes|iOS 6+|
|Xamarin.Android|Yes|API 10+|
|Windows Phone Silverlight|Yes|8.0+|
|Windows Phone RT|Yes|8.1+|
|Windows Store RT|Yes|8.1+|
|Windows 10 UWP|Yes|10+|
|Xamarin.Mac|Yes|All|


### API Usage

Call **CrossConnectivity.Current** from any project or PCL to gain access to APIs.


**IsConnected**
```csharp
/// <summary>
/// Gets if there is an active internet connection
/// </summary>
bool IsConnected { get; }
```

**ConnectionTypes**
```csharp
/// <summary>
/// Gets the list of all active connection types.
/// </summary>
IEnumerable<ConnectionType> ConnectionTypes { get; }
```

**Bandwidths**
```csharp
/// <summary>
/// Retrieves a list of available bandwidths for the platform.
/// Only active connections.
/// </summary>
IEnumerable<UInt64> Bandwidths { get; }
```

#### Pinging Hosts

**IsReachable**
```csharp
/// <summary>
/// Tests if a host name is pingable
/// </summary>
/// <param name="host">The host name can either be a machine name, such as "java.sun.com", or a textual representation of its IP address (127.0.0.1)</param>
/// <param name="msTimeout">Timeout in milliseconds</param>
/// <returns></returns>
Task<bool> IsReachable(string host, int msTimeout = 5000);
```

**IsRemoteReachable**
```csharp
/// <summary>
/// Tests if a remote host name is reachable (no http:// or www.)
/// </summary>
/// <param name="host">Host name can be a remote IP or URL of website</param>
/// <param name="port">Port to attempt to check is reachable.</param>
/// <param name="msTimeout">Timeout in milliseconds.</param>
/// <returns></returns>
Task<bool> IsRemoteReachable(string host, int port = 80, int msTimeout = 5000);
```

#### Changes in Connectivity
When any network connectiivty is gained, changed, or loss you can register for an event to fire:
```csharp
/// <summary>
/// Event handler when connection changes
/// </summary>
event ConnectivityChangedEventHandler ConnectivityChanged; 
```

You will get a ConnectivityChangeEventArgs with the status if you are connected or not:
```csharp
public class ConnectivityChangedEventArgs : EventArgs
{
  public bool IsConnected { get; set; }
}

public delegate void ConnectivityChangedEventHandler(object sender, ConnectivityChangedEventArgs e);
```

Usage sample from Xamarin.Forms:
```csharp
CrossConnectivity.Current.ConnectivityChanged += (sender, args) =>
  {
    page.DisplayAlert("Connectivity Changed", "IsConnected: " + args.IsConnected.ToString(), "OK");
  };
```


### **IMPORTANT**
### Android:
The ACCESS_NETWORK_STATE and ACCESS_WIFI_STATE permissions are required and will be automatically added to your Android Manifest.

By adding these permissions [Google Play will automatically filter out devices](http://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features) without specific hardware. You can get around this by adding the following to your AssemblyInfo.cs file in your Android project:

```
[assembly: UsesFeature("android.hardware.wifi", Required = false)]
```

### iOS:
Bandwidths are not supported and will always return an empty list.

#### Removal of reachabilityForLocalWiFi

Older versions of this sample included the method reachabilityForLocalWiFi. As originally designed, this method allowed apps using Bonjour to check the status of "local only" Wi-Fi (Wi-Fi without a connection to the larger internet) to determine whether or not they should advertise or browse. 
 
However, the additional peer-to-peer APIs that have since been added to iOS and OS X have rendered it largely obsolete.  Because of the narrow use case for this API and the large potential for misuse, reachabilityForLocalWiFi has been removed from Reachability.

Apps that have a specific requirement can use reachabilityWithAddress to monitor IN_LINKLOCALNETNUM (that is, 169.254.0.0).  
 
Note: ONLY apps that have a specific requirement should be monitoring IN_LINKLOCALNETNUM.  For the overwhelming majority of apps, monitoring this address is unnecessary and potentially harmful.

### Windows 8.1 & Windows Phone 8.1 RT:
RT apps can not perform loopback, so you can not use IsReachable to query the states of a local IP.

####Permissions to think about:
The Private Networks (Client & Server) capability is represented by the Capability name = "privateNetworkClientServer" tag in the app manifest. 
The Internet (Client & Server) capability is represented by the Capability name = "internetClientServer" tag in the app manifest.

