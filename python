import os
import httpx
from telegram import Update
from telegram.ext import Application, CommandHandler, ContextTypes

BOT_TOKEN = os.environ.get("TELEGRAM_BOT_TOKEN")
COINGECKO_KEY = os.environ.get("COINGECKO_API_KEY")
PORT = int(os.environ.get("PORT", 8080))

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "BitFusion399Bot is online. Send a crypto symbol like BTC or ETH to get the price."
    )

async def price(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if not context.args:
        await update.message.reply_text("Usage: /price <symbol>")
        return
    symbol = context.args[0].lower()
    # Map common symbols to CoinGecko IDs
    ids = {"btc": "bitcoin", "eth": "ethereum", "sol": "solana", "doge": "dogecoin"}
    coin_id = ids.get(symbol)
    if not coin_id:
        await update.message.reply_text("Unknown symbol. Try BTC, ETH, SOL, or DOGE.")
        return
    url = f"https://api.coingecko.com/api/v3/simple/price?ids={coin_id}&vs_currencies=usd"
    headers = {"x-cg-demo-api-key": COINGECKO_KEY} if COINGECKO_KEY else {}
    async with httpx.AsyncClient() as client:
        r = await client.get(url, headers=headers)
        data = r.json()
    if coin_id in data:
        price = data[coin_id]["usd"]
        await update.message.reply_text(f"{symbol.upper()}: ${price:,.2f}")
    else:
        await update.message.reply_text("Could not fetch price.")

def main():
    app = Application.builder().token(BOT_TOKEN).build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(CommandHandler("price", price))
    # For Railway webhook mode, you need to register the webhook URL
    # This requires setting TELEGRAM_WEBHOOK_URL or using Railway's public domain
    # See railway template docs for the exact pattern

if __name__ == "__main__":
    main()
