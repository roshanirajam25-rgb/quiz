from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, ContextTypes
import questions
import os

TOKEN = os.getenv("7061025429:AAEnKWo0XZECrGHeJGWYMWdv-lDX0tUX-NA")

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    context.user_data['q'] = 0
    await send_quiz(update, context)

async def send_quiz(update, context):
    q = context.user_data['q']
    if q >= len(questions.quiz):
        await context.bot.send_message(
            chat_id=update.effective_chat.id,
            text="✅ Quiz Finished"
        )
        return

    data = questions.quiz[q]
    await context.bot.send_poll(
        chat_id=update.effective_chat.id,
        question=data['question'],
        options=data['options'],
        type='quiz',
        correct_option_id=data['answer'],
        open_period=20
    )
    context.user_data['q'] += 1

app = ApplicationBuilder().token(TOKEN).build()
app.add_handler(CommandHandler("start", start))
app.run_polling()
