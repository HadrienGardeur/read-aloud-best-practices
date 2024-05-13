# Voice Selection

When selecting a voice, the following criteria need to be considered:

- [offline availability](#offline-availability)
- [language and regional preferences](#language-and-regional-preferences)
- [voice quality](#voice-quality)
- along with various other traits

**Recommendations:**

- Reading applications should implement smart defaults that follow the criteria documented in this page.
- But users should always be free to select the voice that they prefer.
- A voice list should ideally be filtered based on some of these criteria as well to avoid displaying hundreds of voices to the user.


## Offline availability

On most platforms, the best voices tend to require an online connection to manage the playback of an utterance.

The following API provide this information:

- On the Web, [`getVoices()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/getVoices) returns this information reliably using a boolean in [`localService`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisVoice/localService).
- On Android, this information is also returned as a method for each voice using [`isNetworkConnectionRequired()`](https://developer.android.com/reference/android/speech/tts/Voice#isNetworkConnectionRequired()).
- On Apple devices, this informations is missing [from the fields describing a voice](https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice#2857908) since all voices are available offline (as long as they have been downloaded).

**Recommendations:**

- Users should be able to easily identify and/or filter voices that require a connection.
- If an application defaults to an online voice, it should also fallback automatically to an offline voice without requiring additional user input.
- 💡Should we also recommend a fallback to an offline voice if the user selected an online voice? If not, should the application warn the user that their preferred voice is unavailable offline?

## Language and regional preferences

On all platforms, language and regional preferences can be obtained through an API:

- On the Web, this informations is provided by the browser to a remote server using the [`Accept-Language`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept-Language) header in HTTP and an ordered list of [BCP 47 language tags](https://datatracker.ietf.org/doc/html/rfc5646) (`en-US` for example). Web apps can also access this information using [`navigator.languages`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/languages).

## Voice quality