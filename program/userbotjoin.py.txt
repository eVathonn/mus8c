import asyncio
from config import BOT_USERNAME, ASSISTANT_NAME, OWNER_NAME, SUDO_USERS
from driver.decorators import authorized_users_only, sudo_users_only, errors
from driver.filters import command, other_filters
from driver.amort import user as USER
from pyrogram import Client, filters
from pyrogram.errors import UserAlreadyParticipant


@Client.on_message(
    command(["userbotjoin","شغيل","انضم","نضم", f"userbotjoin@{BOT_USERNAME}"]) & ~filters.private & ~filters.bot
)
@authorized_users_only
@errors
async def join_group(client, message):
    chid = message.chat.id
    try:
        invitelink = await client.export_chat_invite_link(chid)
    except BaseException:
        await message.reply_text(
            "• **ليس لدي صلاحيات:**\n\n» ✗ __اضافة المستخدمين__",
        )
        return

    try:
        user = await USER.get_me()
    except BaseException:
        user.first_name = "music assistant"

    try:
        await USER.join_chat(invitelink)
    except UserAlreadyParticipant:
        pass
    except Exception as e:
        print(e)
        await message.reply_text(     
            f"᥀برجاء التاكد من عدم حظر الحساب المساعد في المجموعه  ᥀ \n\n** او ربما لم يتمكن حساب المساعد من الانضمام إلى مجموعتك بسبب كثرة طلبات الانضمام **"
            "\n\n**أو تواصل مع المطور @VGGGP او أضف مساعدًا يدويًا لى مجموعتك وحاول مرة أخرى**",
        )
        return
    await message.reply_text(
           f"✓ ** دخل حساب المساعد للمجموعه او القناة بنجاح**",
    )


@Client.on_message(command(["userbotleave","غادر",
                            f"leave@{BOT_USERNAME}"]) & filters.group & ~filters.edited)
@authorized_users_only
async def leave_one(client, message):
    try:
        await USER.send_message(message.chat.id, "✓ الحساب المساعد غادر مجموعتك بنجاح")
        await USER.leave_chat(message.chat.id)
    except BaseException:
        await message.reply_text(
            "✗ ** او ربما لم يتمكن الحساب المساعد من المغادره ربما يكون بسبب الضغط.**\n\n**» انتظر او قم بطردي يدويا**"
        )

        return


@Client.on_message(command(["leaveall","غادرالجميع", f"leaveall@{BOT_USERNAME}"]))
@sudo_users_only
async def leave_all(client, message):
    if message.from_user.id not in SUDO_USERS:
        return

    left = 0
    failed = 0
    lol = await message.reply("↻ **userbot** leaving all chats !")
    async for dialog in USER.iter_dialogs():
        try:
            await USER.leave_chat(dialog.chat.id)
            left += 1
            await lol.edit(
                f"Userbot leaving all group...\n\nLeft: {left} chats.\nFailed: {failed} chats."
            )
        except BaseException:
            failed += 1
            await lol.edit(
                f"Userbot leaving...\n\nLeft: {left} chats.\nFailed: {failed} chats."
            )
        await asyncio.sleep(0.7)
    await client.send_message(
        message.chat.id, f"✓ Left from: {left} chats.\n✗ Failed in: {failed} chats."
    )
