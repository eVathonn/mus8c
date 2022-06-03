from datetime import datetime
from sys import version_info
from time import time

from config import (
    ALIVE_IMG,
    ALIVE_NAME,
    BOT_NAME,
    ASSISTANT_NAME,
    BOT_USERNAME,
    GROUP_SUPPORT,
    OWNER_NAME,
    UPDATES_CHANNEL,
)
from program import __version__
from driver.filters import command, other_filters
from pyrogram import Client, filters
from pyrogram import __version__ as pyrover
from pytgcalls import (__version__ as pytover)
from pyrogram.types import InlineKeyboardButton, InlineKeyboardMarkup, Message

__major__ = 0
__minor__ = 2
__micro__ = 1

__python_version__ = f"{version_info[0]}.{version_info[1]}.{version_info[2]}"


START_TIME = datetime.utcnow()
START_TIME_ISO = START_TIME.replace(microsecond=0).isoformat()
TIME_DURATION_UNITS = (
    ("week", 60 * 60 * 24 * 7),
    ("day", 60 * 60 * 24),
    ("hour", 60 * 60),
    ("min", 60),
    ("sec", 1),
)


async def _human_time_duration(seconds):
    if seconds == 0:
        return "inf"
    parts = []
    for unit, div in TIME_DURATION_UNITS:
        amount, seconds = divmod(int(seconds), div)
        if amount > 0:
            parts.append("{} {}{}".format(amount, unit, "" if amount == 1 else "s"))
    return ", ".join(parts)


@Client.on_message(
    command(["start", f"start@{BOT_USERNAME}"]) & filters.private & ~filters.edited
)
async def start_(client: Client, message: Message):
    await message.reply_text(
        f"""᥀ **هݪاެ عمࢪي ↤ {message.from_user.mention()} !**\n
 ᥀︙ **اެناެ بۅٛت ݪتشغيݪ اެݪاެغاެني في اެݪمكاެݪماެت اެݪصۅٛتيةه  • **
** ᥀︙ݪمعࢪفة طࢪيقة تفعيݪ هذاެ اެݪبۅٛت اެضغط عݪى » طࢪيقة اެݪتفعيݪ ! • . **
 ᥀︙ݪمعࢪفةه اެۅٛاެمࢪ هذاެ اެݪبۅٛت اެضغط عݪى » اެۅٛاެمࢪ اެݪتشغيݪ ! • .**
 
""",
        reply_markup=InlineKeyboardMarkup(
            [
                [
                    InlineKeyboardButton(
                        "‹ اެضف اެݪبۅٛت في مجمۅٛعتك ›",
                        url=f"https://t.me/{BOT_USERNAME}?startgroup=true",
                    )
                ],
                [InlineKeyboardButton("‹ طࢪيقة اެݪتفعيݪ ›", callback_data="cbhowtouse")],
                [InlineKeyboardButton("‹ اެۅٛاެمࢪ اެݪتشغيݪ ›", callback_data="cbvamp")],                 
                [
                    InlineKeyboardButton("‹ اެۅٛاެمࢪ اެݪمشࢪفين ›", callback_data="cbcmds"),
                    InlineKeyboardButton("‹ اެݪمبࢪمج ›", url=f"https://t.me/{OWNER_NAME}"),
                ],
                [
                    InlineKeyboardButton(
                        "‹ كࢪۅٛب اެݪدعم › ", url=f"https://t.me/{GROUP_SUPPORT}"
                    ),
                    InlineKeyboardButton(
                        "‹ اެݪسۅٛࢪس › ", url=f"https://t.me/AOOOU"
                    ),
                ],
                [
                    InlineKeyboardButton(
                        ALIVE_NAME, url=f"https://t.me/{OWNER_NAME}"),
                ],
            ]
        ),
        disable_web_page_preview=True,
    )


@Client.on_message(
    command(["alive", "السورس", f"alive@{BOT_USERNAME}"]) & filters.group & ~filters.edited
)
async def alive(client: Client, message: Message):
    current_time = datetime.utcnow()
    uptime_sec = (current_time - START_TIME).total_seconds()
    uptime = await _human_time_duration(int(uptime_sec))

    keyboard = InlineKeyboardMarkup(
            [
                [
                    InlineKeyboardButton(
                        ALIVE_NAME,
                        url=f"https://t.me/{OWNER_NAME}",
                    )
                ],
                [
                    InlineKeyboardButton(
                        "‹ كࢪۅٛب اެݪدعم › ", url=f"https://t.me/{GROUP_SUPPORT}"
                    ),
                    InlineKeyboardButton(
                        "‹ اެݪسۅٛࢪس › ", url=f"https://t.me/AOOOU"
                    ),
                ],
                [
                    InlineKeyboardButton(
                        "‹ اެضف اެݪبۅٛت في مجمۅٛعتك › ", url="https://t.me/{BOT_USERNAME}?startgroup=true"
                ),
            ]
        ]
    ) 

    alive = f"**مرحبآ  {message.from_user.mention()}, انا {BOT_NAME}**\n\n᥀ البوت يعمل بشكل طبيعي\n᥀المبرمج : [{ALIVE_NAME}](https://t.me/{OWNER_NAME})\n᥀ اصدار Bot : `v{__version__}`\n᥀ اصدار Pyrogram : `{pyrover}`\n᥀ اصدار Python: `{__python_version__}`\n᥀ اصدار PyTgCalls : `{pytover.__version__}`\n᥀ وقت التشغيل: `{uptime}`\n\n**شكرًا لإضافتي هنا ، لتشغيل الفيديو والموسيقى على دردشة الفيديو الخاصة بمجموعتك ** ᥀"

    await message.reply_photo(
        photo=f"{ALIVE_IMG}",
        caption=alive,
        reply_markup=keyboard,
    )


@Client.on_message(command(["ping", "يست", f"ping@{BOT_USERNAME}"]) & ~filters.edited)
async def ping_pong(client: Client, message: Message):
    start = time()
    m_reply = await message.reply_text("pinging...")
    delta_ping = time() - start
    await m_reply.edit_text("᥀ `PONG!!`\n" f"᥀️ `{delta_ping * 1000:.3f} ms`")


@Client.on_message(command(["uptime","الوقت", f"uptime@{BOT_USERNAME}"]) & ~filters.edited)
async def get_uptime(client: Client, message: Message):
    current_time = datetime.utcnow()
    uptime_sec = (current_time - START_TIME).total_seconds()
    uptime = await _human_time_duration(int(uptime_sec))
    await message.reply_text(
        "᥀ bot status:\n"
        f"• **uptime:** `{uptime}`\n"
        f"• **start time:** `{START_TIME_ISO}`"
    )
