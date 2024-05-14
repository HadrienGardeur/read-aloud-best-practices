# Voice Selection

When selecting a voice, the following criteria need to be considered:

- [offline availability](#offline-availability)
- [language and regional preferences](#language-and-regional-preferences)
- [system defaults](#system-defaults)
- [voice quality](#voice-quality)
- along with [various other traits](#other-traits)

**Recommendations:**

- Reading applications should implement smart defaults that follow the criteria documented in this page.
- But users should always be free to select the voice that they prefer, ideally using a contextualized voice list to avoid displaying hundreds of voices.
- Voice preferences should be saved per language for all publications, to make sure that the user doesn't need to re-select a voice whenever they open a new publication.

## Offline availability

On most platforms, the best voices tend to require an online connection to manage the playback of an utterance.

While this can be great for the overall listening experience, relying strictly on online voices can be problematic:

- Users can be temporarily offline, which would restrict their ability to use the read aloud feature.
- If they're on a limited data plan, this could trigger additional costs or block the read aloud feature once the data cap has been reached.
- In an environment with a very high latency, the use of online voices could negatively impact the overall experience.

The following APIs return information about offline availability:

- On the Web, [`getVoices()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/getVoices) returns this information reliably in the [`localService`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisVoice/localService) property for each voice.
- On Android, this information is available as a method for [each voice](https://developer.android.com/reference/kotlin/android/speech/tts/Voice) using [`isNetworkConnectionRequired()`](https://developer.android.com/reference/kotlin/android/speech/tts/Voice#isNetworkConnectionRequired()). In addition, voices also include an estimated network latency (with 5 different values, from very low to very high).

In native apps on Apple devices, this informations is missing [from the fields describing a voice](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice#2857908) since all voices are available offline (as long as they have been downloaded).

**Recommendations:**

- Users should be able to easily identify voices that require a connection.
- If an application defaults to an online voice, it should also fallback automatically to an offline voice without requiring additional user input.
- Users should be free to easily disable all online voices, which should impact the default voice and the list of voices displayed to the user.
- 💡Should we also recommend a fallback to an offline voice if the user selected an online voice? If not, should the application warn the user that their preferred voice is unavailable offline?

## Language and regional preferences

[Extracting a base language for an EPUB](https://kb.daisy.org/publishing/docs/epub/language.html#publication-lang) can be surprisingly difficult:

- There's no difference between primary and secondary languages in its metadata which means that a bilingual publication (let's say in English and Spanish) can end up with the same metadata than an English publication with just a few paragraphs in Spanish.
- The order in which languages are listed isn't meaningful, but applications still need a rule to select a base language (usually the first language listed in its metadata).

💡Should we also cover the equivalent metadata for PDF?

While this base language and the language of each utterance can contain regional information, it's also important for applications to consider user preferences when listing voices or selecting a default voice.

On all platforms, language and regional preferences can be obtained through the following APIs:

- On the Web, this informations is provided by the browser to a remote server using the [`Accept-Language`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept-Language) header in HTTP and an ordered list of [BCP 47 language tags](https://datatracker.ietf.org/doc/html/rfc5646) (`en-US` for example). Web apps can also access this information using [`navigator.languages`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/languages).
- On Android, the [`Locale`](https://developer.android.com/reference/java/util/Locale) class can be called to return this information. Applications can have their own language setting in recent versions of Android, but it's best to use system settings instead since they can return multiple languages and/or regions.
- On Apple devices, a similar [`Locale`](https://developer.apple.com/documentation/foundation/locale#2891954) structure is available and can [return the user's preferred languages](https://developer.apple.com/documentation/foundation/locale#2891954). This is also available when working with [`AVSpeechSynthesisVoice`](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice) using the [`currentLanguageCode()`](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice/1619707-currentlanguagecode) method.

Similarly, all platforms provide language and regional information when listing voices:

- On the Web, [`getVoices()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/getVoices) returns this information slightly inconsistently in the [`lang`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisVoice/lang) property for each voice (related issues have been filed for [Chrome](https://github.com/HadrienGardeur/web-speech-recommended-voices/issues/13) and for [Firefox](https://github.com/HadrienGardeur/web-speech-recommended-voices/issues/17) on Android).
- On Android, this information is available as a method for [each voice](https://developer.android.com/reference/kotlin/android/speech/tts/Voice) using [`getLocale()`](https://developer.android.com/reference/kotlin/android/speech/tts/Voice#getLocale()).
- On Apple devices, this information is available as a property for [each voice](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice) under [`language`](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice/1619698-language). 

**Recommendations:**

- Applications should use the first language listed in `dc:language` as the base language of the publication.
- This base language must be overridable per utterance, [based on additional information extracted from the content](https://kb.daisy.org/publishing/docs/epub/language.html#lang-override).
- Since the base language can be missing or incorrectly documented, it's important to allow the user to override this information as well.
- When listing voices while reading a publication, applications should filter voices based on the base language set for the publication.
- 💡If there is no voice available for a language, what should be the fallback rule?
- When selecting a default voice for a language, applications should follow the user's regional preferences rather than the ones documented in the publication.
- If an application cannot match these preferences, it should rely on language-specific fallbacks rules. For example, if the device is set to `fr-BE` (French from Belgium) but there isn't a single voice available for Belgium, it could fallback to `fr-FR` (French from France) instead.
- If system preferences do not return any information for a given language, applications should also look into regional preferences for other languages. For example, if the device is set to `en-CA` (English from Canada), it could default to `fr-CA` (French from Canada) voices for French instead of that language's fallback.
- When listing voices, applications should group voices together using regions and display the user's preferred regions at the top.

## System defaults

…

## Voice quality

…

## Other traits

### Gender

…

### Children voices

…

### Novelty voices

…

### Personal voices

…