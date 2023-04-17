import pyttsx3
import time
import datetime
import speech_recognition as sr
from fuzzywuzzy import fuzz
import webbrowser
import subprocess
from tkinter import *


window = Tk()
window.title("Добро пожаловать в приложение Кеша")
btn = Button(window, text="Не нажимать!")
btn.grid(padx=6, pady=6)
window.geometry('300x300')

opts = {
    "alias": ('кеша', 'кэша', 'попугай'),
    "tbr": ('скажи', 'расскажи', 'покажи', 'сколько', 'произнеси'),
    "cmds": {
        "ctime": ('текущее время', 'время', 'сейчас времени', 'который час'),
        "browzer": ('открой яндекс', 'запусти яндекс'),
        "ecampus": ('екампус', 'открой екампус'),
        "app": ('приложение'),
        "repeat": ('Повтори')
    }
}

# функция
def speak(what):
    print(what)
    speak_engine.say(what)
    speak_engine.runAndWait()

def callback(recognizer, audio):
    try:
        voice = recognizer.recognize_google(audio, language="ru-RU").lower()
        print("[log] Распознано: " + voice)
        cmd = voice.replace(opts["alias"], "").strip()
        cmd = recognize_cmd(cmd)
        execute_cmd(cmd['cmd'])
    except sr.UnknownValueError:
        print("[log] Голос не распознан")
    except sr.RequestError as e:
        print("[log] Неизвестная ошибка")

def recognize_cmd(cmd):
    RC = {'cmd': '', 'percent': 0}
    for c, v in opts['cmds'].items():
        vrt = max([fuzz.ratio(cmd, x) for x in v])
        if vrt > RC['percent']:
            RC['cmd'] = c
            RC['percent'] = vrt
    return RC

def execute_cmd(cmd):
    if cmd == 'ctime':
        now = datetime.datetime.now()
        speak("Сейчас: " + str(now.hour) + " часов" + ' :' + str(now.minute) + " минут")
    elif cmd == 'browzer':
        url = 'https://yandex.ru/'
        webbrowser.open(url)
    elif cmd == 'app':
        subprocess.Popen('')
    elif cmd == 'ecampus':
        url1 = 'https://ecampus.ncfu.ru/'
        webbrowser.open(url1)
    elif cmd == 'repeat':
        speak(cmd)


#запуск
r = sr.Recognizer()
m = sr.Microphone(device_index=1)

with m as source:
    r.adjust_for_ambient_noise(source)

speak_engine = pyttsx3.init()

speak("К вашим услугам")

stop_listening = r.listen_in_background(m, callback)
window.mainloop()
while True: time.sleep(0.1)
