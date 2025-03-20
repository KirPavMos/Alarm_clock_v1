# Alarm_clock in Python

import time
from datetime import datetime, timedelta
import pygame 
pygame.mixer.init()

# Список для хранения будильников
alarms = []

# Функция для воспроизведения звука из файла
def play_sound(sound_file):
    try:
        pygame.mixer.music.load(sound_file)  # Загрузка звукового файла
        pygame.mixer.music.play()  # Воспроизведение звука
        while pygame.mixer.music.get_busy():  # Ожидание завершения воспроизведения
            time.sleep(1)
    except Exception as e:
        print(f"Ошибка при воспроизведении звука: {e}")

# Функция для проверки и срабатывания будильников
def check_alarms():
    while True:
        current_time = datetime.now().strftime("%H:%M")  # Текущее время в формате "ЧЧ:ММ"
        for alarm in alarms:
            if alarm["time"] == current_time and not alarm["triggered"]:
                print(f"Время вставать! Будильник на {alarm['time']}")
                if alarm["sound_file"]:
                    play_sound(alarm["sound_file"])  # Воспроизведение звукового файла
                alarm["triggered"] = True
                if alarm["repeat"]:
                    # Установка следующего повтора
                    next_time = (datetime.strptime(alarm["time"], "%H:%M") + timedelta(minutes=alarm["repeat_interval"])).strftime("%H:%M")
                    alarms.append({
                        "time": next_time,
                        "sound_file": alarm["sound_file"],
                        "repeat": alarm["repeat"],
                        "repeat_interval": alarm["repeat_interval"],
                        "triggered": False
                    })
        time.sleep(10)  # Проверка каждые 10 секунд

# Функция для добавления будильника
def add_alarm():
    while True:
        alarm_time = input("Введите время будильника в формате ЧЧ:ММ: ")
        try:
            # Проверка корректности формата времени
            datetime.strptime(alarm_time, "%H:%M")
            break
        except ValueError:
            print("Неправильный формат времени. Попробуйте снова.")

    sound_choice = input("Хотите звуковое уведомление? (да/нет): ").lower()
    sound_file = None
    if sound_choice == "да":
        sound_file = input("Введите путь к звуковому файлу (формат .mp3): ")
        # Проверка, существует ли файл
        try:
            with open(sound_file, "rb"):
                pass
        except FileNotFoundError:
            print("Файл не найден.")
            sound_file = None

    repeat_choice = input("Хотите повторять будильник? (да/нет): ").lower()
    repeat = True if repeat_choice == "да" else False
    repeat_interval = 0
    if repeat:
        repeat_interval = int(input("Введите интервал повторения в минутах: "))

    alarms.append({
        "time": alarm_time,
        "sound_file": sound_file,
        "repeat": repeat,
        "repeat_interval": repeat_interval,
        "triggered": False
    })
    print(f"Будильник на {alarm_time} добавлен.")

# Основной цикл программы
def main():
    print("Добро пожаловать в программу 'Будильник'!")
    while True:
        print("\nМеню:")
        print("1. Добавить будильник")
        print("2. Запустить проверку будильников")
        print("3. Выйти")
        choice = input("Выберите действие: ")

        if choice == "1":
            add_alarm()
        elif choice == "2":
            print("Запуск проверки будильников...")
            check_alarms()
        elif choice == "3":
            print("Выход из программы.")
            break
        else:
            print("Неправильный выбор. Попробуйте снова.")

if __name__ == "__main__":
    main()