# Better Logging.

Using decorators and simple `json` data we can dynamically and programmatically improve event logging with your Discord bot. It works as follows:

```py
@log_event
@commands.Cog.listener()
async def on_given_event(self, args):
	if predicate:
		""" where predicate is a disallowed circumstance in which
		you do not want logging to occur; this will return None,
		allowing log_event to exit without logging. """
		return

	""" perform any necessary logic on these data as you pass them to the data dict,
	as no preferential logic should be performed later in the log_event function. """

	embed = discord.Embed(
		title=args.monty,
		description: utils.my_function(args.foobar)
	)
	
	message = f"{args.foobar} activated {args.spam} event!"
	# return (in a dict) whichever objects are attributes of the discord.Message object, so that it will send it as the log message.
	return { "message": message, "embed": embed }
		
```

Once the event listener you have defined is triggered, `log_event` will take the event type and the data you returned and attempt to send it in the proper channel. The proper logging channel data json file is stored in the `Logs` cog as `self.log_config`. It will be opened and read in the `log_event` function, so make sure to properly point to it.

The file is structured as follows:
```js
{
	"on_message_delete": 1234567890  // sample event/channel ID
}
```

## Usage

Below we have my `on_message_delete` listener:
```py
@log_event
@commands.Cog.listener()
async def on_message_delete(self, message):
    if message.author.bot:  # no reason to log bot messages getting deleted.
        return

	author = message.author
	
	embed = discord.Embed(
		title = "Message Deleted",
		description= message.content
		footer = f"Message ID: {message.id} | Channel: {message.channel}"
	)
	
	embed.set_author(name=f"{author.name}#{author.discriminator} ({author.id})", icon_url=author.avatar_url)

    return { "embed": embed }
```


## Example
Here is an example message in the test server:

![message](https://media.discordapp.net/attachments/789533464235212861/789690244743168011/unknown.png "boy I sure hope the mods don't see this lmao")

And now we shall log the deletion event, and the message contents:

![message](https://media.discordapp.net/attachments/789533464235212861/789693814771417088/unknown.png "logged message")

## Dependencies

```
pip install discord.py
pip install decorator
```

And that's it! Enjoy logging the hell out of your server mwahahaha
