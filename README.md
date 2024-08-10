# yeniai
import speech_recognition as sr
import webbrowser
import time
import wikipedia
from time import ctime
import os
import random
from gtts import gTTS
import playsound

wikipedia.set_lang("TR")
r = sr.Recognizer()

def record_audio(ask=False):
    with sr.Microphone() as source:
        if ask:
            ai_speak(ask)
        audio = r.record(source, duration=3)  # Süre ayarı
        # audio = r.listen(source)
        try:
            voice_data = r.recognize_google(audio, language="tr-TR")
            # ai_speak(voice_data)
            return voice_data
        except sr.UnknownValueError:
            print("Ses algılanamadı.")
            return ""
        except sr.RequestError:
            print("Sistem hatası!")
            return ""

def ai_speak(audio_string):
    tts = gTTS(text=audio_string, lang="tr")
    r = random.randint(1, 100000)
    audio_file = "audio-" + str(r) + ".mp3"
    tts.save(audio_file)
    playsound.playsound(audio_file)
    print(audio_string)
    os.remove(audio_file)

def respond(voice_data):
    if "Merhaba" in voice_data:
        ai_speak("Merhabalar...")

    elif "internette ara" in voice_data:
        kelime = record_audio("Ne aramak istersiniz?")
        if kelime:
            url = "https://www.google.com/search?q=" + kelime
            webbrowser.get().open(url)
            try:
                wikikelime = wikipedia.summary(kelime, sentences=2)
                ai_speak(wikikelime)
            except wikipedia.exceptions.DisambiguationError as e:
                ai_speak("Çok fazla sonuç bulundu, lütfen daha spesifik olun.")
            except wikipedia.exceptions.PageError:
                ai_speak("Böyle bir konu bulunamadı.")
        
    elif "kimdir" in voice_data:
        kelime = voice_data.split("kimdir")[0].strip()
        if kelime:
            url = "https://www.google.com/search?q=" + kelime + " kimdir"
            webbrowser.get().open(url)

    elif "nerede" in voice_data:
        kelime = voice_data.split("nerede")[0].strip()
        if kelime:
            url = "https://www.google.com.tr/maps/place/" + kelime
            webbrowser.get().open(url)

    elif "saat kaç" in voice_data:
        ai_speak(ctime())

time.sleep(1)
ai_speak("Merhaba, efendim.")
while True:
    voice_data = record_audio()
    if voice_data:
        respond(voice_data)
