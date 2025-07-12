import random
import discord
from discord.ext import commands
from datetime import datetime
import ssl
import certifi
import shutil

# Set up SSL cert fix
default_ssl_cert = ssl.get_default_verify_paths().openssl_cafile
certifi_bundle = certifi.where()
shutil.copy(certifi_bundle, default_ssl_cert)

# Discord bot setup
intents = discord.Intents.default()
intents.message_content = True
bot = commands.Bot(command_prefix="!", intents=intents)


user_water = {}

# Achievement milestones
milestone_messages = {
    1: "🥤 First Liter Down!",
    2: "💪 Two Liters Strong!",
    3: "🌊 Hydration Hero!",
    4: "👑 Water King/Queen!",
    5: "Keep it up!!"
}

@bot.event
async def on_ready():
    print(f"✅ Logged in as {bot.user}")
    print(f"Registered commands: {[cmd.name for cmd in bot.commands]}")

#Introduction
@bot.command()
async def hello(ctx):
    await ctx.send("👋 Hey! I'm your water tracker bot.")

#Water
@bot.command()
async def drink(ctx, amount: float):
    user_id = ctx.author.id
    today = datetime.now().strftime("%Y-%m-%d")

    if user_id not in user_water:
        user_water[user_id] = {}

    user_water[user_id][today] = user_water[user_id].get(today, 0) + amount
    total_today = user_water[user_id][today]

    # Normal response
    await ctx.send(f"💧 {ctx.author.display_name}, you drank {amount} liters of water today! Total for {today}: {total_today} liters.")

    # Check for achievements
    for milestone, message in milestone_messages.items():
        if total_today >= milestone and (total_today - amount) < milestone:
            await ctx.send(f"🏆 {ctx.author.display_name}, {message}")
#fun facts
@bot.command()
async def funfact(ctx):
    facts = [
        "💧 Your body is about 60% water!",
        "🚿 You can save up to 8 gallons of water a day by turning off the tap when brushing your teeth.",
        "🌊 The Earth is about 71% water—but only 1% is drinkable.",
        "🧠 Drinking water can improve brain function and mood.",
        "🏃 You lose water when you sweat, so drink more if you're exercising!",
        "🍼 Babies have more water in their bodies than adults—around 75%!",
        "💦 Water regulates your body temperature and helps with digestion.",
        "🐳 Whales get water from the food they eat, not by drinking!",
        "💧 Drinking cold water burns a few more calories than room temp water!",
        "💦 A person can survive up to 3 days without water, but not longer!"
        " 😤The more water you drink the clearer your skin,the clearer the skin, the more huzz you attract😎"
    ]
    await ctx.send(random.choice(facts))
#Amount of Water
@bot.command()
async def total(ctx):
    user_id = ctx.author.id
    today = datetime.now().strftime("%Y-%m-%d")

    total_today = user_water.get(user_id, {}).get(today, 0)
    await ctx.send(f"🧮 {ctx.author.display_name}, you've had {total_today} liters of water today.")
#Reset server
@bot.command()
async def reset(ctx):
    user_id = ctx.author.id
    today = datetime.now().strftime("%Y-%m-%d")

    if user_id in user_water and today in user_water[user_id]:
        user_water[user_id][today] = 0
        await ctx.send(f"🔄 {ctx.author.display_name}, your water intake for {today} has been reset.")
    else:
        await ctx.send(f"⚠️ {ctx.author.display_name}, you have no recorded water intake for today to reset.")

# Da token
TOKEN = "hlgiygiugiugizZjtjy^TRET"

bot.run(TOKEN)
