# Best Practices for implementing Read Aloud (TTS) in Reading Applications

With the advent of the European Accessibility Act in June 2025, support for read aloud is becoming a mandatory feature for all reading applications. 

This repository is meant to document best practices for developers or for anyone interested in understanding the inner-workings of a read aloud feature. It won't target a specific platform, ideally making it suitable for both native and Web apps.

Through a dedicated Github Project, this repo will also identify issues that get in the way of implementin a good read aloud experience on various platforms.

# Steps involved

While read aloud is often presentend as a fairly straightforward affordance for users, it's implementation is complex and involves many different steps:

1. Extracting content from the resources of a publication
2. Creating a tree-like structure from it
3. Breaking down or grouping elements together to generate an utterance tree
4. Pre-processing utterances to improve their readability
5. Voice selection ([Web](https://github.com/HadrienGardeur/web-speech-recommended-voices))
6. Highlighting the current utterance
7. Managing playback and navigation
