# AInamika Android SDK

[![Maven Central](https://img.shields.io/maven-central/v/com.ainamika/sdk.svg)](https://search.maven.org/artifact/com.ainamika/sdk)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform](https://img.shields.io/badge/platform-android-green.svg)](https://developer.android.com)
[![API](https://img.shields.io/badge/API-21%2B-brightgreen.svg)](https://android-arsenal.com/api?level=21)

AI-powered analytics SDK for Android - Track user behavior, engagement metrics, and app performance with ease.

---

## Installation & Setup (5 minutes)

### Step 1: Add Repository

Ensure you have Maven Central in your project's `settings.gradle.kts` (or `build.gradle` for older projects):

```kotlin
// settings.gradle.kts
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()  // Add this if not present
    }
}
```

### Step 2: Add Dependency

Add to your app's `build.gradle.kts`:

```kotlin
dependencies {
    implementation("com.ainamika:sdk:1.0.0")
}
```

<details>
<summary>Using Groovy DSL?</summary>

```groovy
dependencies {
    implementation 'com.ainamika:sdk:1.0.0'
}
```
</details>

### Step 3: Create Application Class

Create an `Application` class if you don't have one:

```kotlin
// MyApp.kt
import android.app.Application
import com.ainamika.sdk.core.Ainamika
import com.ainamika.sdk.core.AinamikaConfig

class MyApp : Application() {
    override fun onCreate() {
        super.onCreate()

        // Initialize with your project credentials
        val config = AinamikaConfig.Builder("your_project_key")
            .clientId("your_client_id")
            .enableAutoTracking(true)
            .enableEngagementTracking(true)
            .enablePerformanceTracking(true)
            .enableErrorTracking(true)
            .build()

        Ainamika.initialize(config)
    }
}
```

### Step 4: Register in AndroidManifest.xml

Add your Application class to `AndroidManifest.xml`:

```xml
<application
    android:name=".MyApp"
    android:allowBackup="true"
    ... >

    <!-- Your activities -->

</application>
```

### Step 5: Sync & Run

Click "Sync Now" in Android Studio and run your app. That's it!

---

## Quick Start - Basic Usage

### Track Events

```kotlin
// Track a custom event
Ainamika.track("button_clicked", mapOf(
    "button_name" to "checkout",
    "screen" to "cart"
))

// Track a screen view
Ainamika.screen("ProductDetails", mapOf(
    "product_id" to "123",
    "category" to "electronics"
))
```

### Identify Users

```kotlin
// When user logs in
Ainamika.identify("user_123", mapOf(
    "email" to "user@example.com",
    "plan" to "premium"
))

// When user logs out
Ainamika.reset()
```

### Track Errors Manually

```kotlin
try {
    // risky operation
} catch (e: Exception) {
    Ainamika.trackError(e, mapOf("context" to "checkout"))
}
```

---

## Features

- **Event Tracking** - Track custom events and screen views
- **User Identification** - Identify users and track their journey
- **Auto-Tracking** - Automatic screen view and interaction tracking
- **Engagement Metrics** - Scroll depth, rage clicks, time on screen
- **Performance Monitoring** - App startup time, screen render, frame drops, ANR detection
- **Error Tracking** - Crash reporting with screenshots
- **Network Tracking** - API call monitoring via OkHttp interceptor
- **Offline Support** - Events queued and sent when online
- **Privacy Compliant** - GDPR/CCPA ready with user consent controls

---

## What Gets Tracked Automatically?

Once initialized with auto-tracking enabled, the SDK automatically captures:

| Event | Description |
|-------|-------------|
| `screen_view` | When user navigates to a new screen |
| `session_start` | When app launches or resumes from background |
| `engagement_scroll_depth` | Scroll progress (25%, 50%, 75%, 100%) |
| `engagement_rage_click` | Frustrated taps (3+ taps in same area) |
| `page_exit` | Time spent, scroll depth when leaving screen |
| `performance_app_startup` | Cold/warm start time |
| `performance_screen_render` | Screen render time |
| `api_call` | Network request metrics (requires interceptor) |
| `error` | Crashes and exceptions |

No additional code required!

---

## Configuration

For advanced configuration, use the builder pattern:

```kotlin
class MyApp : Application() {
    override fun onCreate() {
        super.onCreate()

        val config = AinamikaConfig.Builder("your_project_key")
            .clientId("your_client_id")                    // Required for authentication
            .apiEndpoint("https://your-api.com")           // Custom endpoint (optional)
            .enableAutoTracking(true)                      // Auto screen/click tracking
            .enableEngagementTracking(true)                // Scroll, rage clicks, etc.
            .enablePerformanceTracking(true)               // Startup, render, ANR
            .enableNetworkTracking(true)                   // API call tracking
            .enableErrorTracking(true)                     // Crash reporting
            .enableScreenshotOnCrash(true)                 // Capture screenshot on crash
            .enableDebugLogging(BuildConfig.DEBUG)         // Debug logs
            .sampleRate(1.0f)                              // Event sampling (0.0-1.0)
            .errorSampleRate(1.0f)                         // Error sampling (0.0-1.0)
            .sessionTimeoutMinutes(30)                     // Session timeout
            .eventBatchSize(20)                            // Events per batch
            .eventFlushIntervalMs(30000)                   // Flush interval (30s)
            .maxEventsInQueue(1000)                        // Max queued events
            .excludeActivities(listOf(                     // Exclude from tracking
                "SplashActivity",
                "DebugActivity"
            ))
            .build()

        Ainamika.initialize(config)
    }
}
```

---

## API Reference

### Core Methods

| Method | Description |
|--------|-------------|
| `Ainamika.initialize(config)` | Initialize the SDK with configuration |
| `Ainamika.track(event, properties)` | Track a custom event |
| `Ainamika.screen(name, properties)` | Track a screen view |
| `Ainamika.identify(userId, traits)` | Identify a user |
| `Ainamika.setUserProperties(traits)` | Update user properties |
| `Ainamika.reset()` | Reset user identity (logout) |
| `Ainamika.flush()` | Flush pending events immediately |
| `Ainamika.trackError(throwable, properties)` | Manually track an error |
| `Ainamika.setEnabled(enabled)` | Enable/disable SDK at runtime |

### Utility Methods

| Method | Description |
|--------|-------------|
| `Ainamika.getUserId()` | Get current user ID |
| `Ainamika.getSessionId()` | Get current session ID |
| `Ainamika.isAnonymous()` | Check if user is anonymous |
| `Ainamika.initialized` | Check if SDK is initialized |
| `Ainamika.version` | Get SDK version |
| `Ainamika.getNetworkInterceptor()` | Get OkHttp interceptor for API tracking |

---

## Event Tracking

### Custom Events

```kotlin
// Simple event
Ainamika.track("purchase_completed")

// Event with properties
Ainamika.track("purchase_completed", mapOf(
    "order_id" to "ORD-123",
    "amount" to 99.99,
    "currency" to "USD",
    "items" to listOf("item1", "item2")
))
```

### Screen Views

```kotlin
// Track screen view
Ainamika.screen("ProductList")

// With properties
Ainamika.screen("ProductDetails", mapOf(
    "product_id" to "123",
    "source" to "search"
))
```

### Auto-Tracked Events

When `enableAutoTracking(true)` is set, the SDK automatically tracks:

| Event | Description |
|-------|-------------|
| `screen_view` | Activity/Fragment visibility |
| `view_clicked` | Button, ImageView clicks |
| `text_changed` | EditText input |
| `scroll_detected` | RecyclerView, ScrollView scrolls |

---

## Engagement Tracking

When `enableEngagementTracking(true)` is set, the SDK tracks:

### Scroll Depth
```json
{
  "event": "engagement_scroll_depth",
  "properties": {
    "depth": 50,
    "maxDepth": 75,
    "timeToReach": 5000,
    "screenName": "ProductList"
  }
}
```
Tracks when users reach 25%, 50%, 75%, 90%, and 100% scroll depth.

### Rage Clicks
```json
{
  "event": "engagement_rage_click",
  "properties": {
    "clickCount": 5,
    "position": { "x": 540, "y": 1200 },
    "totalRageClicks": 2,
    "screenName": "Checkout"
  }
}
```
Detects frustrated users (3+ rapid taps in same area within 1 second).

### Page Exit
```json
{
  "event": "page_exit",
  "properties": {
    "timeOnPage": 45000,
    "activeTime": 40000,
    "idleTime": 5000,
    "scrollDepth": 75,
    "totalClicks": 12,
    "rageClicks": 0,
    "screenName": "ProductDetails"
  }
}
```
Tracks comprehensive metrics when user leaves a screen.

---

## Performance Tracking

When `enablePerformanceTracking(true)` is set, the SDK tracks:

### App Startup
```json
{
  "event": "performance_app_startup",
  "properties": {
    "value": 1250,
    "pageLoad": 1250,
    "domContentLoaded": 800,
    "startType": "cold",
    "firstActivity": "MainActivity"
  }
}
```

### Screen Render
```json
{
  "event": "performance_screen_render",
  "properties": {
    "value": 150,
    "element": "ProductDetails",
    "screenName": "ProductDetails"
  }
}
```

### Frame Stats (Jank Detection)
```json
{
  "event": "performance_frame_stats",
  "properties": {
    "value": 8.5,
    "totalFrames": 1200,
    "droppedFrames": 102,
    "screenName": "AnimatedList"
  }
}
```

### ANR Detection
```json
{
  "event": "performance_anr",
  "properties": {
    "blockedTimeMs": 6000,
    "screenName": "DataSync",
    "mainThreadState": "BLOCKED"
  }
}
```

### Memory Pressure
```json
{
  "event": "performance_memory_pressure",
  "properties": {
    "severity": "high",
    "trimLevel": 15,
    "usedMemoryMb": 180,
    "maxMemoryMb": 256,
    "screenName": "ImageGallery"
  }
}
```

---

## Network Tracking

Track API calls by adding the interceptor to your OkHttpClient:

```kotlin
val client = OkHttpClient.Builder()
    .addInterceptor(Ainamika.getNetworkInterceptor()!!)
    .build()
```

This automatically tracks:
```json
{
  "event": "api_call",
  "properties": {
    "url": "https://api.example.com/products",
    "method": "GET",
    "status": 200,
    "duration": 245,
    "success": true
  }
}
```

---

## Error Tracking

### Automatic Crash Reporting

When `enableErrorTracking(true)` is set, crashes are automatically captured with:
- Stack trace
- Device info
- Memory state
- Screenshot (if enabled)

### Manual Error Tracking

```kotlin
try {
    // risky operation
} catch (e: Exception) {
    Ainamika.trackError(e, mapOf(
        "context" to "checkout_process",
        "step" to "payment"
    ))
}
```

---

## User Management

### Identify Users

```kotlin
// When user logs in
Ainamika.identify("user_123", mapOf(
    "email" to "user@example.com",
    "name" to "John Doe",
    "plan" to "premium",
    "signup_date" to "2024-01-15"
))
```

### Update User Properties

```kotlin
Ainamika.setUserProperties(mapOf(
    "last_purchase" to "2024-03-20",
    "total_orders" to 15
))
```

### Anonymous Users

Before `identify()` is called, users are tracked with an anonymous ID:
- Format: `anon_<random>_<timestamp>`
- Persisted across app launches
- Automatically linked when `identify()` is called

### Reset User

```kotlin
// On logout - generates new anonymous ID
Ainamika.reset()
```

---

## Session Management

Sessions are automatically managed:
- New session starts on app launch
- Session expires after 30 minutes of inactivity (configurable)
- `session_start` event is tracked with device info

---

## Offline Support

Events are automatically:
- Queued in memory when offline
- Persisted to disk if app closes
- Retried with exponential backoff
- Sent in batches when online

---

## ProGuard / R8

If using ProGuard or R8, add these rules:

```proguard
# AInamika SDK
-keep class com.ainamika.sdk.** { *; }
-keepclassmembers class com.ainamika.sdk.** { *; }

# Gson (used for serialization)
-keepattributes Signature
-keepattributes *Annotation*
-keep class com.google.gson.** { *; }
```

---

## Debug Mode

Enable debug logging to see SDK activity:

```kotlin
AinamikaConfig.Builder("your_project_key")
    .enableDebugLogging(true)
    .build()
```

Logs are tagged with:
- `Ainamika` - Core SDK
- `EventTracker` - Event tracking
- `EngagementTracker` - Engagement metrics
- `PerformanceTracker` - Performance metrics
- `ErrorTracker` - Error/crash tracking
- `NetworkTracker` - API tracking

---

## Requirements

- **Minimum SDK**: 21 (Android 5.0 Lollipop)
- **Target SDK**: 34 (Android 14)
- **Kotlin**: 1.9+
- **Java**: 17+

---

## Permissions

The SDK requires these permissions (automatically merged):

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

---

## Data Privacy

### GDPR Compliance

```kotlin
// Disable tracking for GDPR opt-out
Ainamika.setEnabled(false)

// Re-enable when consent given
Ainamika.setEnabled(true)
```

### Data Collected

| Data Type | Purpose |
|-----------|---------|
| Anonymous ID | User identification (no PII) |
| Device model | Analytics segmentation |
| OS version | Compatibility insights |
| App version | Release tracking |
| Screen views | Usage analytics |
| Custom events | Business metrics |

---

## Comparison with Web SDK

The Android SDK tracks the same events as the Web SDK for consistent cross-platform analytics:

| Web SDK Event | Android SDK Event | Notes |
|---------------|-------------------|-------|
| `session_start` | `session_start` | Includes device info |
| `engagement_scroll_depth` | `engagement_scroll_depth` | Same thresholds |
| `engagement_rage_click` | `engagement_rage_click` | Same detection logic |
| `page_exit` | `page_exit` | Screen exit metrics |
| `performance_page_load` | `performance_app_startup` | App startup time |
| `performance_lcp` | `performance_screen_render` | Screen render time |
| `performance_cls` | `performance_frame_stats` | Frame drop/jank |
| `api_call` | `api_call` | Same format |

---

## Troubleshooting

### Events Not Sending

1. Check network connectivity
2. Verify `projectKey` and `clientId` are correct
3. Enable debug logging to see errors
4. Check if SDK is enabled: `Ainamika.initialized`

### High Memory Usage

1. Reduce `maxEventsInQueue`
2. Decrease `eventFlushIntervalMs`
3. Disable screenshot on crash

### Build Issues

Ensure you have the correct dependencies:
```kotlin
implementation("com.ainamika:sdk:1.0.0")
```

---

## Support

- **Email**: support@ainamika.com

---

## License

MIT License - see [LICENSE](LICENSE) for details.

---

## Changelog

### 1.0.0
- Initial release
- Event tracking with batching
- User identification
- Auto-tracking for screens and clicks
- Engagement tracking (scroll depth, rage clicks, page exit)
- Performance tracking (startup, render, frame stats, ANR, memory)
- Network tracking via OkHttp interceptor
- Error tracking with screenshots
- Offline support
- Session management
