Toast
=====

Notification utility for Windows bash (WSL, Cygwin, MSYS etc.) and MacOS.  
Written in pure bash with plugins using other technologies (cURL, Powershell etc.).

Project homepage: https://github.com/chanibal/toast/


Usage
-----

Simple popup just with a subject:

```bash
toast "my message in the notification"
```

Popup with standard input printed:

![screenshot](screenshot.png)

```bash
date --iso-8601=s | toast "The current date and time"
```

All arguments are used as the message subject.

If the command is executed in a pipe, all of `stdin` is read.


Plugins and configuration
-------------------------

Toast is very simple to extend. 
It will automatically find functions that start with `plugin_toast_...` and evaluate them with three arguments:

1. `subject` - the subject of the notification (the part that is the command line argument to the `toast` command)
2. `body` - the body of the notification (the optional part that is piped into the `toast` command)
3. `client` - the name of the current device, defaults to hostname

You can add your own plugin in the configuration file `~/.config/.toast` (supports `XDG_CONFIG_HOME` changes) or in your `.profile`/`.bash_profile`/other script executing on shell startup.

Example:
```bash
plugin_toast_example ()
{
	local subject body client
	subject="$1"
	body="$2"
	client="$3"

	curl --silent --output /dev/null --show-error --fail -X POST \
		--form-string subject="$subject"                         \
		--form-string body="$body"                               \
		--form-string client="$client"                           \
		"http://example.com"
	
	return $?
}
```


Available plugins
-----------------

Currently two plugins are built in

### `windows_notification`

Pops up a Windows 8/10 tray notification in the corner of your screen.

No additional configuration


### `ifttt`

Executes a IFTTT maker request. This is an easy way to link an Android popup using Pushbullet.

Configuration:

- `PLUGIN_TOAST_IFTTT_KEY` - the maker event key, required
- `PLUGIN_TOAST_IFTTT_EVENT` - the maker event name, defaults to `notification`.
