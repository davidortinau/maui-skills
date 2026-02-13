# .NET MAUI Skills

A collection of 33 skills for .NET MAUI development, designed for use with GitHub Copilot CLI and Claude Code. Each skill provides focused, expert-level guidance on a specific area of .NET MAUI app development.

Skills are loaded on-demand when your prompt matches the skill's topic, injecting detailed guidance, code examples, and platform-specific notes into the AI's context.

## Available Skills

| Skill | Description |
|-------|-------------|
| [maui-accessibility](plugins/maui-skills/skills/maui-accessibility/) | Guide for making .NET MAUI apps accessible — screen reader support via SemanticProperties, heading levels, AutomationProperties visibility control, programmatic focus and announcements, and platform-specific gotchas for TalkBack, VoiceOver, and Narrator. |
| [maui-animations](plugins/maui-skills/skills/maui-animations/) | .NET MAUI view animations, custom animations, easing functions, rotation, scale, translation, and fade effects. |
| [maui-app-icons-splash](plugins/maui-skills/skills/maui-app-icons-splash/) | .NET MAUI app icon configuration, splash screen setup, SVG to PNG conversion at build time, composed/adaptive icons, and platform-specific icon and splash screen requirements for Android, iOS, Mac Catalyst, and Windows. |
| [maui-app-lifecycle](plugins/maui-skills/skills/maui-app-lifecycle/) | .NET MAUI app lifecycle guidance covering the four app states (not running, running, deactivated, stopped), cross-platform Window lifecycle events, backgrounding and resume behaviour, platform-specific lifecycle mapping for Android and iOS/Mac Catalyst, and state-preservation patterns. |
| [maui-aspire](plugins/maui-skills/skills/maui-aspire/) | Guide for .NET MAUI apps consuming .NET Aspire-hosted backend services. Covers AppHost configuration, service discovery for mobile clients, HttpClient DI setup, Entra ID authentication with MSAL.NET for calling protected APIs, development workflow, and platform-specific networking. |
| [maui-authentication](plugins/maui-skills/skills/maui-authentication/) | Add authentication to .NET MAUI apps. Covers WebAuthenticator for generic OAuth 2.0 / social login, and MSAL.NET for Microsoft Entra ID with broker support, token caching, Conditional Access, platform-specific setup, and Blazor Hybrid integration. |
| [maui-collectionview](plugins/maui-skills/skills/maui-collectionview/) | Guidance for implementing CollectionView in .NET MAUI apps — data display, layouts (list & grid), selection, grouping, scrolling, empty views, templates, incremental loading, swipe actions, and pull-to-refresh. |
| [maui-custom-handlers](plugins/maui-skills/skills/maui-custom-handlers/) | Guide for creating custom .NET MAUI handlers, customizing existing handlers with property mappers, and implementing platform-specific native views. Covers PrependToMapping/ModifyMapping/AppendToMapping, PropertyMapper, CommandMapper, partial handler classes, and handler registration. |
| [maui-data-binding](plugins/maui-skills/skills/maui-data-binding/) | Guidance for .NET MAUI XAML data bindings, compiled bindings, value converters, binding modes, multi-binding, relative bindings, and MVVM best practices. |
| [maui-deep-linking](plugins/maui-skills/skills/maui-deep-linking/) | Guide for implementing deep linking in .NET MAUI apps. Covers Android App Links with intent filters, Digital Asset Links, and AutoVerify; iOS Universal Links with Associated Domains entitlements and Apple App Site Association files; custom URI schemes; and domain verification for both platforms. |
| [maui-dependency-injection](plugins/maui-skills/skills/maui-dependency-injection/) | Guidance for dependency injection in .NET MAUI apps — service registration, lifetime selection (Singleton/Transient/Scoped), constructor injection, automatic resolution via Shell navigation, explicit resolution patterns, platform-specific registrations, and testability best practices. |
| [maui-file-handling](plugins/maui-skills/skills/maui-file-handling/) | Guidance for file picker, file system helpers, bundled assets, and app data storage in .NET MAUI applications. Covers FilePicker APIs, FileResult handling, platform permissions, and common pitfalls across Android, iOS, macOS, and Windows. |
| [maui-geolocation](plugins/maui-skills/skills/maui-geolocation/) | Add geolocation capabilities to .NET MAUI apps using Microsoft.Maui.Devices.Sensors. Covers one-shot and continuous location, platform permissions (Android, iOS, macOS, Windows), accuracy levels, CancellationToken usage, mock-location detection, and a DI-friendly service wrapper. Use this skill when implementing GPS/location features, requesting location permissions, or troubleshooting platform-specific geolocation behavior in MAUI applications. |
| [maui-gestures](plugins/maui-skills/skills/maui-gestures/) | Guidance for implementing tap, swipe, pan, pinch, drag-and-drop, and pointer gesture recognizers in .NET MAUI applications. Covers XAML and C# usage, combining gestures, and platform differences. |
| [maui-graphics-drawing](plugins/maui-skills/skills/maui-graphics-drawing/) | Guidance for custom drawing with Microsoft.Maui.Graphics, GraphicsView, canvas drawing operations, shapes, paths, text rendering, image drawing, shadows, clipping, and canvas state management. |
| [maui-hot-reload-diagnostics](plugins/maui-skills/skills/maui-hot-reload-diagnostics/) | Diagnose and troubleshoot .NET MAUI Hot Reload issues (C# Hot Reload, XAML Hot Reload, Blazor Hybrid). Use when hot reload isn't working, UI doesn't update after code changes, or when setting up hot reload debugging environment. Covers all UI approaches (XAML, MauiReactor, C# Markup, Blazor Hybrid), Visual Studio, VS Code, environment variables, encoding requirements, and MetadataUpdateHandler. |
| [maui-hybridwebview](plugins/maui-skills/skills/maui-hybridwebview/) | Guidance for embedding web content in .NET MAUI apps using HybridWebView, including JavaScript–C# interop, bidirectional communication, raw messaging, and trimming/NativeAOT considerations. |
| [maui-local-notifications](plugins/maui-skills/skills/maui-local-notifications/) | Add local notifications to .NET MAUI apps on Android, iOS, and Mac Catalyst. Use when implementing scheduled reminders, alert messages, in-app notification systems, or any feature that sends notifications to the user's device. Covers notification channels, permissions, scheduling, foreground/background handling, and DI registration. Works with XAML/MVVM, C# Markup, and MauiReactor. |
| [maui-localization](plugins/maui-skills/skills/maui-localization/) | Guidance for localizing .NET MAUI apps: multi-language support via .resx resource files, culture resolution and runtime switching, RTL layout, platform language declarations (iOS/Mac Catalyst Info.plist, Windows Package.appxmanifest), and image localization strategies. |
| [maui-maps](plugins/maui-skills/skills/maui-maps/) | Guidance for adding map controls, pins, polygons, polylines, geocoding, Google Maps API key configuration, and platform setup in .NET MAUI apps using Microsoft.Maui.Controls.Maps. |
| [maui-media-picker](plugins/maui-skills/skills/maui-media-picker/) | Guidance for picking photos/videos, capturing from camera, multi-select (.NET 10), MediaPickerOptions, platform permissions, and FileResult handling in .NET MAUI. |
| [maui-performance](plugins/maui-skills/skills/maui-performance/) | Performance optimization guidance for .NET MAUI apps covering profiling, compiled bindings, layout efficiency, image optimization, resource dictionaries, startup time, trimming, and NativeAOT configuration. |
| [maui-permissions](plugins/maui-skills/skills/maui-permissions/) | .NET MAUI runtime permissions guidance — checking and requesting permissions, PermissionStatus handling, custom permissions via BasePlatformPermission, platform-specific manifest/plist declarations, and DI-friendly service patterns. |
| [maui-platform-invoke](plugins/maui-skills/skills/maui-platform-invoke/) | Guidance for calling platform-specific native APIs from .NET MAUI apps. Covers partial classes, conditional compilation, multi-targeting configuration, and dependency injection patterns for cross-platform code that needs Android, iOS, Mac Catalyst, or Windows functionality. |
| [maui-push-notifications](plugins/maui-skills/skills/maui-push-notifications/) | End-to-end guide for adding push notifications to .NET MAUI apps. Covers Firebase Cloud Messaging (Android), Apple Push Notification Service (iOS), Azure Notification Hubs as the cross-platform broker, an ASP.NET Core backend API, and the MAUI client wiring on every platform. Use this skill when implementing push notifications, registering devices, sending test notifications, or troubleshooting token/registration issues in a .NET MAUI application. |
| [maui-rest-api](plugins/maui-skills/skills/maui-rest-api/) | Guidance for consuming REST APIs in .NET MAUI apps. Covers HttpClient setup with System.Text.Json, DI registration, service interface/implementation pattern, full CRUD operations (GET, POST, PUT, DELETE), error handling, platform-specific clear-text traffic configuration, and async/await best practices. Use when adding API calls, creating data services, or wiring up HttpClient in a MAUI project. |
| [maui-safe-area](plugins/maui-skills/skills/maui-safe-area/) | .NET MAUI safe area and edge-to-edge layout guidance for .NET 10+. Covers SafeAreaEdges property, SafeAreaRegions enum, per-edge control, keyboard avoidance, Blazor Hybrid CSS safe areas, migration from legacy APIs, and platform-specific behavior for Android, iOS, and Mac Catalyst. |
| [maui-secure-storage](plugins/maui-skills/skills/maui-secure-storage/) | Add secure storage to .NET MAUI apps using SecureStorage.Default. Covers SetAsync, GetAsync, Remove, RemoveAll, platform setup (Android backup rules, iOS Keychain entitlements, Windows limits), common pitfalls, and a DI wrapper service for testability. Use when storing tokens, secrets, or sensitive user data. |
| [maui-shell-navigation](plugins/maui-skills/skills/maui-shell-navigation/) | .NET MAUI Shell navigation guidance — Shell visual hierarchy, AppShell setup, tab bars, flyout menus, URI-based navigation with GoToAsync, route registration, query parameters, back navigation, and navigation events. Use when building or modifying Shell-based MAUI apps, adding pages/routes, configuring tabs or flyout, or implementing navigation with data passing. |
| [maui-speech-to-text](plugins/maui-skills/skills/maui-speech-to-text/) | Add speech-to-text voice input to .NET MAUI apps using CommunityToolkit.Maui. Use this skill when implementing voice input, speech recognition, microphone permissions, or hands-free text entry in MAUI applications. Works with any UI pattern (XAML/MVVM, C# Markup, MauiReactor). Produces normalized natural language text from spoken input. |
| [maui-sqlite-database](plugins/maui-skills/skills/maui-sqlite-database/) | Add SQLite local database storage to .NET MAUI apps using sqlite-net-pcl. Use this skill when implementing local data persistence, offline storage, CRUD operations, or database access in MAUI applications. Covers Constants, data models with ORM attributes, async database service with lazy init, DI registration, WAL mode, and file management. Works with any UI pattern (XAML/MVVM, C# Markup, MauiReactor). |
| [maui-theming](plugins/maui-skills/skills/maui-theming/) | Guide for theming .NET MAUI apps—light/dark mode support, AppThemeBinding, dynamic resources, ResourceDictionary theme switching, and system theme detection. |
| [maui-unit-testing](plugins/maui-skills/skills/maui-unit-testing/) | xUnit testing guidance for .NET MAUI apps — ViewModel testing, mocking MAUI services, test project setup, code coverage, and on-device test runners. |

## Installation

### GitHub Copilot CLI

Install the plugin from this repository:

```bash
/plugin marketplace add davidortinau/maui-skills
/plugin install maui-skills@maui-skills
/skills reload
```

Individual skills become available automatically based on your prompts.

### Claude Code

Clone this repository and copy the skills you want into your Claude Code skills directory:

```bash
git clone https://github.com/davidortinau/maui-skills.git
cp -R maui-skills/plugins/maui-skills/skills/maui-* ~/.claude/skills/
```

Or copy individual skills:

```bash
cp -R maui-skills/plugins/maui-skills/skills/maui-collectionview ~/.claude/skills/
```

### Manual (any tool)

Each skill is a self-contained directory with a `SKILL.md` file using standard YAML frontmatter (`name`, `description`). Copy the directories into whichever location your AI tool reads skills from.

## Skill Format

Every skill follows the same structure:

```
maui-<topic>/
├── SKILL.md              # Skill definition with YAML frontmatter
└── scripts/              # Optional helper scripts
```

The `SKILL.md` file contains:

- YAML frontmatter with `name` and `description` fields
- Detailed markdown guidance, best practices, and code examples
- Platform-specific notes for Android, iOS, Mac Catalyst, and Windows

## Contributing

Contributions are welcome. To add or improve a skill:

1. Fork this repository.
2. Add or edit a skill under `plugins/maui-skills/skills/<skill-name>/`.
3. Ensure the `SKILL.md` has valid YAML frontmatter with `name` and `description`.
4. If adding a new skill, add its path to `.github/plugin/marketplace.json`.
5. Submit a pull request with a description of what the skill covers.

## License

[MIT](LICENSE)
