<div align="center">

<img width="1200" height="475" alt="GHBanner" src="https://github.com/user-attachments/assets/0aa67016-6eaf-458a-adb2-6e31a0763ed6" />

  <h1>Built with AI Studio & Google Jules AI Agent</h1>

  <p>The fastest path from prompt to production with Gemini — directly on your mobile device.</p>

  <a href="https://aistudio.google.com/apps">Start building</a>

</div>

---

# 📱 How to Build an APK from your Google AI Studio App Directly on Your Phone (No Computer Needed!)

Have you ever wanted to turn your Google AI Studio web app or native Android app into an installable **APK** and run it directly on your phone, but didn't want to deal with the hassle of installing Android Studio, configuring Java SDKs, setting up Gradle, or even opening a computer?

With this brand-new, completely automated method, you can **build and install an APK directly on your phone**, completely free, without needing a computer! This guide shows you how to leverage **Google Jules** (the autonomous coding agent) and **GitHub Actions** to compile, package, and deploy your AI-generated app into a ready-to-use Android APK.

---

## 🚀 How It Works: The 100% Mobile Workflow

```
[Google AI Studio] ──(Save to)──> [GitHub Repository] ──(Prompt)──> [Google Jules]
                                                                        │
                                                                 (Creates Build pipeline)
                                                                        ▼
[Physical Phone] <──(Install APK)── [GitHub Action Artifacts] <──(Compiles App)
```

1. **Vibe Code in Google AI Studio**: Describe your app idea using natural language right from your mobile browser or desktop.
2. **Publish to GitHub**: Sync the generated code directly to your GitHub repository in one click.
3. **Prompt Google Jules**: Ask Jules (the coding agent) to set up the APK build workflow. Jules configures the entire pipeline autonomously, dry-runs compile checks to fix errors, and pushes the build configuration.
4. **Auto-Compile with GitHub Actions**: GitHub Actions spins up an cloud runner, sets up the Android SDK, and builds a clean debug `.apk` file.
5. **Download and Install**: Open GitHub on your phone, download the compiled APK artifact, and install it instantly.

---

## 🛠️ Prerequisites

Before starting, make sure you have:
* A **[Google AI Studio](https://aistudio.google.com/)** account.
* A **[GitHub](https://github.com/)** account (free).
* An **Android smartphone** (to install and test your app).
* Access to **Google Jules** (via the web console or mobile app like Jules Console) connected to your GitHub repository.

---

## 📝 Step-by-Step Guide

### Step 1: Create your App in Google AI Studio
1. Open [Google AI Studio](https://aistudio.google.com/) in your browser.
2. Under the **Build** tab, choose your platform:
   * **Native Android App**: Generates a high-quality Kotlin and Jetpack Compose project.
   * **Web App (React/Vite/TypeScript)**: Generates a full-stack web application.
3. Enter your prompt (e.g., *"Create a beautiful sci-fi unit converter app with interactive sliders, custom themes, and historic unit logging"*).
4. AI Studio's Antigravity Agent will generate all the code files and display a live preview in the side pane.

### Step 2: Publish/Sync to GitHub
1. Once you are happy with the app preview, tap the **Save to GitHub** (or **Export to GitHub**) button in the top menu.
2. Authenticate with your GitHub account when prompted.
3. Create a new repository (e.g., `my-cool-ai-app`) and choose to make it public or private.
4. Click **Create Repo** to push all the generated source files directly to GitHub.

### Step 3: Let Google Jules Work Its Magic ✨
Now, instead of manually writing configuration files, Gradle setup, or building processes, you can summon **Google Jules** to handle everything for you!

1. Open **Google Jules** (via [jules.google.com](https://jules.google.com) or the mobile **Jules Console** wrapper on your phone).
2. Select your newly created repository and choose the `main` branch.
3. Enter a clear prompt asking Jules to set up the build pipeline for you:

   > *"Hey Jules, I want to build a debug APK from my app. Please create a GitHub Actions workflow that automatically compiles this project into an installable APK whenever I push changes or run it manually. Also, run a quick dry-run check in your sandbox to make sure there are no syntax or dependency errors in the code before pushing!"*

4. **Why Jules is a game-changer here:**
   * **Autonomous Configuration**: Jules detects whether your app is a **Native Kotlin/Compose app** or a **Web App (React/TypeScript)** and creates the correct workflow file (see templates below).
   * **Pre-commit Sandbox Dry Runs**: Jules acts as a local compiler in its secure Linux sandbox. It will dry-run the Gradle or Capacitor setup, inspect logs for any hidden errors, self-correct them, and push clean, functional files to GitHub. This eliminates the annoying back-and-forth of failing builds!

---

### Step 4: GitHub Actions Autopilot ✈️

Once Jules pushes the configuration, GitHub Actions takes over. Below are the actual workflow configurations that Jules places under `.github/workflows/build-apk.yml` based on your project type.

#### Option A: If Your App is a Native Android App (Kotlin / Compose)
For native projects, Jules sets up Java, installs the Android command-line tools, grants Gradle permissions, and triggers the build:

```yaml
name: Build Android Debug APK

on:
  push:
    branches: [ main ]
  workflow_dispatch: # Allows manual trigger from your phone

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@692973e3d937129bcbf40652eb9f2f61becf3332 # v4.1.7

      - name: Set up JDK 17
        uses: actions/setup-java@6a0805fcdc5074154ca0117368f53b8540d49926 # v4.2.2
        with:
          distribution: 'zulu'
          java-version: '17'
          cache: 'gradle'

      - name: Grant Execute Permissions to Gradle
        run: chmod +x gradlew

      - name: Build Debug APK
        run: ./gradlew assembleDebug

      - name: Upload APK Artifact
        uses: actions/upload-artifact@0b2256b8c012f0828dc542b3febcab082c67f72b # v4.3.4
        with:
          name: debug-apk
          path: app/build/outputs/apk/debug/app-debug.apk
```

#### Option B: If Your App is a Web App (React / PWA / TypeScript)
If you built a web app in AI Studio, it needs to be wrapped so it can run inside a native Android WebView. Jules achieves this by automatically configuring **Capacitor** (an open-source web-to-native wrapper) and building it:

```yaml
name: Wrap and Build Web App to APK

on:
  push:
    branches: [ main ]
  workflow_dispatch:

jobs:
  build-apk:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@692973e3d937129bcbf40652eb9f2f61becf3332 # v4.1.7

      - name: Set up Node.js
        uses: actions/setup-node@1e60f620b9541d16bece96c54020245380758a80 # v4.0.3
        with:
          node-version: 20
          cache: 'npm'

      - name: Install Dependencies
        run: npm ci

      - name: Build Web Application
        run: npm run build

      - name: Set up Capacitor
        run: |
          npm install @capacitor/core @capacitor/cli
          npx cap init "My AI App" "com.aistudio.myapp" --web-dir=dist
          npm install @capacitor/android
          npx cap add android
          npx cap sync android

      - name: Set up JDK 17
        uses: actions/setup-java@6a0805fcdc5074154ca0117368f53b8540d49926 # v4.2.2
        with:
          distribution: 'zulu'
          java-version: '17'
          cache: 'gradle'

      - name: Build Android APK via Gradle
        run: |
          cd android
          chmod +x gradlew
          ./gradlew assembleDebug

      - name: Upload Compiled APK
        uses: actions/upload-artifact@0b2256b8c012f0828dc542b3febcab082c67f72b # v4.3.4
        with:
          name: web-wrapped-apk
          path: android/app/build/outputs/apk/debug/app-debug.apk
```

---

### Step 5: Download and Install Directly on Your Phone 📥

1. Go to your repository on GitHub using your phone's browser (or the GitHub mobile app).
2. Tap the **Actions** tab.
3. Tap on the latest active workflow run (marked with a green checkmark `✓` when complete).
4. Scroll down to the **Artifacts** section at the bottom of the page.
5. Tap **debug-apk** (or **web-wrapped-apk**) to download the `.zip` containing your APK.
6. Extract the zip file on your phone, tap the `.apk` file, and select **Install**.
   * *Note: Since this is a debug APK built by your own workflow, Android might prompt you with "Blocked by Play Protect". Simply tap **Install Anyway** to proceed.*
7. **Success!** Your custom Google AI Studio application is now running as a native application right on your home screen. 🎉

---

## ✨ Key Benefits of This Method

* **No Computer Required**: You can perform the entire development loop — prompting, building, debugging, and installing — using nothing but your smartphone!
* **100% Free**: Google AI Studio, GitHub, GitHub Actions, and Google Jules provide generous free tiers that make this setup completely free of charge.
* **Jules as Your Co-Pilot**: If the build fails on GitHub Actions due to a missing dependency or configuration issue, you don't need to struggle with code. Simply tell Jules the error message, and it will rewrite the files, verify the build in its sandbox, and push the fix automatically.
* **Rapid Iteration**: Want to add a new feature? Prompt AI Studio, sync to GitHub, and let the background automated build deliver a fresh APK directly to your phone in minutes.

---

## 🔒 Security, Secrets Management & Best Practices

When building AI Studio applications and automating their builds with GitHub Actions, maintaining a strong security posture is essential. Below are some best practices that we strongly encourage you to follow.

### 🔑 Secure Your Gemini API Key (Do Not Hardcode!)
Hardcoding your Gemini API keys or other sensitive secrets directly in your code violates critical security policies. If your repository is public, bots will instantly scrape and abuse your keys.

#### 1. For Web Apps (Vite / React)
* **Never** hardcode the key in your code:
  ```typescript
  // ❌ BAD: Avoid exposing secrets in your source code
  const apiKey = "AIzaSyD_EXAMPLE_KEY";
  ```
* **Instead**, read it from environment variables:
  ```typescript
  // ✅ GOOD: Load from environment variables
  const apiKey = import.meta.env.VITE_GEMINI_API_KEY;
  ```
* Define your key in a local `.env.local` file (which should be added to `.gitignore` to prevent committing it):
  ```env
  VITE_GEMINI_API_KEY=your_actual_api_key_here
  ```
* Configure the secret on GitHub:
  Go to your GitHub Repository -> **Settings** -> **Secrets and variables** -> **Actions** -> Add a Repository Secret named `VITE_GEMINI_API_KEY`.

#### 2. For Native Android Apps (Kotlin / Compose)
* **Never** put keys directly in `MainActivity.kt` or config files.
* **Instead**, use Android's `BuildConfig` or Gradle properties. You can store the API key in your user-level `gradle.properties` file or fetch it from system environment variables during GitHub Actions execution:
  ```kotlin
  // ✅ GOOD: Use build configuration
  val apiKey = BuildConfig.GEMINI_API_KEY
  ```
* In your `build.gradle.kts` configuration, inject the value safely:
  ```kotlin
  buildTypes {
      release {
          buildConfigField("String", "GEMINI_API_KEY", "\"${System.getenv("GEMINI_API_KEY") ?: ""}\"")
      }
      debug {
          buildConfigField("String", "GEMINI_API_KEY", "\"${System.getenv("GEMINI_API_KEY") ?: ""}\"")
      }
  }
  ```

---

### 🛡️ Use Pinned Commit SHAs in GitHub Workflows
To prevent supply chain attacks (where a third-party GitHub Action is compromised or maliciously updated), always pin third-party actions to an immutable full-length commit SHA rather than a mutable tag like `@v4`.

* **Vulnerable**:
  ```yaml
  uses: actions/checkout@v4
  ```
* **Secure**:
  ```yaml
  uses: actions/checkout@692973e3d937129bcbf40652eb9f2f61becf3332 # v4.1.7
  ```

---

### 📱 Handle Debug APKs Safely
The GitHub Actions templates provided in this guide generate a **debug APK** (`app-debug.apk`).

* **Internal Testing Only**: Debug APKs are signed with a generic debug key. They should only be used for personal testing, and never distributed to public users.
* **Keep Artifacts Private**: If your repository is public, anybody can access and download your built GitHub Actions artifacts. If your debug APK contains hardcoded sensitive keys or test credentials, they can be extracted via reverse engineering. Always verify that your API keys are loaded dynamically as environment variables.
* **Signing for Production**: To publish your app to the Google Play Store or distribute it securely, you must sign it with an upload/production key. **Never commit your Keystore file (`*.jks`) or Keystore password to your GitHub repository.** Keep them locally and use GitHub Secrets to supply them during production builds.

---

*Now go ahead, sync your Google AI Studio app to GitHub, call Google Jules, and build your very first fully custom APK directly on your phone! Happy Vibe Coding!* 🚀
