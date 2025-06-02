# Best Practices for implementing Read Aloud (TTS) in Reading Applications

With the advent of the European Accessibility Act in June 2025, support for read aloud is becoming a mandatory feature for all reading applications. 

This repository is meant to document best practices for developers or for anyone interested in understanding the inner-workings of a read aloud feature. It won't target a specific platform, ideally making it suitable for both native and Web apps.

[Through a dedicated Github Project](https://github.com/users/HadrienGardeur/projects/2), this project also aims to identify issues that get in the way of implementing a good read aloud experience on various platforms.

# Overview

If you'd like to understand how Read Aloud works in reading applications, I've recorded a Webinar for the Book Industry Communication (BIC) on May 1st 2025 that covers this in full. The recording is [available on YouTube](https://www.youtube.com/watch?v=IjU-p9GqebQ&t=2298s) along with [the slides on Google Slides](https://docs.google.com/presentation/d/1atCe28C6wmiKKdGq990RtxHww4GQEg6Mv4Z1sHqO_lU/edit?usp=sharing).

# Steps involved

1. Extracting content from the resources of a publication
2. Breaking down or grouping elements together to generate a list of utterances
3. Pre-processing utterances to improve their readability
4. [Voice selection](voice-selection.md)
5. Managing playback and navigation
6. Highlighting the current utterance
