## What this is

Droidtale-v2 is an automated builder tool that packages the Undertale game for Android devices. It takes an Undertale `data.win` file, wraps it with a pre-built APK container, manages game assets, and produces a signed Android APK ready for installation on mobile devices.

### Stack

- **Language:** Java
- **Framework / runtime:** Java Swing (desktop GUI)
- **Notable libraries:** Apache Commons Codec (MD5/checksum utilities), Appium Sign (APK signing), Android Asset Packaging Tool (aapt.exe for asset bundling)

## How it's organized

```
src/com/mrpowergamerbr/droidtale/
  Droidtale.java           Entry point; initializes Swing GUI
  UndertaleWindow.java     Main UI window with file selection & build controls
  Sign.java                APK signing logic (RSA/SHA1, from Appium)
  StatusWindow.java        Progress/logging window during build
  FileStatus.java          Enum for validation states (VALID, INVALID_CHECKSUM, etc.)
  utils/
    UndertaleUtils.java    Checksum validation against Undertale v1.001
    DataWrapper.java       Container for validated file data & metadata
    FileUtils.java         Recursive file deletion
    ZipUtils.java          ZIP manipulation (adding assets to APK)
  UndertaleWrapper.apk     Pre-built base APK (~12.9 MB)
  aapt.exe                 Android Asset Packaging Tool (~1.4 MB)
  icon.png                 Application icon
```

**How it fits together:** 

The application is a single-window desktop builder. The user selects a `data.win` file, which triggers validation in `UndertaleUtils` (MD5 checksum against a known Undertale v1.001 build). On validation, the status label updates and the "Start!" button enables. When clicked, a worker thread orchestrates a multi-step build: copies the base `UndertaleWrapper.apk`, injects the renamed `data.win` as `game.droid`, copies Undertale's audio and data assets from the source directory, bundles them via `aapt`, signs the APK using embedded RSA keys (from `Sign.java`), and cleans up temporary files. Progress updates stream to a `StatusWindow` throughout.

## How to run it

1. **Build the project:**
   ```bash
   mvn clean package
   ```

2. **Run the application:**
   ```bash
   java -cp target/classes com.mrpowergamerbr.droidtale.Droidtale
   ```

3. **Usage:**
   - Launch the GUI
   - Select an Undertale `data.win` file (v1.001 expected; other versions may work if you override the checksum warning)
   - Click "Select" to browse for the file
   - Click "Start!" to build the APK
   - The signed APK will be written as `UndertaleWrapper.apk` in the current directory
   - Transfer the APK to an Android device and install

**Requirements:**
- Java 6+ (pom.xml targets Java 1.6)
- Undertale game files (data.win + audio/asset files in the same directory)
- Windows environment (aapt.exe bundled; Linux/Mac would need aapt ported or substituted)

## Try asking

- How does the checksum validation in `UndertaleUtils` work, and can I build with modded Undertale files?
- What's the role of `aapt.exe` in the build process, and why are game assets copied separately instead of embedded in `data.win`?
- How are the RSA keys embedded in `Sign.java`, and can I use my own signing keys instead?
