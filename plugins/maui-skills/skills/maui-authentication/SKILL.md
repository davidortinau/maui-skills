---
name: maui-authentication
description: >
  Add web authentication to .NET MAUI apps using WebAuthenticator, OAuth 2.0,
  social login providers (Google, Apple, Microsoft), token handling, callback URI
  configuration, and secure token storage with SecureStorage.
---

# .NET MAUI Web Authentication

Use `WebAuthenticator` to launch browser-based auth flows (OAuth 2.0, OpenID Connect, social login) and receive callback URIs back into the app.

## Core API

```csharp
try
{
    var result = await WebAuthenticator.Default.AuthenticateAsync(
        new WebAuthenticatorOptions
        {
            Url = new Uri("https://your-server.com/auth/login"),
            CallbackUrl = new Uri("myapp://callback"),
            PrefersEphemeralWebBrowserSession = true // iOS 13+: private session, no shared cookies
        });

    string accessToken = result.AccessToken;
    string refreshToken = result.Properties["refresh_token"];
}
catch (TaskCanceledException)
{
    // User cancelled the auth flow — do not treat as an error
}
```

- `Url` — the authorization endpoint (your server or identity provider).
- `CallbackUrl` — the URI scheme your app is registered to handle.
- `PrefersEphemeralWebBrowserSession` — when `true` (iOS 13+), uses a private browser session that does not share cookies or data with Safari. Useful for forcing login prompts.

## Platform Setup

### Android

#### 1. Callback Activity

Create a subclass of `WebAuthenticatorCallbackActivity` with an `IntentFilter` matching your callback URI scheme:

```csharp
using Android.App;
using Android.Content.PM;

namespace MyApp.Platforms.Android;

[Activity(NoHistory = true, LaunchMode = LaunchMode.SingleTop, Exported = true)]
[IntentFilter(
    new[] { Android.Content.Intent.ActionView },
    Categories = new[] { Android.Content.Intent.CategoryDefault, Android.Content.Intent.CategoryBrowsable },
    DataScheme = "myapp",
    DataHost = "callback")]
public class WebAuthenticationCallbackActivity : Microsoft.Maui.Authentication.WebAuthenticatorCallbackActivity
{
}
```

#### 2. Package Visibility (Android 11+)

Add a `<queries>` element to `AndroidManifest.xml` so the app can resolve browser intents:

```xml
<manifest>
  <queries>
    <intent>
      <action android:name="android.support.customtabs.action.CustomTabsService" />
    </intent>
  </queries>
</manifest>
```

### iOS / Mac Catalyst

Register the callback URI scheme in `Info.plist`:

```xml
<key>CFBundleURLTypes</key>
<array>
  <dict>
    <key>CFBundleURLName</key>
    <string>myapp</string>
    <key>CFBundleURLSchemes</key>
    <array>
      <string>myapp</string>
    </array>
  </dict>
</array>
```

No additional code is needed — MAUI handles the callback automatically on Apple platforms.

### Windows

Register the protocol in `Package.appxmanifest`:

```xml
<Extensions>
  <uap:Extension Category="windows.protocol">
    <uap:Protocol Name="myapp">
      <uap:DisplayName>My App Auth</uap:DisplayName>
    </uap:Protocol>
  </uap:Extension>
</Extensions>
```

> [!WARNING]
> Windows WebAuthenticator is currently broken. See [dotnet/maui#2702](https://github.com/dotnet/maui/issues/2702). Consider using MSAL or a WinUI-specific workaround for Windows auth flows.

## Apple Sign In

Use `AppleSignInAuthenticator` on iOS 13+ for native Sign in with Apple:

```csharp
var result = await AppleSignInAuthenticator.Default.AuthenticateAsync(
    new AppleSignInAuthenticator.Options
    {
        IncludeFullNameScope = true,
        IncludeEmailScope = true
    });

string idToken = result.IdToken;
string name = result.Properties["name"];
```

Apple only returns the user's name and email on **first** sign-in. Cache them immediately.

## Security: Use a Server Backend

> [!IMPORTANT]
> Never embed client secrets, API keys, or signing keys in a mobile app binary. They can be extracted trivially.

The recommended pattern:

1. App calls `WebAuthenticator` pointing to **your server** endpoint.
2. Server initiates the OAuth flow with the identity provider (holds the client secret).
3. Provider redirects back to your server with an auth code.
4. Server exchanges the code for tokens and returns them to the app via the callback URI.

## Token Persistence with SecureStorage

Store tokens securely using `SecureStorage` (Keychain on iOS, Keystore on Android):

```csharp
// Save
await SecureStorage.Default.SetAsync("access_token", accessToken);
await SecureStorage.Default.SetAsync("refresh_token", refreshToken);

// Retrieve
string token = await SecureStorage.Default.GetAsync("access_token");

// Clear on logout
SecureStorage.Default.RemoveAll();
```

## DI-Friendly Auth Service

Wrap authentication in an injectable service for testability:

```csharp
public interface IAuthService
{
    Task<AuthResult> LoginAsync(CancellationToken ct = default);
    Task LogoutAsync();
    Task<string?> GetAccessTokenAsync();
}

public record AuthResult(bool Success, string? ErrorMessage = null);

public class WebAuthService : IAuthService
{
    private const string AuthUrl = "https://your-server.com/auth/login";
    private const string CallbackUrl = "myapp://callback";

    public async Task<AuthResult> LoginAsync(CancellationToken ct = default)
    {
        try
        {
            var result = await WebAuthenticator.Default.AuthenticateAsync(
                new WebAuthenticatorOptions
                {
                    Url = new Uri(AuthUrl),
                    CallbackUrl = new Uri(CallbackUrl),
                    PrefersEphemeralWebBrowserSession = true
                });

            await SecureStorage.Default.SetAsync("access_token", result.AccessToken);
            return new AuthResult(true);
        }
        catch (TaskCanceledException)
        {
            return new AuthResult(false, "Login cancelled.");
        }
    }

    public Task LogoutAsync()
    {
        SecureStorage.Default.RemoveAll();
        return Task.CompletedTask;
    }

    public Task<string?> GetAccessTokenAsync()
        => SecureStorage.Default.GetAsync("access_token");
}
```

Register in `MauiProgram.cs`:

```csharp
builder.Services.AddSingleton<IAuthService, WebAuthService>();
```

## Checklist

- [ ] Callback URI scheme matches across all platform configs and `CallbackUrl`.
- [ ] Android has `WebAuthenticatorCallbackActivity` with correct `IntentFilter`.
- [ ] Android 11+ has `<queries>` for Custom Tabs in the manifest.
- [ ] iOS/Mac Catalyst has `CFBundleURLTypes` in `Info.plist`.
- [ ] Client secrets are on the server, not in the app.
- [ ] Tokens stored with `SecureStorage`, cleared on logout.
- [ ] `TaskCanceledException` handled gracefully in UI.
