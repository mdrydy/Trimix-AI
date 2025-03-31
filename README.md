import telebot
import json
import os

BOT_TOKEN = '7308063220:AAEj73GKJWudtBB3-vgqsxSY_Cm7voEbOHM'
bot = telebot.TeleBot(BOT_TOKEN)

# اسم ملف التخزين
DATA_FILE = "points.json"

# تحميل البيانات من الملف إذا كان موجودًا
if os.path.exists(DATA_FILE):
    with open(DATA_FILE, "r") as file:
        user_points = json.load(file)
else:
    user_points = {}

def save_data():
    with open(DATA_FILE, "w") as file:
        json.dump(user_points, file)

@bot.message_handler(commands=['start'])
def start(message):
    user_id = str(message.from_user.id)  # تحويل المعرف إلى نص لأن JSON لا يدعم الأرقام كمفاتيح
    if user_id not in user_points:
        user_points[user_id] = 0
        save_data()
    bot.reply_to(message, "مرحبًا بك في بوت تجميع النقاط! استخدم /earn لكسب النقاط.")

@bot.message_handler(commands=['points'])
def show_points(message):
    user_id = str(message.from_user.id)
    points = user_points.get(user_id, 0)
    bot.reply_to(message, f"لديك {points} نقاط.")

@bot.message_handler(commands=['earn'])
def earn_points(message):
    user_id = str(message.from_user.id)
    points_earned = 10
    if user_id not in user_points:
        user_points[user_id] = 0
    user_points[user_id] += points_earned
    save_data()
    bot.reply_to(message, f"لقد حصلت على {points_earned} نقاط!")

@bot.message_handler(commands=['reset'])
def reset_points(message):
    user_id = str(message.from_user.id)
    user_points[user_id] = 0
    save_data()
    bot.reply_to(message, "تمت إعادة النقاط إلى الصفر.")

print("🤖 بوت تجميع النقاط يعمل الآن...")
bot.polling()
