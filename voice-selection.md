# Voice Selection

When selecting a voice, the following criteria need to be considered:

- [language and regional preferences](#language-and-regional-preferences)
- [voice quality](#voice-quality)
- [system defaults](#system-defaults)
- [offline availability](#offline-availability)
- along with [various other traits](#other-traits)

**Recommendations:**

- Reading applications should implement smart defaults that follow the criteria documented in this page.
- But users should always be free to select the voice that they prefer, ideally using a contextualized voice list to avoid displaying hundreds of voices.
- Voice preferences should be saved per language for all publications, to make sure that the user doesn't need to re-select a voice whenever they open a new publication.
- Additional language packs are usually hidden fairly deep in system settings, where users are not likely to find them. On platforms where deep linking into system settings is possible, it's a good idea to include a link from your read aloud settings to these additional voice packs.

## Language and regional preferences

[Extracting a base language for an EPUB](https://kb.daisy.org/publishing/docs/epub/language.html#publication-lang) can be surprisingly difficult:

- There's no difference between primary and secondary languages in its metadata which means that a bilingual publication (let's say in English and Spanish) can end up with the same metadata than an English publication with just a few paragraphs in Spanish.
- The order in which languages are listed isn't meaningful, but applications still need a rule to select a base language (usually the first language listed in its metadata).

💡Should we also cover the equivalent metadata for PDF?

While this base language and the language of each utterance can contain regional information, it's also important for applications to consider user preferences when listing voices or selecting a default voice.

On all platforms, language and regional preferences can be obtained through the following APIs:

- On the Web, this informations is provided by the browser to a remote server using the [`Accept-Language`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept-Language) header in HTTP and an ordered list of [BCP 47 language tags](https://datatracker.ietf.org/doc/html/rfc5646) (`en-US` for example). Web apps can also access this information using [`navigator.languages`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/languages).
- On Android and Chrome OS, the [`Locale`](https://developer.android.com/reference/java/util/Locale) class can be called to return this information. Applications can have their own language setting in recent versions of Android, but it's best to use system settings instead since they can return multiple languages and/or regions.
- On Apple devices, a similar [`Locale`](https://developer.apple.com/documentation/foundation/locale#2891954) structure is available and can [return the user's preferred languages](https://developer.apple.com/documentation/foundation/locale#2891954). This is also available when working with [`AVSpeechSynthesisVoice`](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice) using the [`currentLanguageCode()`](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice/1619707-currentlanguagecode) method.

Similarly, all platforms provide language and regional information when listing voices:

- On the Web and applications based on Web technologies, [`getVoices()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/getVoices) returns this information slightly inconsistently in the [`lang`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisVoice/lang) property for each voice (related issues have been filed for [Chrome](https://github.com/HadrienGardeur/web-speech-recommended-voices/issues/13) and for [Firefox](https://github.com/HadrienGardeur/web-speech-recommended-voices/issues/17) on Android).
- On Android and Chrome OS, this information is available as a method for [each voice](https://developer.android.com/reference/kotlin/android/speech/tts/Voice) using [`getLocale()`](https://developer.android.com/reference/kotlin/android/speech/tts/Voice#getLocale()).
- On Apple devices, this information is available as a property for [each voice](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice) under [`language`](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice/1619698-language). 

**Recommendations:**

- Applications should use the first language listed in `dc:language` as the base language of the publication.
- This base language must be overridable per utterance, [based on additional information extracted from the content](https://kb.daisy.org/publishing/docs/epub/language.html#lang-override).
- Since the base language can be missing or incorrectly documented, it's important to allow the user to override this information as well.
- When listing voices while reading a publication, applications should filter voices based on the base language set for the publication.
- 💡If there is no voice available for a language, what should be the fallback rule?
- When selecting a default voice for a language, applications should follow the user's regional preferences rather than the ones documented in the publication.
- If an application cannot match these preferences, it should rely on language-specific fallbacks rules. For example, if the device is set to `fr-BE` (French from Belgium) but there isn't a single voice available for Belgium, it could fallback to `fr-FR` (French from France) instead.
- If system preferences do not return any information for a given language, applications should also look into regional preferences for other languages. For example, if a device is set to `en-CA` (English from Canada), an application could default to `fr-CA` (French from Canada) voices for French instead of that language's fallback (`fr-FR` using the previous example).
- When listing voices, applications should group voices together using regions and display the user's preferred regions at the top.

## Voice quality

With the advent of voices using machine learning (ML), quality is constantly going up at a rapid pace.

With new hardware dedicated to these tasks on all platforms, these high quality voices are also becoming capable of running on device rather than strictly through cloud services.

At the other end of the spectrum, some platforms continue to embed very low quality voices by default:

- Eloquence voices on Apple devices
- eSpeak voices on Chrome OS

Unfortunately, the quality of each voice is not always available or in some cases, poorly documented:

- On the Web and applications based on Web technologies, [`getVoices()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/getVoices) doesn't return any information about voice quality.
- On Android and Chrome OS, this information is available as a method for [each voice](https://developer.android.com/reference/kotlin/android/speech/tts/Voice) using [`getQuality()`](https://developer.android.com/reference/kotlin/android/speech/tts/Voice#getQuality()) and it [returns five different values](https://developer.android.com/reference/kotlin/android/speech/tts/Voice#quality_low).
- On Apple devices, this information is available as a property for [each voice](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice) under [`quality`](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice/1619688-quality) and it [returns three different values](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoicequality).
- Unfortunately, Apple returns the same quality value (`default`) for all pre-loaded voices on their devices, even though the quality of these voices can vary greatly.

In order to assess the quality of some of these voices, applications need to either infer this information (from an identifier or a voice name) or fully document what's available on each platform.

[A separate project](https://github.com/HadrienGardeur/web-speech-recommended-voices) is dedicated to this task for Web applications or desktop/mobile apps built on top of Web technologies.

**Recommandations:**

- Applications should default to the highest quality voice available for a given language/region.
- When listing voices, applications should sort them by voice quality once they've grouped them by region.
- Very low quality voices can be disruptive, even with proper defaults. If anything else is available, it's best to filter them out of a voice list.
- On Apple devices specifically, Eloquence voices should be filtered out since they outnumber remaining voices available by default (8 Eloquence voices for 2 "normal" voices by default in French for example).
- Web applications should rely on recommended voices, as documented [in this separate project](https://github.com/HadrienGardeur/web-speech-recommended-voices).

## System defaults

On every platform, users can define a system default for voice selection. These system defaults usually require to select:

- a language
- and a voice for that language

On some platforms, there's also a concept of engine, where the list of voices depends on the engine selected for playback.

These system defaults tend to be hidden pretty deep in system settings under accessibility features, which lower the chance of users encountering them. They're usually grouped together with the ability to install additional voice packs, either to extend the number of languages supported or to add offline support for higher quality voices.

Identifying these system defaults can also be harder than expected. 

- On the Web and applications based on Web technologies, [`getVoices()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/getVoices) returns this information using [`default`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisVoice/default) with some noticeable inconsistencies:

- On Safari, every single voice is incorrectly identified as being the default one ([related issue](https://github.com/HadrienGardeur/web-speech-recommended-voices/issues/16)).
- While on Chrome for Android, none of them are identified as the default voice ([related issue](https://github.com/HadrienGardeur/web-speech-recommended-voices/issues/16)).

On Android and on Apple devices, there is no concept of default voice when fetching a list of voices but there are other APIs available:

- On Android and Chrome OS, [`getDefaultVoice()`](https://developer.android.com/reference/kotlin/android/speech/tts/TextToSpeech#getVoices()) can be called on the [`TextToSpeech`](https://developer.android.com/reference/kotlin/android/speech/tts/TextToSpeech) class to return the system default
- On Apple devices, [`AVSpeechSynthesisVoice`](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice) can be [initialized with a language code](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice/1619699-init) to return the platform default voice for each language/region (as defined by Apple rather than by the user).

Overall this means that on Apple devices, the system defaults (as selected by the user) are impossible to fetch since none of the APIs return this information.

On Android and Chrome OS, things are slightly better, but system defaults are only useful if they match the base language of the publication.

Finally, it's worth pointing out that there's no easy way to know whether the system default was truly set by the user or if it's the platform's default for a given language.

Even if the user installs additional voice packs with higher quality voices, it doesn't automatically overrides the platform defaults either.

**Recommandations:**

- All in all, system defaults are hardly the most useful criteria to take into account and should be simply displayed as an option among others in the voice list.
- Applications should never use the system default voice without checking its language first and making sure that it's equal to the current utterance's language.
- Ideally, all applications should be aware of platform defaults to further identify if the system defaults were truly set by the user.
- Web applications should fully ignore system defaults if they detect multiple voices that identify themselves as the default voice ([related issue](https://github.com/HadrienGardeur/web-speech-recommended-voices/issues/16)) for any given language.

## Offline availability

On most platforms, the best voices tend to require an online connection to manage the playback of an utterance.

While this can be great for the overall listening experience, relying strictly on online voices can be problematic:

- Users can be temporarily offline, which would restrict their ability to use the read aloud feature.
- If they're on a limited data plan, this could trigger additional costs or block the read aloud feature once the data cap has been reached.
- In an environment with a very high latency, the use of online voices could negatively impact the overall experience.

The following APIs return information about offline availability:

- On the Web and applications based on Web technologies, [`getVoices()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/getVoices) returns this information reliably in the [`localService`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisVoice/localService) property for each voice.
- On Android and Chrome OS, this information is available as a method for [each voice](https://developer.android.com/reference/kotlin/android/speech/tts/Voice) using [`isNetworkConnectionRequired()`](https://developer.android.com/reference/kotlin/android/speech/tts/Voice#isNetworkConnectionRequired()). In addition, voices also include an estimated network latency (with 5 different values, from very low to very high).

In native apps on Apple devices, this informations is missing [from the fields describing a voice](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice#2857908) since all voices are available offline.

**Recommendations:**

- Users should be able to easily identify voices that require a connection.
- If an application defaults to an online voice, it should also fallback automatically to an offline voice without requiring additional user input.
- Users should be free to easily disable all online voices, which should impact the default voice and the list of voices displayed to the user.
- 💡Should we also recommend a fallback to an offline voice if the user selected an online voice? If not, should the application warn the user that their preferred voice is unavailable offline?

## Other traits

### Gender

…

### Children voices

…

### Novelty voices

On Apple devices, novelty voices (Effects) are preloaded by default by the system, mostly for historical reasons. These 15 voices are only available in English, but in typical Apple fashion, their names are localized based on the system language.

Since these voices provide no value whatsoever when using read aloud, they should be filtered out of a voice list displayed to the user.

When using native APIs, Apple identifies these voices using [voice traits](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice/traits) and provides [`isNoveltyVoices`](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice/traits/4134513-isnoveltyvoice) as a method for inspecting voices.

On the Web and in applications based on Web technologies, there's no easy way to detect them, aside from knowing their localized `name` returned by [`getVoices()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/getVoices).

This might be covered in a future update of [the project dedicated to recommended voices on the Web](https://github.com/HadrienGardeur/web-speech-recommended-voices).

### Personal voices

…

## Resources

- [Recommended voices for the Web Speech API](https://github.com/HadrienGardeur/web-speech-recommended-voices)
- [List of voices available on Windows 10/11](https://support.microsoft.com/en-us/windows/appendix-a-supported-languages-and-voices-4486e345-7730-53da-fcfe-55cc64300f01)