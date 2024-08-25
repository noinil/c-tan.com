+++
title = "利用 Azure 的 AI 服务做一些提高效率的小事情"
description = "分享一下最近使用 Azure AI 所做的好玩的工具。  顺势享受 AI 带来的便捷。"
date = "2024-08-25T12:00:00"
categories = ["technique"]
tags = ["AI"]

slug = "azure-tts-stt-translation-2024"
summary = "论文的阅读， 海外会议的参加， 常常对长时间的外文理解专注力提出挑战。  像我这样的懒惰型语言学习者， 不妨借助 AI 的神奇效能， 放松一下自己的大脑。"
+++

最近在 Denver 参加 ACS Fall 2024.  在尝试适应时差的深夜清醒时段中， 我觉得应该找一些不需要深刻思考能力的小事情来折腾一下， 好加速下一次睡意的降临。

经过几个夜晚的折腾， 我实现了下面这些功能：
1. 按下快捷键之后读出选中的文字；
2. 识别麦克风收到的语音并记录下来， 同时给出中文翻译；
3. 翻译选中的任意语言的文字， 在系统的 notification center 中显示。
下面是简单的介绍。

作为依赖， 读者需要开通 Microsoft Azure 并自己创建一些 resources.  在本地需要安装对应的 SDK (可以查看具体代码中导入的库).

## Text to Speech (TTS)

从创建的 Azure SpeechServices API 中获得 "key" 和 "region" 信息， 写到 environment variables 中：
```bash
export TTS_KEY='Minim proident ullamco deserunt'
export TTS_REGION='Consequat excepteur'
```

下面的脚本可以在 MacOS 中运行：
```python
#!/usr/bin/env python

import os
import time
import subprocess
import azure.cognitiveservices.speech as speechsdk
from pynput import keyboard

# 设置 Azure Speech 服务
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
        print(f"获取选中内容时出错: {e}")
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
这样的话， 在选中某段外文之后， 按下 `Cmd + r` 之后就可以听到朗读； 而按下 `Cmd + k` 之后则退出程序。  

有一些比较好玩的测试经验， 主要是通过修改 AI 声音得到：
- Azure 目前还提供了各种有趣的方言， 比如东北、 陕西、 山东等等， 十分有喜剧效果。
- Azure 的某个普通话语音， 在读日文的时候， 会将 "です" 读出翘舌效果， 听起来像 "で事"。
- 大部分情况下在同一句话中夹杂各种语言都已经不是问题， 但是中日混合好像还不是很灵活， 常常无法做出正确的判断。


## Speech to Text (STT)

理论上来说并不需要单独创建一个新的 Azure Resource, 因为我们已经为 TTS 创建了一个。  不过考虑到 TTS 其实可以使用免费额度， 而这对于 STT 来说显得很不够用 (每个月才 5 小时)， 因此高频使用者新建立一个 resource 是个不错的主意。 

另外， 我给我的 STT 程序加上了一个翻译的功能， 这样在语音录入的同时还能看到翻译结果。  这需要在 Azure 中再创建一个 TextTranslation API.

这里我就省略添加 environment variables 的步骤， 直接进入程序啦：
```python
#!/usr/bin/env python

import os
import azure.cognitiveservices.speech as speechsdk
from azure.ai.translation.text import TextTranslationClient
from azure.core.credentials import AzureKeyCredential

# 配置自动语言检测
auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["zh-CN", "ja-JP", "en-US", "ko-KR"])

speech_config = speechsdk.SpeechConfig(subscription=os.environ.get('STT_KEY'), region=os.environ.get('STT_REGION'))
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, auto_detect_source_language_config=auto_detect_source_language_config)

# 配置翻译服务
translator_key = os.environ.get('TRANSLATOR_KEY')
translator_region = os.environ.get('TRANSLATOR_REGION')

translator_credential = AzureKeyCredential(translator_key)
translator_client = TextTranslationClient(credential=translator_credential, region=translator_region)

# 定义目标语言（中文）
target_language = "zh"

# 事件处理函数：每次识别到一条语音时调用
def recognized_callback(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # print(f"识别结果: {evt.result.text}")
        recognized_text = evt.result.text
        print(f"识别结果: {recognized_text}")

        # 调用翻译服务
        try:
            response = translator_client.translate(
                body=[recognized_text],
                to_language=[target_language]
            )
            translation = response[0] if response else None
            translated_text = translation.translations[0].text
            print(f"翻译结果: {translated_text}")
        except Exception as e:
            print(f"翻译时出错: {str(e)}")

    elif evt.result.reason == speechsdk.ResultReason.NoMatch:
        print("未能识别任何内容。")

# 事件处理函数：处理识别取消或错误
def canceled_callback(evt):
    print(f"识别被取消: {evt.reason}")
    if evt.reason == speechsdk.CancellationReason.Error:
        print(f"错误详情: {evt.error_details}")

# 绑定事件
speech_recognizer.recognized.connect(recognized_callback)
speech_recognizer.canceled.connect(canceled_callback)

# 开始连续识别
speech_recognizer.start_continuous_recognition()

print("语音识别已启动，请开始说话...")

# 等待用户手动停止
try:
    while True:
        pass
except KeyboardInterrupt:
    print("识别已停止。")
    speech_recognizer.stop_continuous_recognition()
```

目前有这样一些瑕疵：
- 我创建 `speech_recognizer` 的方式导致同一个翻译服务只在最开始的时候尝试确认录入语音的语言， 这在接下来的一系列语音识别中是不会变化的。  比如第一句接收到的话如果是 "こんにちは" 的话， 那么接下来听到的所有话都会被尝试翻译为日语。
- 我在 ACS Fall (Colorado convention center) 的会场中试了一下收录演讲者们的声音， 但是嘈杂的空调声常常让我的电脑识别不到任何内容。 
- 即便收音效果良好， 识别出来的内容也很受演讲者状态的影响， 如果碰到口吃或者紧张不连贯等情况， 就会得到出乎意料的结果。

## Text Translation

实际上翻译功能已经在上一小节中得到。  读者可以从上一段 python 脚本中轻易摘录出需要的部分。

这里我想简单讨论一下与 Alfred 的组合。  利用 Alfred 的 "Universal Action" 功能也可以实现截取选中文字并直接翻译及显示， 这样实际上节省了与系统剪切板和 notification center 之间进行交互的额外开发。 

先编写一个 python 脚本来实现翻译的基本功能：
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

# 读取选中的文本
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

接下来就把与系统的交互交给 Alfred 来做：

1. 在 Alfred 的 Workflows 界面中添加一个新的 "Blank Workflow";
2. 在 workflow 的空白面板中添加一个 "Universal Action", 设置为仅为 text 工作；
3. 在 workflow 的空白面板中添加一个 script, 这里我使用 zsh 脚本， 因为 python 需要使用特定 venv 下的程序， 设置起来比较麻烦。 脚本内容如下：
```zsh
query=$1

$PYTHON_VENV_PATH/bin/python $PY_SCRIPT/test_translation_alfred.py "$query"
```
4. 在 workflow 的空白面板中添加一个 "Post Notification", title 部分设置为 "Translation Results", text 部分设置为 `{query}`. 这样就可以直接把翻译结果输出到系统的 notification center.
