# TReceipts Pro

A Flutter app for scanning, managing, and exporting receipts — with AI-powered OCR via Firebase Cloud Functions and built-in German tax (Elster) categorisation.

**Version:** 1.0.35 (build 36) · **Platforms:** Android, iOS · **Languages:** English, German

---

## Features

- **Receipt scanning** — camera capture or gallery import (JPEG / PDF)
- **AI extraction** — Cloud Function OCR pipeline extracts seller, date, amount, items
- **Scan balance** — credit-based system; zero balance gates the scanner and redirects to pricing
- **Receipts overview** — monthly grouping, section filtering by Elster / category, image thumbnails
- **Excel export** — standard export (`excel_file_service`) and tax-optimised Elster export (`tax_excel_service`) via Syncfusion
- **In-app purchases** — Google Play Billing integration with pending-purchase detection
- **Authentication** — Google Sign-In (Android), Sign In with Apple (iOS)
- **Theme** — light / dark mode toggle, persisted via Riverpod
- **Localisation** — English and German (`flutter gen-l10n`)

---

## Tech Stack

| Concern | Package |
| --- | --- |
| State management | `flutter_bloc 9.1.1`, `flutter_riverpod 3.2.1` |
| Navigation | `go_router 17.1.0` |
| Dependency injection | `get_it 9.2.1` |
| Firebase | core, auth, firestore, storage, functions, messaging, crashlytics, analytics, performance, remote\_config, app\_check |
| Networking | `dio 5.9.2` with `RetryInterceptor` (3 retries) |
| Image | `camera 0.12.0`, `image_picker 1.2.1`, `flutter_image_compress 2.4.0` |
| In-app purchase | `in_app_purchase 3.2.3` |
| Persistence | `hive 2.2.3`, `shared_preferences 2.5.4` |
| Excel | `syncfusion_flutter_xlsio 32.2.7` |
| Connectivity | `connectivity_plus 6.1.5`, `internet_connection_checker 3.0.1` |
| Charts | `fl_chart 1.1.1` |
| Auth | `google_sign_in 7.2.0`, `sign_in_with_apple 7.0.1` |

---

## Architecture

Clean Architecture with feature-based modules.

```
lib/
├── main_prod.dart          # prod entry point
├── main_dev.dart           # dev entry point
├── main_common.dart        # shared bootstrap (Firebase, DI, Hive, Crashlytics)
├── core/
│   ├── init/               # Firebase init, RemoteConfig, AppInitializer
│   ├── router/             # GoRouter + Riverpod RouterNotifier
│   ├── services/           # ApiService, FirestoreService, ExcelService, etc.
│   ├── network/            # Dio client, RetryInterceptor
│   ├── providers/          # Shared Riverpod providers (auth, scan balance)
│   └── utils/              # ReceiptSectionResolver, helpers
├── di/                     # GetIt locators (minimal / full / data / presentation)
├── features/
│   ├── login/              # Riverpod auth controller, Google + Apple sign-in
│   ├── receipt_scan/       # BLoC: ImageCaptureBloc + ReceiptScanBloc
│   ├── receipts_overview_pro/  # Riverpod list, category filter, Excel export
│   ├── purchase_plans/     # In-app purchase flow, plan cards, pricing page
│   ├── home_page/          # Dashboard
│   ├── user_screen/        # Profile, settings, feedback, privacy policy
│   └── [theme_riverpod, locale_riverpod, feedback]/
└── shared/
    ├── themes/             # AppColors, AppTheme
    └── widgets/            # Reusable UI components
```

Each feature follows `data / domain / presentation` separation with its own repository, use-cases, and BLoC or Riverpod controller.

**State management mix:**
- **BLoC** — complex async flows (`ImageCaptureBloc`, `ReceiptScanBloc`, `ReceiptsOverviewBloc`)
- **Riverpod** — auth state, scan balance, plans, theme, locale

---

## Getting Started

### Prerequisites

- Flutter `>=3.11.4` and Dart `>=3.11.4`
- Android Studio / Xcode
- Firebase CLI (`npm install -g firebase-tools`)
- A Firebase project (`treceiptpro-production` for prod, separate project for dev)

### Setup

```bash
flutter pub get
```

Place the following files (not committed):
- `android/app/src/prod/google-services.json`
- `android/app/src/dev/google-services.json`
- `ios/Runner/GoogleService-Info.plist`
- `android/key.properties` (signing config)

### Running

```bash
# Development
flutter run --flavor dev -t lib/main_dev.dart

# Production
flutter run --flavor prod -t lib/main_prod.dart
```

### Building a release APK

```bash
flutter build apk --flavor prod -t lib/main_prod.dart --release
```

---

## Localisation

Source files live in `lib/l10n/`. After editing an `.arb` file, regenerate:

```bash
flutter gen-l10n
```

Supported locales: `en`, `de`.

---

## Code Quality

```bash
# Get dependencies
flutter pub get

# Auto-fix all fixable issues
dart fix --apply

# Fix specific lint categories
dart fix --apply --code=directives_ordering
dart fix --apply --code=prefer_const_constructors
dart fix --apply --code=unused_import

# Full static analysis
flutter analyze
```

---

## Firebase

The app uses a Firebase project for prod and a separate project for dev. Configuration is managed via flavors:

```
android/app/src/dev/google-services.json   ← dev flavor
android/app/src/prod/google-services.json  ← prod flavor
```

Services in use: Authentication, Firestore, Storage, Cloud Functions, Remote Config, Crashlytics, Analytics, Performance Monitoring, App Check, FCM.

---

## In-App Purchases

Scan packs are defined in `data/models/scan_pack.dart` and fetched from Google Play via `StoreService`. The `purchase_plans` feature handles:

- Listing available plans
- Initiating purchase via `in_app_purchase`
- Crediting scan balance in Firestore on successful transaction

---

## Environments

| Flavor | App ID | App Name |
| --- | --- | --- |
| `dev` | `com.receipts.treceipts_pro.dev` | TReceipt Dev |
| `prod` | `com.receipts.treceipts_pro` | TReceipt |

Min SDK: Android 26 · Target SDK: 36 · JVM target: 17

<table>
  <tr>
     <td align="center"><img width="100" height="200" alt="Login_Page" src="https://github.com/user-attachments/assets/ddfcc537-e897-4b4a-86f6-746a2c765036" /><br/>Login Page</td>
      &nbsp;&nbsp;&nbsp;
    <td align="center"><img width="100" height="200" alt="Home_Page" src="https://github.com/user-attachments/assets/5d111037-92c3-4c72-803b-c313213e5c89" /><br/>Home Page</td>
       &nbsp;&nbsp;&nbsp;
    <td align="center"><img width="100" height="200" alt="Scan_Page" src="https://github.com/user-attachments/assets/eff1feff-58be-4656-b3d6-bd5ab7e1c400" /><br/>Scan Page</td>
 &nbsp;&nbsp;&nbsp;
    <td align="center"><img width="100" height="200" alt="User_Page" src="https://github.com/user-attachments/assets/cbcecb66-0658-4266-8f78-b10a052155c2" /><br/>Receipts_Page</td>
 &nbsp;&nbsp;&nbsp;
    <td align="center"><img width="100" height="200" alt="User_Page" src="https://github.com/user-attachments/assets/a27ede1c-a9d8-475c-b680-2df679fd556f" /><br/>User Page</td>
     
  </tr>
</table>




