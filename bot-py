import os
import discord
from discord.ext import commands
from dotenv import load_dotenv
from threading import Thread
from http.server import BaseHTTPRequestHandler, HTTPServer

load_dotenv()

TOKEN = os.getenv("DISCORD_TOKEN")
GUILD_ID = int(os.getenv("GUILD_ID"))
CHAT_GERAL_ID = int(os.getenv("CHAT_GERAL_ID"))

intents = discord.Intents.default()
intents.members = True
intents.message_content = True

bot = commands.Bot(command_prefix="!", intents=intents)

@bot.event
async def on_ready():
    print(f"Bot conectado como {bot.user} (ID: {bot.user.id})")

@bot.event
async def on_member_join(member: discord.Member):
    try:
        await member.send(f"oi {member.name}! bem-vindo(a) ao servidor 💫")
    except discord.Forbidden:
        pass

    canal = bot.get_channel(CHAT_GERAL_ID)
    if canal is None:
        return

    msg = await canal.send(f"{member.mention} entrou no servidor! seja bem-vindo(a) 🎉")

    try:
        pins = await canal.pins()
        if len(pins) >= 50:
            pins_sem_sistema = [p for p in pins if p.type == discord.MessageType.default]
            if pins_sem_sistema:
                await pins_sem_sistema[-1].unpin()
        await msg.pin()
    except discord.Forbidden:
        pass

# --- Servidor web falso pro Render não dormir ---
class WebServer(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        self.wfile.write(b"Bot online!")

def run_server():
    port = int(os.environ.get("PORT", 8080))
    server = HTTPServer(("0.0.0.0", port), WebServer)
    server.serve_forever()

def keep_alive():
    t = Thread(target=run_server)
    t.start()

if __name__ == "__main__":
    keep_alive()
    bot.run(TOKEN)
