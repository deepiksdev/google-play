# Data safety declaration

A draft of Play Console's **Data safety** form for `io.deepiks.livia`, derived
from the Android source in
[deepiksdev/kotlin](https://github.com/deepiksdev/kotlin) so the answers match
what the app actually does rather than what it is assumed to do.

**This is an engineering inventory, not a legal sign-off.** Every row cites the
code it came from, so a claim can be checked. What the code cannot answer —
retention on the backend, what third-party processors keep — is collected under
[Decisions still needed](#decisions-still-needed); the form cannot be submitted
accurately until those are settled. Play treats a wrong answer here as a policy
violation, not a bug.

## What the app does not do

Worth stating first, because it removes whole sections of the form:

- **No analytics and no crash reporting.** Neither Firebase Analytics nor
  Crashlytics is a dependency. Logging is Timber, on-device only.
- **No advertising**, no ad SDK, no ad ID.
- **No location.** `ACCESS_FINE_LOCATION` / `COARSE` are not in the manifest.
- **No contacts**, no SMS, no call log, no system calendar read.
- **No third-party analytics or attribution SDKs of any kind.**

The full permission set is small: `INTERNET`, `ACCESS_NETWORK_STATE`,
`ACCESS_WIFI_STATE`, `ACCESS_LOCAL_NETWORK`, `RECORD_AUDIO`, `CAMERA`,
`MODIFY_AUDIO_SETTINGS`, `BLUETOOTH_CONNECT`, `POST_NOTIFICATIONS`,
`WAKE_LOCK`.

## The encryption story

Most user content in Firestore is **end-to-end encrypted before it leaves the
device**. Fields prefixed `__` are encrypted with the care receiver's data
encryption key, which only their paired caregivers hold
(`crypto/FieldCipher.kt`, `crypto/EncryptedFirestore.kt`,
`crypto/DataEncryptionKey.kt`). Feed photos live inside the encrypted document
rather than in Firebase Storage (`feed/FeedPost.kt`).

Backup is off entirely — `allowBackup="false"` plus `data_extraction_rules.xml`
excluding every domain — so the keys never leave the device.

Email passwords and OAuth tokens are held in Android Keystore-backed
`EncryptedSharedPreferences` (`email/EmailAccountStore.kt`) and are **never
transmitted**; IMAP and Gmail are contacted directly from the device.

This is worth declaring: Play asks whether data is encrypted in transit, and
the honest answer here is stronger than the usual "yes, TLS".

## Declaration by data type

"Collected" = leaves the device. "Shared" = reaches a third party — see the
decision on service providers below before finalising that column.

| Play category | Type | Collected | Purpose | Evidence |
| --- | --- | --- | --- | --- |
| Personal info | Name | Yes | App functionality — shown as the post author | `authorName` in `feed/FeedPost.kt` |
| Personal info | Email address | Yes | Account management — Firebase email-link and Google sign-in | `auth/AccountManager.kt` |
| Personal info | User IDs | Yes | App functionality — Firebase uid, reused as the RevenueCat `appUserID` | `auth/FirebaseIdentity.kt`, `subscription/SubscriptionManager.kt` |
| Financial info | Purchase history | Yes | App functionality — entitlement `livia_silver` and credit balance | `subscription/SubscriptionManager.kt`, `/api/credits` |
| Messages | Emails | Yes | App functionality — the assistant reads and sends mail on request | `tools/MailTool.kt` → `/api/agent/step` |
| Messages | Other in-app messages | Yes | App functionality — feed captions and assistant conversation | `feed/FeedPost.kt`, `agent/AgentStepLoop.kt` |
| Photos and videos | Photos | Yes | App functionality — the shared feed and grocery photos | `feed/`, `crypto/QrScanner.kt` (camera) |
| Audio | Voice or sound recordings | Yes | App functionality — streamed to the assistant while the mic is held | `audio/AudioPipeline.kt`, `live/LiveSocket.kt` → `/api/live` |
| Calendar | Calendar events | Yes | App functionality — the in-app calendar, not the system one | Firestore `calendars`, `tools/CalendarTools.kt` |
| App activity | Other user-generated content | Yes | App functionality — grocery lists, remembered facts | `tools/GroceryTools.kt`, `tools/MemoryTools.kt` |
| Device or other IDs | Device or other IDs | Yes | App functionality — FCM token for push | Firestore `tokens`, `push/` |
| App info and performance | Crash logs | **No** | — | No Crashlytics dependency |
| App info and performance | Diagnostics | **No** | — | No Analytics dependency |

For every row above, the honest answers to the two follow-up questions are:

- **Is this data required?** Yes for user IDs and purchase history. Optional for
  the rest — the app works without email, feed, calendar or voice, each being a
  feature the user opts into.
- **Is collection optional?** Yes, per feature, by not using it.

## Security section

| Question | Answer | Basis |
| --- | --- | --- |
| Is data encrypted in transit? | **Yes** | TLS throughout, plus field-level E2E encryption on Firestore content |
| Can users request data deletion? | **See below** | Needs a decision |

## Decisions still needed

The source cannot settle these. Each one blocks an accurate submission.

1. **Account deletion.** Play requires apps with account creation to offer
   in-app account deletion *and* a web URL to request it. Firebase Anonymous
   Auth creates an account on first launch, so this applies. Neither exists yet
   — this is a product gap the form will expose, not just a paperwork item.
2. **Privacy policy URL.** Required on the store listing and referenced by this
   form. Does one exist for Livia?
3. **Service providers and "sharing".** Audio, email content and conversation
   reach the Cloud Run backend and from there Google's Gemini API. Whether that
   counts as *sharing* under Play's definition, or as processing by a service
   provider on your behalf, depends on the contractual relationship and on what
   Gemini retains. The iOS listing in
   [deepiksdev/app-store](https://github.com/deepiksdev/app-store) took a
   position on the equivalent Apple question — mirror it for consistency unless
   there is a reason not to.
4. **Backend retention.** How long `/api/agent/step` and `/api/live` keep
   transcripts and audio determines whether the declaration can honestly say
   data is not retained. Only the backend can answer.
5. **The care receiver is often not the account holder.** A caregiver
   configures the device for someone else, who may not be in a position to
   consent for themselves. Nothing in the Data safety form captures this, but
   it is worth a look at whether the privacy policy addresses it.

## Sources

- [Provide information for Google Play's Data safety section](https://support.google.com/googleplay/android-developer/answer/10787469)
