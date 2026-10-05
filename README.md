[Home Page](https://mallikarjun524.github.io/)

# 🚀 Flutter Android App Links (Deep Linking)

This repository hosts the digital handshake files (`assetlinks.json`) required to enable **Android App Links** for the "Deep Link Cookbook" Flutter application.

## 📁 Repository Structure
```text
.
├── .nojekyll                # Bypasses Jekyll to allow hidden folders
├── _config.yml              # Forces inclusion of the .well-known folder
└── .well-known/
    └── assetlinks.json      # The Digital Handshake file (Android)
```
## 🛠️ 1. Web Configuration (assetlinks.json)
### This file is hosted at https://mallikarjun524.github.io/.well-known/assetlinks.json. It proves to Android that this website belongs to our app.

```json
[
  {
    "relation": [
      "delegate_permission/common.handle_all_urls"
    ],
    "target": {
      "namespace": "android_app",
      "package_name": "com.example.deeplink_cookbook",
      "sha256_cert_fingerprints": [
        "YOUR_SHA256_FINGERPRINT_HERE"
      ]
    }
  }
]
```

## 📱 2. Android Manifest Configuration
### To enable deep linking, the following intent-filter was added to android/app/src/main/AndroidManifest.xml inside the manifest -> application -> activity tag you need to put this code

```xml
<intent-filter android:autoVerify="true">
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />

    <data android:scheme="https" android:host="mallikarjun524.github.io" />
</intent-filter>

<meta-data android:name="flutter_deeplinking_enabled" android:value="true" />
```

## 🎯 3. Flutter (Dart) Routing
### We use the go_router package to handle incoming URLs and navigate to the correct screen.

> main.dart
```dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';

void main() {
  runApp(const MyApp());
}

final GoRouter _router = GoRouter(
  routes: [
    GoRoute(path: "/", builder: (context, state) => const HomeScreen()),
    GoRoute(
      path: "/details/:id",
      builder: (context, state) {
        String id = state.pathParameters["id"] ?? "";
        return DetailsScreen(id: id, key: ValueKey(id));
      },
    ),
  ],
);

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      routerConfig: _router,
      title: "Deeplink CookBook",
      debugShowCheckedModeBanner: false,
    );
  }
}

class DetailsScreen extends StatelessWidget {
  final String id;
  const DetailsScreen({super.key, required this.id});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Details")),
      body: Center(
        child: Column(
          children: [
            Text("Welcome to Details Page"),
            Text("You opened item ID: $id"),
          ],
        ),
      ),
    );
  }
}

class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Home Page")),
      body: Center(
        child:Column(
            children:[
                Text("Welcome to HomePage"),
                TextField(
                  controller: pageController,
                  onSubmitted: (value) {
                    // Use context.push function to display a page and able come back current page by pressing back button
                    // Use conext.go funtion to display a page and cannot able to come back current page context.go("/details/984"); 
                    context.push("/details/$value");
                  },
                  decoration: InputDecoration(hintText: "Enter Details id"),
                ),
            ]
        )
     ),
    );
  }
}
```

## 🧪 4. Testing Commands
### Ensure your device is connected via USB with Debugging enabled.

## A. Get SHA-256 Fingerprint
### Run this to get the ***`sha256_cert_fingerprints`*** value for your assetlinks.json:
```bash
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

## B. Verify App Link Status
### first run app and then Run this to check if Android has verified the handshake:
```bash
adb shell pm get-app-links com.example.deeplink_cookbook
```

## C. Trigger Deep Link via Terminal
### Test if the app opens to a specific route:
```bash
adb shell am start -a android.intent.action.VIEW -c android.intent.category.BROWSABLE -d "https://mallikarjun524.github.io/details/42"
```
