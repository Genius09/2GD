# 2GD

import requests
from datetime import dat# `AAFT` is part of the BOT_TOKEN in the config.py file. It is likely a
# portion of the token used for authentication and authorization purposes in a
# bot application. It is important to keep this token secure and not share it
# publicly to prevent unauthorized access to the bot.
# `AAFT` is part of the BOT_TOKEN in the config.py file. It is likely a
# portion of the token used for authentication and authorization purposes in a
# bot application. It is important to keep this token secure and not share it
# publicly to prevent unauthorized access to the bot.
etime
import time

from config import BOT_TOKEN, CHAT_ID, API_URL

def get_set_price():
    try:
        response = requests.get(API_URL)
        data = response.json()
        return data['set']
    except Exception as e:
        log_message(f"Error fetching API: {e}")
        return None

def get_2d_value(price):
    return str(price).split('.')[-1]

def send_to_telegram(message):
    url = f"https://api.telegram.org/bot{BOT_TOKEN}/sendMessage"
    data = {"chat_id": CHAT_ID, "text": message}
    response = requests.post(url, data=data)
    log_message(f"Sent to Telegram: {message}")
    return response.text

def log_message(message):
    with open("log.txt", "a") as file:
        file.write(f"{datetime.now()} - {message}\n")

def summary_message():
    try:
        with open("log.txt", "r") as file:
            logs = file.readlines()[-20:]  # last 20 lines
        message = "📊 2D Summary (last 20 records):\n" + "".join(logs)
        send_to_telegram(message)
    except Exception as e:
        log_message(f"Error reading log file: {e}")

while True:
    now = datetime.now()
    current_time = now.strftime("%H:%M")
    print("Current Time:", current_time)

    if current_time in ["12:00", "15:30"]:
        price = get_set_price()
        if price:
            value_2d = get_2d_value(price)
            message = f"📊 {current_time} 2D Value: {value_2d}"
            send_to_telegram(message)
        else:
            log_message("Failed to get SET price.")
        time.sleep(60)

    if current_time in ["12:10", "15:40"]:
        summary_message()
        time.sleep(60)

    time.sleep(1)
