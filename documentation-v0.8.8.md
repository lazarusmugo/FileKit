<div align="center">
  <img src="https://github.com/vinceglb/FileKit/assets/24540801/5ad7fc2d-04fd-4ba7-b5de-b4a7ada753c9" alt="FileKit for Kotlin Multiplatform and Compose Multiplatform" />

  <br>
 
  <h1>FileKit</h1>
  <p>Files, Medias, Folder Picker and File saver library for Kotlin Multiplatform and Compose Multiplatform</p>

  <div>
    <img src="https://img.shields.io/maven-central/v/io.github.vinceglb/filekit-core" alt="FileKit Kotlin Maven Version" />
    <img src="https://img.shields.io/badge/Platform-Android-brightgreen.svg?logo=android" alt="Badge Android" />
		<img src="https://img.shields.io/badge/Platform-iOS%20%2F%20macOS-lightgrey.svg?logo=apple" alt="Badge iOS" />
		<img src="https://img.shields.io/badge/Platform-JVM-8A2BE2.svg?logo=openjdk" alt="Badge JVM" />
    <img src="https://img.shields.io/badge/Platform-WASM%20%2F%20JS-yellow.svg?logo=javascript" alt="Badge JS" />
  </div>

  <br>
</div>

FileKit is a library that allows you to pick and save files in a simple way. On each platform, it uses the native file picker API to provide a consistent experience.

## 🚀 Quick Start

Pick a file, a directory or save a file in common code:

```kotlin
// Pick a file
val file = FileKit.pickFile()

// Pick a directory
val directory = FileKit.pickDirectory()

// Save a file
val file = FileKit.saveFile(
    extension = "txt",
    bytes = "Hello, World!".encodeToByteArray()
)
```

Get file information in common code:

```kotlin
val filePath = file?.path
val fileName = file?.name
val bytes = file?.readBytes()
```

Compose Multiplatform integration made simple:

```kotlin
// Pick files from Compose
val launcher = rememberFilePickerLauncher(mode = PickerMode.Multiple()) { files ->
    // Handle picked files
}

// Use the pickerLauncher
Button(onClick = { launcher.launch() }) {
    Text("Pick files")
}
```

![FileKit Preview](https://github.com/vinceglb/FileKit/assets/24540801/e8a7bc49-41cc-4632-84c4-1013fd23dd76)

## 📦 Installation

```gradle
repositories {
    mavenCentral()
}

dependencies {
    // Enables FileKit without Compose dependencies
    implementation("io.github.vinceglb:filekit-core:0.8.8")

    // Enables FileKit with Composable utilities
    implementation("io.github.vinceglb:filekit-compose:0.8.8")
}
```

If using JVM target and Linux distribution, you need to add the following module:

```gradle
compose.desktop {
    application {
        nativeDistributions {
            linux {
                modules("jdk.security.auth")
            }
        }
    }
}
```

## ⚡ Initialization

Using **FileKit Core methods on Android** requires an initialization: 
 - `FileKit.pickFile()`
 - `FileKit.pickDirectory()`
 - `FileKit.saveFile()`

In this case, only if using Android, you need to initialize FileKit in your `ComponentActivity`:

```kotlin
// MainActivity.kt
class MainActivity : ComponentActivity() {
    override fun onCreate() {
        super.onCreate()
        FileKit.init(this)
    }
}
```

In all other cases, you can use FileKit without initialization.

## 📄 File Picker

### Picker types

You can pick different types of files with `PickerType`:
- `Image`: Pick an image file.
- `Video`: Pick a video file.
- `ImageAndVideo`: Pick an image or a video file.
- `File`: Pick any file. It is the default type. It's possible to specify a list of extensions.

```kotlin
val imageType = PickerType.Image
val videoType = PickerType.Video
val imageAndVideoType = PickerType.ImageAndVideo
val fileType = PickerType.File(extensions = listOf("pdf", "docx"))
```

### Picker modes

You can pick files in different modes with `PickerMode`. The mode will change the output type. `Single` is the default mode.

```kotlin
val singleMode = PickerMode.Single
val multipleMode = PickerMode.Multiple()
```

#### Max items

On Android and iOS, when using `PickerType` `Image`, `Video` or `ImageAndVideo`, we can use `PickerMode.Multiple(maxItems = X)` to limit the number of picked files. The value must be between 1 and 50. Default value is `null` (no limit).

### Launch the picker

You can launch the picker with `FileKit.pickFile` or `rememberFilePickerLauncher`:

```kotlin
// FileKit Core
val file = FileKit.pickFile(
    type = PickerType.Image,
    mode = PickerMode.Single,
    title = "Pick an image",
    initialDirectory = "/custom/initial/path"
)

// FileKit Compose
val launcher = rememberFilePickerLauncher(
    type = PickerType.ImageAndVideo,
    mode = PickerMode.Multiple(),
    title = "Pick a media",
    initialDirectory = "/custom/initial/path"
) { files ->
    // Handle the picked files
}
launcher.launch()
```

## 📁 Directory Picker

You can pick a directory with `FileKit.pickDirectory` or `rememberDirectoryPickerLauncher`:

```kotlin
// FileKit Core
val directory = FileKit.pickDirectory(
    title = "Pick a directory",
    initialDirectory = "/custom/initial/path"
)

// FileKit Compose
val launcher = rememberDirectoryPickerLauncher(
    title = "Pick a directory",
    initialDirectory = "/custom/initial/path"
) { directory ->
    // Handle the picked directory
}
launcher.launch()
```

The directory picker is available on all platforms, expect for WASM / JS. To check if the directory picker is available from the common code, you can use `FileKit.isDirectoryPickerSupported()`.

```kotlin
val directoryModeSupported = FileKit.isDirectoryPickerSupported()
```

## 💾 Save File Picker

You can save a file with `FileKit.saveFile` or `rememberFileSaverLauncher`:

```kotlin
// FileKit Core
val file = FileKit.saveFile(
    baseName = "myTextFile",
    extension = "txt",
    initialDirectory = "/custom/initial/path",
    bytes = "Hello, World!".encodeToByteArray()
)

// FileKit Compose
val launcher = rememberFileSaverLauncher() { file ->
    // Handle the saved file
}
launcher.launch(
    baseName = "myTextFile",
    extension = "txt",
    initialDirectory = "/custom/initial/path",
    bytes = "Hello, World!".encodeToByteArray()
)
```

### Optional bytes argument

Bytes argument is optional. If you don't provide it, the file will be empty. This feature is available on Android, iOS, macOS and JVM. It is not available on WASM / JS.

To check if it's possible to save a file without bytes from the common code, you can use:

```kotlin
val isSupported: Boolean = FileKit.isSaveFileWithoutBytesSupported()
```

## 🧑‍💻 PlatformFile and PlatformDirectory

The `PlatformFile` and `PlatformDirectory` classes are wrappers around the platform file system. It allows you to get the file name, path and read the file content in common code.

```kotlin
val platformFile: PlatformFile = ...

val filePath: String? = platformFile.path
val fileName: String = platformFile.name            // Base name with extension
val baseName: String = platformFile.baseName
val extension: String = platformFile.extension
val size: Long = platformFile.getSize()
val bytes: ByteArray = platformFile.readBytes()     // suspend function

val platformDirectory: PlatformDirectory = ...
val directoryPath: String? = platformDirectory.path
```

On each platform, you can get the original platform file:

```kotlin
// Android
val uri: Uri = platformFile.uri
val uri: Uri = platformDirectory.uri

// iOS / macOS
val nsUrl: NSURL = platformFile.nsUrl
val nsUrl: NSURL = platformDirectory.nsUrl

// JVM
val file: java.io.File = platformFile.file
val file: java.io.File = platformDirectory.file

// WASM / JS
val file: org.w3c.files.File = platformFile.file
val file: org.w3c.files.File = // PlatformDirectory not supported on WASM / JS
```

## 🕺 Going further with files

[KMPFile](https://github.com/zacharee/KMPFile) is a library built by [@zacharee](https://github.com/zacharee) that provides a common API to work with files in Kotlin Multiplatform. 

It mimics the Java File API available on Android, JVM, iOS and macOS. It's a great companion to FileKit to work with files in a consistent way across all platforms.

Also, KMPFile provides built-in support for FileKit's `PlatformFile` and `PlatformDirectory` classes thanks to [a dedicated library](https://github.com/zacharee/KMPFile?tab=readme-ov-file#picking-files).

Get started with KMPFile by visiting this repository: https://github.com/zacharee/KMPFile

## 🤏 Proguard & obfuscation

If using Proguard or obfuscation on JVM, you need to add the following rules:

```proguard
-keep class com.sun.jna.** { *; }
-keep class * implements com.sun.jna.** { *; }
```

## 🌱 Sample projects

You can find 2 sample projects in the `samples` directory:
- `sample-core`: A Kotlin Multiplatform project using FileKit in a shared viewModel targeting Android, JVM, WASM, JS, iOS Swift, macOS Swift and iOS Compose.
- `sample-compose`: A Compose Multiplatform project using FileKit in a Composable targeting Android, iOS, JVM, WASM, 

## ✨ Behind the scene

FileKit uses the native file picker API on each platform:

- On Android, it uses `PickVisualMedia`, `OpenDocument` and `OpenDocumentTree` contracts.
- On iOS, it uses both `UIDocumentPickerViewController` and `PHPickerViewController` APIs.
- On macOS, it uses the `NSOpenPanel` API.
- On JVM, it uses JNA to access the file system on Windows and macOS and XDG Desktop Portal on Linux.
- On WASM / JS, it uses the `input` element with the `file` type.

Also, FileKit uses the bear minimum of dependencies to be as lightweight as possible. 

FileKit Core uses the following libraries:
- [KotlinX Coroutines](https://github.com/Kotlin/kotlinx.coroutines)
- Only Android: [AndroidX Activity KTX](https://developer.android.com/jetpack/androidx/releases/activity)
- Only JVM: [Java Native Access - JNA](https://github.com/java-native-access/jna/tree/master)
- Only JVM: [XDG Desktop Portal](https://github.com/hypfvieh/dbus-java)

FileKit Compose uses the following libraries:
- [Jetbrains Compose Runtime](https://github.com/JetBrains/compose-multiplatform)
- Only Android: [AndroidX Activity Compose](https://developer.android.com/jetpack/androidx/releases/activity)

## 😎 Credits

FileKit is inspired by the following libraries:

- [compose-multiplatform-file-picker](https://github.com/Wavesonics/compose-multiplatform-file-picker)
- [peekaboo](https://github.com/onseok/peekaboo)
- [Calf](https://github.com/MohamedRejeb/Calf)
- [jnafilechooser](https://github.com/steos/jnafilechooser)
- [swing-jnafilechooser](https://github.com/DJ-Raven/swing-jnafilechooser)
- [nativefiledialog](https://github.com/mlabbe/nativefiledialog)
- [IFileDialogImp](https://github.com/dbwiddis/IFileDialogImp)
- [IntelliJ Community Foundation](https://github.com/JetBrains/intellij-community/blob/master/platform/util/ui/src/com/intellij/ui/mac/foundation/Foundation.java)
- [file_picker (flutter)](https://pub.dev/packages/file_picker)

---

Made with ❤️ by Vince
