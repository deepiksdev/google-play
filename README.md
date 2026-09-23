# google-play

Google Play publishing for **Livia** — the Android app in
[deepiksdev/kotlin](https://github.com/deepiksdev/kotlin), package
`io.deepiks.livia`. The Play Console counterpart to
[deepiksdev/app-store](https://github.com/deepiksdev/app-store).

## keys/

The whole directory is git-ignored, because both files in it are secret and
neither can ever be committed:

| File | What it is |
| --- | --- |
| `livia-upload.jks` | The **upload key** for Play App Signing. Play re-signs every build with its own app-signing key, so this one only proves an upload is really ours. |
| `key.properties` | The keystore's passwords and alias, plus `storeFile=livia-upload.jks` resolved beside it. Read by the Android build. |

Nothing tracked in this repo references them, so a fresh clone has no `keys/`
at all. Get both files from the team password manager and drop them in.

The upload certificate's fingerprints — not secret, and Play Console shows the
same pair under **Setup → App integrity**:

```
SHA-1    FB:95:82:87:95:04:D4:97:0D:FD:79:1A:46:9F:5C:CF:DD:C7:35:82
SHA-256  7D:4A:F8:C1:65:BD:5B:CA:0B:8B:DB:0C:8B:53:9C:A8:0F:FA:EC:59:00:69:21:1F:A2:7F:91:CC:72:A3:EA:D9
```

Do **not** register these with Firebase for Google Sign-In or App Check. Builds
reaching devices are signed by Google's app-signing key, so those need the
*app-signing* fingerprints from Play Console instead. Add these as well only to
cover release builds installed directly rather than through Play.

## screenshots/

Store listing screenshot candidates, captured from the Galaxy Tab A9 on the
API 36 build. Valid to upload, but 800 px on the short side, so they do not
reach the 1080 px Play wants for large-screen recommendations — see
[`screenshots/README.md`](screenshots/README.md).

## data-safety.md

A draft of Play Console's Data safety form, derived from the Android source so
each answer cites the code behind it. Not submitted — it ends with the
questions the source cannot answer, including account deletion, which is a
product gap rather than paperwork.

## How the Android build finds them

`app/build.gradle.kts` in `deepiksdev/kotlin` reads `keys/key.properties` from
this repo, assuming the two are checked out side by side:

```
~/GitHub/
├── google-play/
│   └── keys/{livia-upload.jks,key.properties}
└── kotlin/
```

Anywhere else, point at the directory explicitly:

```sh
./gradlew bundleRelease -PliviaSigningDir=/path/to/keys
LIVIA_SIGNING_DIR=/path/to/keys ./gradlew bundleRelease
```

Without the files the build still configures and compiles — `bundleRelease` is
simply unsigned, and only `assembleDebug` is installable.

```sh
cd ../kotlin
./gradlew bundleRelease
# → app/build/outputs/bundle/release/app-release.aab
keytool -printcert -jarfile app/build/outputs/bundle/release/app-release.aab
```

The bundle is what Play Console takes; `versionCode` must increase on every
upload.

## Not here yet

- The **service-account JSON** for the Play Developer API, which RevenueCat
  needs to validate purchases server-side. It belongs in `keys/` when created.
- The **Play product catalog** — a subscription `livia_silver` with `monthly`
  and `yearly` base plans, and the consumables `credits_500` / `credits_2000`,
  mirroring what
  [deepiksdev/revenue-cat](https://github.com/deepiksdev/revenue-cat) already
  wires up for the App Store. Play models subscriptions as one product with
  several base plans, so RevenueCat's store identifiers read
  `livia_silver:monthly`, not a product per duration.
