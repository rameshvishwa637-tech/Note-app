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

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2

      - name: Set up JDK 17
        uses: actions/setup-java@8df1039502a15bceb9433410b1a100fbe190c53b # v4.5.0
        with:
          distribution: 'zulu'
          java-version: '17'
          cache: 'gradle'

      - name: Grant Execute Permissions to Gradle
        run: chmod +x gradlew

      - name: Build Debug APK
        run: ./gradlew assembleDebug

      - name: Upload APK Artifact
        uses: actions/upload-artifact@b4b15b8c7c6ac21ea08fcf65892d2ee8f75cf882 # v4.4.3
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

permissions:
  contents: read

jobs:
  build-apk:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2

      - name: Set up Node.js
        uses: actions/setup-node@39370e3970a6d050c480ffad4ff0ed4d3fdee5af # v4.1.0
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
        uses: actions/setup-java@8df1039502a15bceb9433410b1a100fbe190c53b # v4.5.0
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
        uses: actions/upload-artifact@b4b15b8c7c6ac21ea08fcf65892d2ee8f75cf882 # v4.4.3
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

## 🔒 Security, Secrets Management, and Best Practices

When building mobile APKs and automating workflows with GitHub Actions, practicing solid security hygiene is critical to prevent credential exposure and build pipeline compromises.

### 1. Securing Gemini API Keys
Never hardcode your Google Gemini API key or any other service secret in your codebase (such as `MainActivity.kt` or web configuration files).
- **For Web Apps:** Use environmental variables (e.g., `import.meta.env.VITE_GEMINI_API_KEY`) and supply them at runtime, or inject them as GitHub Actions Secrets during the build phase.
- **For Native Android Apps:** Utilize the [Secrets Gradle Plugin](https://github.com/google/secrets-gradle-plugin) to read keys from a `local.properties` file that is kept out of version control, or access them securely via environmental variables during building.
- **Ignore Secrets Files:** Ensure that files like `local.properties`, `.env`, `.env.local`, or configuration files containing keys are explicitly added to your `.gitignore`.

### 2. Pinning GitHub Actions to Immutable Commit SHAs
Standard GitHub actions tags (such as `@v4`) are mutable and can be modified or hijacked by malicious actors to execute unauthorized code on your runners (a supply chain attack).
- Always pin actions to their **immutable full-length commit SHAs** (as shown in the Option A & B templates above).
- Keep version comments next to the SHAs (e.g., `uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2`) so they remain human-readable and maintainable.

### 3. Enforcing Least-Privilege Workflow Permissions
By default, GitHub Actions workflows may inherit broad write permissions if not explicitly restricted.
- Always specify explicit top-level permissions in your workflow files (e.g., `permissions: contents: read`).
- Adhere to the principle of least privilege so that a compromised workflow step cannot tamper with repository code or releases.

### 4. Handling Debug APKs Safely
Debug APKs are built with relaxed security constraints to facilitate local testing and debugging.
- **Credential Storage:** Never embed long-lived production secrets inside a debug APK. Debug APKs can be easily decompiled, exposing any embedded strings or keys.
- **Distribution:** Do not distribute debug APKs to general users. When launching your application publicly, configure a secure Release build signed with a unique upload key and utilize proper ProGuard/R8 obfuscation.

---

*Now go ahead, sync your Google AI Studio app to GitHub, call Google Jules, and build your very first fully custom APK directly on your phone! Happy Vibe Coding!* 🚀
