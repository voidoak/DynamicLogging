# Better Logging.

Using decorators and simple `json` data we can dynamically and programmatically improve event logging with your Discord bot. It works as follows:

```py
@log_event
@commands.Cog.listener()
async def on_given_event(self, args):
		if predicate:
				""" where predicate is a disallowed circumstance in which you do not want logging to occur; this will return None, allowing the log_event to exit without logging. """
				return

		""" perform any necessary logic on these data as you pass them to the data dict, as no preferential logic should be performed later in the log_event function. """
		data = {
				"title": args.monty,
				"description": utils.my_function(args.foobar),
				"spam": args.eggs
		}
		
		return data
		
```

Once the event listener you have defined is triggered, `log_event` will take the event type and the data you returned and attempt to format an embed using that data, according to the formats listed in `logging/log_formats.json`. You must make sure to return all data you require inside that format or you will raise a `KeyError`. 

Formatting properly is as follows (`logging/log_formats.json`):
```js
{
	"event_type": {
		"title": "{title}",
		"description": "Did you know that {description}? Me neither, supposedly {spam.x} is a good alternative!"
	}  // "title" and "description" are required, else KeyError is raised.
}
```
Optional fields can be any additional data you would want represented in your embed in any of the fields, as long as you represent them properly in the given event listener format in the formats file. Additional fields such as an image, footer, and author must be manually inputted as keys with the desired data. `"image"` field must be a link to the image.

If all goes well and the returned event data was formatted in sync with the respective format, `log_event` will generate an embed and attempt to send it in the proper logging channel; this data is stored in `logging/log_config.json`. If none is found, it will simply not log it. For a given event, it will store a channel snowflake ID, to be used later.

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

    data = {
        "author": message.author,
        "id": message.id,
        "channel": message.channel,
        "content": message.content
    }

    return data
```

And here is how I have set up the `on_message_delete` format:
```json
"on_message_delete": {
    "title": "Message Deleted",
    "author": "{author.name}#{author.discriminator} ({author.id})",
    "description": "{content}",
    "footer": "Message ID: {id} | Channel: {channel}"
  }
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
