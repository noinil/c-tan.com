+++
title = "Using Azure AI Services for Productivity Boosts (TTS, STT, and Translation)"
description = "Let me share some fun tools I've recently created using Azure AI.  It's a good way to embrace the convenience that AI brings."
date = "2024-08-25T12:00:00"
categories = ["technique"]
tags = ["AI", "TTS", "STT", "AI Translation"]

slug = "azure-tts-stt-translation-2024"
summary = "Reading papers and attending international conferences often demand long time of focused comprehension in a foreign language.  For lazy language learners (like me), why not take advantage of AI's incredible capabilities to give your brain a bit of a break?"
+++


I recently attended the ACS Fall 2024 in Denver. During the late-night hours when I was wide awake, trying to overcome jet lag, I thought it would be a good idea to work on some tasks that don't require deep thinking, just enough to help bring on the next wave of sleepiness.

After a few nights of tinkering, I managed to implement the following features:

- Reading aloud selected text after pressing a shortcut key;
- Recognizing speech from the microphone, recording it, and providing a Chinese translation;
- Translating selected text from any language and displaying the translation in the system's notification center (MacOS).

As prerequisites, you'll need to sign up for Microsoft Azure and create some "Resources".  Locally, you'll need to install the appropriate SDKs (you can check the specific libraries imported in the code).


## Text to Speech (TTS)

Obtain the 'key' and 'region' information from the Azure Speech Services API you created and set them in the environment variables:
```bash
export TTS_KEY='Minim proident ullamco deserunt'
export TTS_REGION='Consequat excepteur'
```

The following python script works well in MacOS:
```python
#!/usr/bin/env python

import os
import time
import subprocess
import azure.cognitiveservices.speech as speechsdk
from pynput import keyboard

# set up Azure Speech information
speech_config = speechsdk.SpeechConfig(subscription=os.environ.get('TTS_KEY'), region=os.environ.get('TTS_REGION'))
speech_config.speech_synthesis_voice_name='en-US-EmmaMultilingualNeural'
audio_config = speechsdk.audio.AudioOutputConfig(use_default_speaker=True)
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

def get_selected_text():
    try:
        applescript = '''
        tell application "System Events"
            keystroke "c" using {command down}
            delay 0.1
            set selectedText to the clipboard
        end tell
        return selectedText
        '''
        result = subprocess.run(['osascript', '-e', applescript], stdout=subprocess.PIPE)
        return result.stdout.decode('utf-8').strip()
    except Exception as e:
        print(f"Error: {e}")
        return ""

def on_activate():
    time.sleep(0.5)
    text = get_selected_text()
    speech_synthesis_result = speech_synthesizer.speak_text_async(text).get()
    if speech_synthesis_result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
        print("Speech synthesized successfully.")
    elif speech_synthesis_result.reason == speechsdk.ResultReason.Canceled:
        print("Speech synthesis canceled:", speech_synthesis_result.cancellation_details.reason)
        if speech_synthesis_result.cancellation_details.error_details:
            print("Error details:", speech_synthesis_result.cancellation_details.error_details)

def on_exit():
    print("Exiting program...")
    h.stop()

with keyboard.GlobalHotKeys({
        '<cmd>+r': on_activate,
        '<cmd>+k': on_exit
}) as h:
    h.join()
```

After selecting a piece of foreign text, pressing `Cmd + r` will read it aloud, while pressing `Cmd + k` will exit the program.

Here are some fun observations from testing, mainly by tweaking the AI voice settings:
- Azure currently offers a variety of entertaining dialects, such as Northeastern, Shaanxi, and Shandong accents, which add a humorous effect.
- One of Azure's Mandarin voices pronounces 'です' in Japanese with a "retroflex" sound, making it sound like 'で事.'
- In most cases, mixing different languages within the same sentence works fine, but the combination of Chinese and Japanese still seems a bit tricky, often resulting in incorrect interpretations.


## Speech to Text (STT)

In theory, there's no need to create a separate Azure resource, since we already have one set up for TTS.  However, considering that TTS can take advantage of the free tier, which is insufficient for STT (only 5 hours per month), it's a good idea for frequent users to create a new resource specifically for STT.

Additionally, I added a translation feature to my STT program, so it also shows translation results alongside the speech input.  This requires creating a Text Translation API in Azure.

I'll skip the steps for adding environment variables and jump straight into the program:
```python
#!/usr/bin/env python

import os
import azure.cognitiveservices.speech as speechsdk
from azure.ai.translation.text import TextTranslationClient
from azure.core.credentials import AzureKeyCredential

# language auto detection
auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["zh-CN", "ja-JP", "en-US", "ko-KR"])

speech_config = speechsdk.SpeechConfig(subscription=os.environ.get('STT_KEY'), region=os.environ.get('STT_REGION'))
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, auto_detect_source_language_config=auto_detect_source_language_config)

# set up translation service
translator_key = os.environ.get('TRANSLATOR_KEY')
translator_region = os.environ.get('TRANSLATOR_REGION')

translator_credential = AzureKeyCredential(translator_key)
translator_client = TextTranslationClient(credential=translator_credential, region=translator_region)

# set your target language
target_language = "zh"

# event handling: every time a new speech is detected
def recognized_callback(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # print(f"Recognition:  {evt.result.text}")
        recognized_text = evt.result.text
        print(f"识别结果: {recognized_text}")

        # translation
        try:
            response = translator_client.translate(
                body=[recognized_text],
                to_language=[target_language]
            )
            translation = response[0] if response else None
            translated_text = translation.translations[0].text
            print(f"Translation result:  {translated_text}")
        except Exception as e:
            print(f"Translation error: {str(e)}")

    elif evt.result.reason == speechsdk.ResultReason.NoMatch:
        print("未能识别任何内容。")

# event handling: cancel or error
def canceled_callback(evt):
    print(f"Recognition error: {evt.reason}")
    if evt.reason == speechsdk.CancellationReason.Error:
        print(f"Recognition error: {evt.error_details}")

# event binding
speech_recognizer.recognized.connect(recognized_callback)
speech_recognizer.canceled.connect(canceled_callback)

# start the continuous recognition
speech_recognizer.start_continuous_recognition()

print("语音识别已启动，请开始说话...")

# wait until cancel
try:
    while True:
        pass
except KeyboardInterrupt:
    print("Recognition stopped")
    speech_recognizer.stop_continuous_recognition()
```

目前有这样一些瑕疵：
- 我创建 `speech_recognizer` 的方式导致同一个翻译服务只在最开始的时候尝试确认录入语音的语言， 这在接下来的一系列语音识别中是不会变化的。  比如第一句接收到的话如果是 "こんにちは" 的话， 那么接下来听到的所有话都会被尝试翻译为日语。
- 我在 ACS Fall (Colorado convention center) 的会场中试了一下收录演讲者们的声音， 但是嘈杂的空调声常常让我的电脑识别不到任何内容。 
- 即便收音效果良好， 识别出来的内容也很受演讲者状态的影响， 如果碰到口吃或者紧张不连贯等情况， 就会得到出乎意料的结果。


There are currently a few issues:
- The way I create the `speech_recognizer` causes the translation service to only determine the input language at the very beginning, and this doesn't change for the subsequent speech recognition.  For example, if the first phrase detected is 'こんにちは', all following inputs will be treated as Japanese for recognition and translation.
- I tested the program at the ACS Fall conference (Colorado Convention Center) to capture the speakers' voices, but the noisy air conditioning often prevented my computer from recognizing anything.
- Even with good audio capture, the recognition results can be heavily influenced by the speaker's delivery.  Issues like stuttering or nervous, disjointed speech can lead to unexpected outcomes.



## Text Translation

The translation feature was actually demonstrated in the previous section.  Readers can easily extract the relevant part from the earlier Python script.

Here, I'd like to briefly discuss the integration with Alfred.  By using Alfred's "Universal Action" feature, you can also directly capture selected text, translate it, and display the results.  This effectively saves the extra development effort of interacting with the system clipboard and notification center.

A python script for the basic translation function:
```python
#!/usr/bin/env python

import os
import sys
from azure.ai.translation.text import TextTranslationClient
from azure.core.credentials import AzureKeyCredential


translator_key = os.environ.get('TRANSLATOR_KEY')
translator_region = os.environ.get('TRANSLATOR_REGION')

translator_credential = AzureKeyCredential(translator_key)
text_translator = TextTranslationClient(credential=translator_credential, region=translator_region)

selected_text = sys.argv[1]

try:
    to_language = ["zh"]
    input_text_elements = [selected_text]

    response = text_translator.translate(body=input_text_elements, to_language=to_language)
    translation = response[0] if response else None

    if translation:
        detected_language = translation.detected_language
        for translated_text in translation.translations:
            print(f"{translated_text.text}")

except HttpResponseError as exception:
    if exception.error is not None:
        print(f"Error Code: {exception.error.code}")
        print(f"Message: {exception.error.message}")
```


Next, let's leave the system interactions to Alfred:

- In the Alfred "Workflows" interface, add a new "Blank Workflow".
- In the workflow panel, add a "Universal Action" and set it to work only with text.
- In the workflow panel, add a script.  I'm using a "zsh" script here because running Python requires setting up a specific virtual environment, which is more complicated.  The script content is as follows:
```python
query=$1

$PYTHON_VENV_PATH/bin/python $PY_SCRIPT/test_translation_alfred.py "$query"
```
- In the workflow panel, add a "Post Notification". Set the title to "Translation Results", and the text to `{query}`.  This will output the translation result directly to the system's notification center.

