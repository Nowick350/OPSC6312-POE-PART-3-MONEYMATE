# MoneyMate 

A personal money manager built for South Africa — rand (ZAR) first, three
languages, and a cloud that actually works. MoneyMate is a **learning
prototype**: all accounts, transactions and balances are simulated demo data,
no real bank is connected.

Built with **Kotlin** and **Jetpack Compose** for a final-year PoE (Portfolios
of Evidence) project.

## Features

- **Demo banking** — two simulated accounts, income & expenses, monthly
  summary, in the South African rand
- **Budgets** — per-category monthly limits with progress bars, and
  **push-notification alerts** when a budget passes **80%** or goes **over**
- **Savings goals** — targets with progress, add money anytime
- **Multi-language** — English (main) + **isiZulu** + **Sesotho**, switchable
  in Settings, applied instantly
- **Live exchange rates** — ZAR → USD / EUR / GBP from the free
  [Frankfurter](https://www.frankfurter.dev/) REST API, shown on the Home tab
- **Push notifications (FCM)** — the phone's Firebase Cloud Messaging token is
  shown in Settings; a message sent from the Firebase console arrives live on
  the device
- **Receipt photos** — attach a photo of a receipt to any transaction; it is
  uploaded to **Firebase Storage** and can be viewed again from the Activity
  tab
- **Sign in with Google** — the modern Credential Manager flow, on top of
  email/password with **Firebase Authentication** (passwords are hashed by
  Firebase and never stored by MoneyMate)
- **Offline-first sync** — everything is written to the phone's **Room**
  database first, then copied up to **Cloud Firestore** whenever there is
  internet (deletes use tombstones, so offline changes reconcile correctly)
- **Unit tests** — the money helpers (formatting & parsing) are tested on the
  computer
- **GitHub Actions** — every commit is automatically built on GitHub's
  servers, and a debug APK is published as a build artifact

## Tech stack

| Layer      | Choice |
|------------|--------|
| UI         | Jetpack Compose (Material 3), zero XML layouts |
| Language   | Kotlin |
| Database   | Room (on the phone) + Cloud Firestore (cloud copy) |
| Auth       | Firebase Authentication (email/password + Google) |
| Push       | Firebase Cloud Messaging (budget alerts + console demo) |
| Storage    | Firebase Storage (receipt photos) |
| Images     | Coil |
| Exchange   | Frankfurter REST API (free, no key) |
| Tests      | kotlin.test unit tests |
| CI/CD      | GitHub Actions (debug APK on every commit) |

## Getting started

1. Open the project in **Android Studio** (Koala or newer).
2. Let Gradle sync — all dependencies are standard public artifacts.
3. The Firebase project is linked through `app/google-services.json`
   (the demo project). For your own Firebase project, replace that file and
   update `GOOGLE_CLIENT_ID` at the top of `MmRepo.kt`.
4. Run the **app** configuration on a device or emulator (min SDK 26).
5. First launch: create an account — it starts with a sample workspace
   (two accounts, a month of transactions, three budgets, two goals).

### Google sign-in setup (one time)

1. Get your debug SHA-1:
   `keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey`
2. In the **Google Cloud Console** → *APIs & Services → Credentials* →
   *Create OAuth client ID* → **Android** → package `com.example.moneymate`
   + your SHA-1.
3. Paste the client id into `GOOGLE_CLIENT_ID` in `MmRepo.kt`.

## Running the tests

* **Unit tests:** open `app/src/test/.../MoneyMateTest.kt` and run the green
  arrow (or *Build → Run Tests*). No device needed.
* **Receipts & push:** on a device — attach a photo when adding a
  transaction; for the push demo, copy the FCM token from Settings and send a
  message to it from *Firebase console → Communication → Cloud Messaging*.

## Project structure (app/src/main/java/com/example/moneymate)

| File | Role |
|------|------|
| `MainActivity.kt` | every screen (Compose) + the app shell |
| `MmRepo.kt` | the engine: Firebase auth, Room, Firestore sync, budget alerts, receipts, Google sign-in |
| `MmDatabase.kt` | Room entities, DAO and database |
| `MmPush.kt` | FCM: notification channel, local alerts, token, push service |
| `MmPushService` | the service that receives console-sent pushes |
| `Lang.kt` | English / Zulu / Sesotho string maps |
| `ExchangeRates.kt` | Frankfurter client + the Home-tab rates card |

## Honest notes

- All money is **simulated** — MoneyMate never touches a real bank. Real
  bank linking would need a provider account (for example Stitch) before it
  can be switched on.
- The demo Firebase project's data is public-testable by design; this is a
  PoE prototype, not a production release.
