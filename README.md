# Holy Bible (Android)

An offline-first, family-friendly King James Version (KJV) Holy Bible app for Android built with **Kotlin** and **Jetpack Compose**, designed for both kids and adults with chapter illustrations, independent daily random readings, verse highlighting, and bookmarks.

---

## Features

- **Full 66-Book KJV Bible (Offline):** Loads all 39 Old Testament and 27 New Testament books (1,189 chapters) locally from `assets/bible.json` with zero internet dependency.
- **Independent Reading Modes:**
    - **Continue Reading:** Automatically saves and resumes your exact book and chapter progress.
    - **Random Chapter for Today:** Picks a daily random chapter that stays locked until you complete it, without overwriting your main reading progress.
- **Illustrated Chapter Headers (Kids & Adults):**
    - Displays bundled chapter artwork from `app/src/main/assets/illustrations/<book>/` (e.g., `genesis_chapter_1.jpg`).
    - Optionally generates family-friendly chapter illustrations in the background via OpenAI (`dall-e-3`), caches them permanently to internal storage (`filesDir/BibleIllustrations/`), and hides the image container cleanly when offline.
    - Includes a one-tap toolbar toggle to show or hide illustrations anytime.
- **Fast Navigation & Search:**
    - **Table of Contents Sheet:** Segmented Old/New Testament switcher, instant book search bar, expandable chapter grids, and star badges on illustrated story chapters.
    - **One-Tap Recovery Pill:** Quick `Previous: <Book> <Chapter>` pill at the top of the reader in case of accidental taps on *Next Chapter*.
- **Verse Highlighting & Bookmarks:** Tap any verse to highlight it in Yellow, Green, Blue, or Pink, and bookmark favorite chapters for quick access.

---

## Architecture & Project Structure

The project uses the **Factory Method** design pattern (`ChapterFactory.kt`) to decouple JSON parsing and fallback sample data from the UI layer:

```text
app/src/main/
├── AndroidManifest.xml
├── assets/
│   ├── bible.json                       # Complete 66-book KJV Bible dataset
│   └── illustrations/
│       └── genesis/
│           ├── genesis_chapter_1.jpg    # Chapter artwork (<book>_chapter_<num>.jpg)
│           └── ...
├── res/
│   ├── drawable/
│   │   └── bible_background.jpg         # Home screen cover artwork
│   └── mipmap-*/                        # App launcher icons
└── java/com/shebinvins/holybible/
    ├── MainActivity.kt                  # Entry point & navigation state
    ├── models/
    │   ├── BibleModels.kt               # Testament, BibleChapter, BibleVerse
    │   └── HighlightColor.kt            # Verse highlight palette
    ├── factories/
    │   └── ChapterFactory.kt            # Factory Method (JsonChapterCreator & SampleChapterCreator)
    ├── services/
    │   ├── BibleDatabase.kt             # Singleton chapter index & prev/next lookup
    │   ├── BibleStore.kt                # SharedPreferences persistence for progress, bookmarks & highlights
    │   ├── KidsIllustrationHelper.kt    # Local asset artwork loader & story captions
    │   └── OpenAIImageService.kt        # Background coroutine image generator & permanent disk cache
    └── ui/
        ├── HomeScreen.kt                # Cover screen with Continue & Random buttons
        ├── ChapterReaderScreen.kt       # Verse reader, artwork header, toolbar & navigation
        ├── TestamentBookPickerSheet.kt  # Searchable Old/New Testament Table of Contents
        ├── HighlightPickerSheet.kt      # Verse color picker bottom sheet
        └── BookmarksListSheet.kt        # Saved bookmarked chapters sheet
```

---

## Prerequisites

- **Android Studio:** Ladybug (2024.2.1) or newer
- **JDK:** Java 17+
- **Android SDK:** Compile SDK 35 / Minimum SDK 24 (Android 7.0+)
- **Device / Emulator:** Physical Android phone (USB Debugging enabled) or Android Emulator

---

## How to Run the Application

### 1. Clone the Repository
```bash
git clone https://github.com/vshebin/holyBibleAndroid.git
cd holyBibleAndroid
```

### 2. Verify Assets & Resources
Ensure the following files are present before building:
- `app/src/main/assets/bible.json` — Full KJV Bible JSON array (if omitted, the app automatically falls back to `SampleChapterCreator`).
- `app/src/main/res/drawable/bible_background.jpg` — Home screen background image.
- `app/src/main/assets/illustrations/<book>/<book>_chapter_<number>.jpg` — Optional bundled chapter illustrations (all lowercase filenames).

### 3. (Optional) Enable AI Chapter Illustrations
To enable automatic background artwork generation for chapters without local images:
1. Open `app/src/main/java/com/shebinvins/holybible/services/OpenAIImageService.kt`.
2. Replace `"YOUR_OPENAI_API_KEY"` with your OpenAI API key:
   ```kotlin
   private const val API_KEY = "sk-..."
   ```
   *(Note: Never commit a live API key to a public repository.)*

### 4. Build & Launch in Android Studio
1. Open **Android Studio** -> **File -> Open** -> select the `holyBibleAndroid` folder.
2. Wait for **Gradle Sync** to finish downloading dependencies (including `androidx.compose.material:material-icons-extended`).
3. Select an Android Emulator or connected physical Android device from the top device dropdown.
4. Click **Run** (`Ctrl + R` on macOS / `Shift + F10` on Windows/Linux).

---

## Adding More Chapter Illustrations Later

To add artwork for additional books without changing any Kotlin code:
1. Create a lowercase folder for the book inside `app/src/main/assets/illustrations/` (e.g., `exodus/` or `luke/`).
2. Name each image using the pattern `<book>_chapter_<number>.jpg` (e.g., `exodus_chapter_14.jpg`, `luke_chapter_2.jpg`).
3. Rebuild the app—`KidsIllustrationHelper` will automatically detect and display the new artwork.
