# Jenu-Gumpu Android App

Kotlin Android Studio project for the Jenu-Gumpu honey producer collective app.

## How To Run

1. Open Android Studio.
2. Select **File > Open**.
3. Choose `C:\Users\sindhu bindhu\Desktop\appjenu`.
4. Let Gradle sync finish.
5. Run the `app` configuration on an emulator or Android phone.

## Included

- Jetpack Compose frontend screens for dashboard, harvests, grading, prices, batches, collective stock, calculator, AI helper, and profile.
- MVVM state management with Kotlin Flow.
- Room database entities and DAO for users, harvests, batches, prices, and AI logs.
- Firebase Auth, Firestore, Storage, and Analytics dependencies.
- Firebase backend service methods for users, harvests, batches, and prices.
- Gemini API service with fallback local advice when no key is configured.
- Offline-first repository pattern for local save plus cloud sync.

## Backend Setup

- Add `google-services.json` after creating your Firebase Android app.
- Add `GEMINI_API_KEY=your_key_here` to `local.properties` for live Gemini advice.
- The app can open without these keys, but Firebase/Gemini calls need real project credentials.
