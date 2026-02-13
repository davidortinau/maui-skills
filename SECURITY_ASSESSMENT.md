# Security Risk Assessment — maui-skills

**Date:** 2026-02-12
**Scope:** 31 AI skills (SKILL.md files), 1 shell script, plugin metadata
**Repository:** davidortinau/maui-skills

---

## Executive Summary

This repository contains **AI skill definitions** (markdown documents with code examples) for .NET MAUI development, consumed by GitHub Copilot CLI and Claude Code. The skills are **passive guidance documents** — they instruct an AI assistant on how to generate code, but do not execute code themselves (with one exception: a diagnostic shell script).

**Overall risk: LOW.** No prompt injection, no real secrets, no destructive commands. However, several skills teach patterns that could introduce vulnerabilities in downstream apps if developers follow the examples without modification. These are documented below.

---

## 1. Skill Architecture Security

| Check | Result |
|-------|--------|
| Prompt injection attempts | ✅ **None found** — no hidden instructions, jailbreak phrases, or AI manipulation |
| Hardcoded secrets/credentials | ✅ **None** — all examples use placeholders (`<same-key-as-backend>`, `YOUR_GOOGLE_MAPS_KEY`) |
| Executable code in skills | ✅ **Safe** — code examples are illustrative C#/XAML snippets, not runnable scripts |
| External network calls | ✅ **None** — one `curl` example in push-notifications uses a placeholder URL |
| Destructive commands (`rm -rf`, `sudo`, etc.) | ✅ **None found** |
| Malicious dependencies | ✅ **None** — references only official Microsoft/NuGet packages |

---

## 2. Shell Script Review: `diagnose-hot-reload.sh`

**Location:** `plugins/maui-skills/skills/maui-hot-reload-diagnostics/scripts/`

| Check | Result |
|-------|--------|
| Downloads external content | ✅ No |
| Modifies system files | ✅ No |
| Runs with elevated privileges | ✅ No `sudo` usage |
| Destructive operations | ✅ No — only creates an output directory and reads diagnostics |
| Data exfiltration | ✅ No — writes to local directory only |

**Verdict: SAFE.** The script is a read-only diagnostic collector (dotnet info, env vars, file encoding checks, grep for project metadata).

---

## 3. Downstream Code Security Risks

These findings relate to **patterns taught by the skills** that could introduce vulnerabilities in apps built following the guidance.

### 🔴 CRITICAL — API Key Embedded in App Binary

**Skill:** `maui-push-notifications`
**Lines:** ~179–184

```csharp
public static class PushConfig
{
    public const string ApiKey = "<same-key-as-backend>";
}
```

The skill teaches storing a shared API key as a `const` in client code. Even with a placeholder value, developers will replace it with a real key. Constants compiled into .NET assemblies are trivially extractable via `ildasm`, `dnSpy`, or `strings`.

**Recommendation:** Add a warning that this pattern is for demo purposes only. Recommend dynamic token exchange (e.g., device-bound tokens issued after authentication) for production.

---

### 🟠 HIGH — HybridWebView Exposes All Public C# Methods to JavaScript

**Skill:** `maui-hybridwebview`

`SetInvokeJavaScriptTarget(new MyJsBridge())` exposes **every public method** on the target object to JavaScript. If the bridge class has methods beyond what the web content needs, or if the web content loads external resources, this creates an attack surface.

**Recommendation:** Add guidance to use a minimal interface/dedicated bridge class. Note that if the WebView ever loads remote content, this becomes a **critical** RCE vector.

---

### 🟠 HIGH — Deep Link Routing Without Input Validation

**Skill:** `maui-deep-linking`

```csharp
Shell.Current.GoToAsync(MapToRoute(intent.Data.ToString()!));
```

The example navigates directly from an incoming URI to a Shell route. If `MapToRoute()` does not strictly whitelist valid routes, a malicious deep link could navigate to unintended pages, pass crafted query parameters, or bypass authentication flows.

**Recommendation:** Add explicit input validation guidance and a route whitelist example.

---

### 🟠 HIGH — SQLite Database Stored Without Encryption

**Skill:** `maui-sqlite-database`

The skill teaches storing data in plaintext SQLite at `FileSystem.AppDataDirectory` with no mention of encryption. If the database contains PII, tokens, or sensitive business data, it is vulnerable to extraction via device backup, forensics, or filesystem access on rooted/jailbroken devices.

**Recommendation:** Add a note recommending `SQLCipher` or similar encryption for sensitive data, and cross-reference `maui-secure-storage` for tokens.

---

### 🟡 MEDIUM — Clear-Text HTTP Configuration Without Production Warning

**Skill:** `maui-rest-api`

The skill shows how to enable clear-text HTTP traffic for Android (`network_security_config.xml` with `cleartextTrafficPermitted="true"`) and iOS (`NSAppTransportSecurity` / `NSAllowsLocalNetworking`). These are appropriate for local development but dangerous in production.

**Recommendation:** Add an explicit `⚠️ DEVELOPMENT ONLY` callout and show how to scope the exception to `localhost`/`10.0.2.2` only (the Android example already does this correctly; the iOS example should match).

---

### 🟡 MEDIUM — iOS Keychain Persistence After App Uninstall

**Skill:** `maui-secure-storage`

The skill correctly documents that iOS Keychain entries persist after app uninstall, but does not flag the security implication: if a user uninstalls and another user reinstalls on the same device, the second user could access the first user's stored tokens.

**Recommendation:** Add guidance to clear Keychain entries on first launch (using a `UserDefaults` flag) as a mitigation.

---

### 🟡 MEDIUM — Push Notification Installation ID in Unencrypted Preferences

**Skill:** `maui-push-notifications`

The push notification installation ID is stored in `Preferences` (unencrypted shared preferences on Android). While not a credential, it could be used for device impersonation if extracted.

**Recommendation:** Consider storing the installation ID in `SecureStorage` instead.

---

### 🟢 LOW — Geolocation Data Handling

**Skill:** `maui-geolocation`

The skill covers mock location detection and permission handling well. However, it does not address:
- Rate limiting continuous location updates (battery/privacy)
- User consent UI requirements (GDPR/CCPA)
- Secure transmission of location data to backends

**Recommendation:** Add a brief note on privacy regulations and secure data handling.

---

## 4. Supply Chain & Distribution Security

| Check | Result |
|-------|--------|
| `marketplace.json` integrity | ✅ Clean metadata, no executable content |
| Dependency on external repos | ✅ None — self-contained markdown |
| Plugin installation path | ✅ Standard Copilot CLI plugin mechanism |
| Update mechanism | ℹ️ Manual — no auto-update, no remote code fetch |

---

## 5. Summary of Recommendations

| # | Priority | Action |
|---|----------|--------|
| 1 | 🔴 Critical | `maui-push-notifications`: Add warning against embedding API keys in app binaries; show token-exchange pattern |
| 2 | 🟠 High | `maui-hybridwebview`: Add guidance for minimal bridge interfaces; warn about remote content risks |
| 3 | 🟠 High | `maui-deep-linking`: Add input validation and route whitelisting examples |
| 4 | 🟠 High | `maui-sqlite-database`: Add encryption guidance for sensitive data |
| 5 | 🟡 Medium | `maui-rest-api`: Add explicit "development only" warning on clear-text HTTP config |
| 6 | 🟡 Medium | `maui-secure-storage`: Add Keychain-clear-on-first-launch pattern |
| 7 | 🟡 Medium | `maui-push-notifications`: Move installation ID to SecureStorage |
| 8 | 🟢 Low | `maui-geolocation`: Add privacy/GDPR note |

---

## 6. Conclusion

The maui-skills repository is **safe to use as an AI plugin**. The skills contain no prompt injection, no real secrets, no malicious code, and no destructive operations. The primary risk vector is that several skills teach simplified patterns (appropriate for tutorials) that could be insecure if adopted verbatim in production applications. Adding security callouts to the 4–5 highest-risk skills would significantly improve the overall security posture of apps built with this guidance.
