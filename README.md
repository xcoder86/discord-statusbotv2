!!! PLEASE UPDATE YOUR SOURCE / HOSTED PACKAGE - LINK NOT WORKING - THANKS !!!!

# Discord StatusBot

Automated Builds - Download: https://cdn.movinto.net/discord-statusbot/ (will get moved to gitub asap)

This project provides a simple bot which checks the status of any gameserver. Its written in go and for abstracting the discord api the library [discordgo](https://github.com/bwmarrin/discordgo) is used. For fetching the information about the gameservers the  **[query.li](https://query.li/doc/)** API is used.
If you want to know which games are supported please check out the documentation available there.
In general, every steam game is supported using the "game" steam.

# Inviting the bot
If you want to get this bot on your discord guild you may use the following link: [invite bot](https://discordapp.com/oauth2/authorize?client_id=589102629653577777&scope=bot&permissions=0)
This is the hosted version of this bot. You're also able to host it on your own. For this purpose there are native windows and linux binaries which should made it easy to use for everyone.

Currently there is just an automated build mechanism in my openshift, I'll add a download link for the latest version later. For now just ask me for simple setup instructions.

# Command reference
This bot is managed by simple commands like the other before as well. Each command needs to be prefixed with the ident defined in the config file. The hosted version uses `!`.
## `setnick`
You can change the bots name while using this command. It should be formatted like the following:
`!setnick mybot`

This changes the bots name to "mybot".
## `addserver`
The `addserver` comand is used for adding servers to the bots inventory.
The command is structured like the following:
`!addserver {tag} {game} {ip/domain} {port}`
The tag means just the "name" of this server in a short. This is used later for requesting the servers status (see status command).

This should result in something like:
`!addserver mygameserver arkse 123.123.123.123 27016`

The Port used here needs to be the query port.

## `delserver`
delserver removes recently added servers from the bots inventory.
The command is structured like the following:
`!delserver {tag}`

See !addserver and !servers command as well.

This should result in something like:
`!delserver mygameserver`

## `servers`
This is a simple command which returns a list of servers configured. The response just contains the tags of the servers.

## `setdescription`
The Description is some kind of virtual field which may is used in the formatting later. If you want to change the description of a server you just have to run it like the following:
`!setdescription mygameserver This is my ark server`
## `status`
The bots response on this command if a tag is provided is the defined formatted status response. There are defaults used if there is no other formatting defined. But each gameserver can have its own formatting. (See format command)

## `monitoring`
There is some kind of monitoring again as well. This is just a simple check-every-x-logic which reports if the status of a gameserver changes. If your gameserver went offline it'll get reported to the channel where the monitoring was enabled at.
Command structure:
`!monitoring {tag} {true/false}`

This should result in something like:
`!monitoring mygameserver true` - which enables the monitoring for "mygameserver" in the current channel.

## `format`
Each gameserver can have its own formatting. You can change it with this command. This is a little more complex, if you have any issues feel free to use the [support discord](https://discord.gg/aMDa2TB).

The command is structured like following:
`!format {tag} {type} {status} {format}`

Valid types:
* status
* monitoring

Valid status:
* online
* offline

There are a lot of formatting variables available. For checking which variables are valid you may use the following formatting:
`!format mygameserver status online {{printf "%+v\n" . }}`

# Formats
The formatting in the new version is a little more advanced. You can query the variables directly like `{{.Game.Info.player_count}}` but conditions are possible as well now.
If you have 10 slots available for example you can use a formatting like this:
`{{ if and (gt .Game.Info.player_count 5.0) (le .Game.Info.player_count 9.0) }} nearly full! {{ else if eq .Game.Info.player_count 10.0 }} we're full {{ end }}`

This basically means that if more than 5 but less than 9 players are online "nearly full!" is getting printed out, if 10 players are online "we're full" is returned.

Additional to the variables you can see with `{{printf "%+v\n" . }}` there are two other variables available:
`{{break}}` - which results in a line break
`{{description}}` - which results in the description you set with !setdescription

## Examples
Just some formatting ideas, feel free to create a PR if you have some ideas so we can add them here :)

Command: `!format mygameserver status online Server tagged {{tag}} is up {{ if and (gt .Game.Info.player_count 5.0) (le .Game.Info.player_count 9.0) }} but nearly full! {{ else if eq .Game.Info.player_count 10.0 }} but we're full {{ end }}`
Result: Server tagged mygameserver is up( | but nearly full! | but we're full )


## self-hosted
Automated Builds - Download: https://cdn.movinto.net/discord-statusbot/ (will get moved to gitub asap)

Just download the binary you need (windows = discord-statusbot.exe; linux = discord-statusbot) and the config example.
Replace the "token" with the discord token provided here: https://discordapp.com/developers/applications/
Place the config file anywhere and start the bot running the binary and defining the config file with "-config".
Example:
`./discord-statusbot -config ~/.config/discord.json`

And thats it. For more detailed output if you have to debug something for example you can provide "-vvv" for running the bot in a verbose mode.
