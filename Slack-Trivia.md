```
-- variables
set delayAmt to 0.1
set regexMatchBefore to "^[^_]*" -- var input after this expression
set regexMatchNextLine to "[]+([^]+)" -- var input before this expression

-- functions

-- insert item into list
on insertItemInList(theItem, theList, thePosition)
	set theListCount to length of theList
	if thePosition is 0 then
		return false
	else if thePosition is less than 0 then
		if (thePosition * -1) is greater than theListCount + 1 then return false
	else
		if thePosition is greater than theListCount + 1 then return false
	end if
	if thePosition is less than 0 then
		if (thePosition * -1) is theListCount + 1 then
			set beginning of theList to theItem
		else
			set theList to reverse of theList
			set thePosition to (thePosition * -1)
			if thePosition is 1 then
				set beginning of theList to theItem
			else if thePosition is (theListCount + 1) then
				set end of theList to theItem
			else
				set theList to (items 1 thru (thePosition - 1) of theList) & theItem & (items thePosition thru -1 of theList)
			end if
			set theList to reverse of theList
		end if
	else
		if thePosition is 1 then
			set beginning of theList to theItem
		else if thePosition is (theListCount + 1) then
			set end of theList to theItem
		else
			set theList to (items 1 thru (thePosition - 1) of theList) & theItem & (items thePosition thru -1 of theList)
		end if
	end if
	return theList
end insertItemInList

on encode_char(thisChar)
	set the ASCIInum to (the ASCII number thisChar)
	set the hexList to {"0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "A", "B", "C", "D", "E", "F"}
	set x to item ((ASCIInum div 16) + 1) of the hexList
	set y to item ((ASCIInum mod 16) + 1) of the hexList
	return ("%" & x & y) as string
end encode_char

on encode_string(aString)
	set {myTID, AppleScript's text item delimiters} to {AppleScript's text item delimiters, {""}}
	set myList to text items of aString -- Gives list {"2", "69", "12"}
	set containerList to {}
	repeat with myItem in myList -- Loop through the items in the list
		display dialog encode_char(myItem)
	end repeat
	set AppleScript's text item delimiters to myTID -- It's considered good practice to return the TID's to their original state
end encode_string


encode_string("test")

--open Google Chrome to resize
tell application "Google Chrome" to activate
tell application "Google Chrome" to close every window -- reset windows
tell application "System Events" to keystroke "n" using command down --new window for later

--set bound variables
tell application "Finder"
	set {screen_left, screen_top, screen_width, screen_height} to bounds of window of desktop
end tell

--make sure the front is in front
tell application "System Events"
	set myFrontMost to name of first item of (processes whose frontmost is true)
end tell

--set front most app (slack i hope) to full, if not go to 0 
try
	tell application "System Events"
		tell process myFrontMost
			set {{w, h}} to size of drawer of window 1
		end tell
	end tell
on error
	set {w, h} to {0, 0}
end try
--and try another way
tell application "System Events"
	tell process myFrontMost
		try
			set {{w, h}} to size of drawer of window 1
		on error
			set {w, h} to {0, 0}
		end try
		set position of window 1 to {0, 0}
		set size of window 1 to {screen_width - w, screen_height}
	end tell
end tell

--open slack
tell application "Slack" to activate

--set bound variables
tell application "Finder"
	set {screen_left, screen_top, screen_width, screen_height} to bounds of window of desktop
end tell

--make sure the front is in front
tell application "System Events"
	set myFrontMost to name of first item of (processes whose frontmost is true)
end tell

--set front most app (slack i hope) to full, if not go to 0 
try
	tell application "System Events"
		tell process myFrontMost
			set {{w, h}} to size of drawer of window 1
		end tell
	end tell
on error
	set {w, h} to {0, 0}
end try
--and try another way
tell application "System Events"
	tell process myFrontMost
		try
			set {{w, h}} to size of drawer of window 1
		on error
			set {w, h} to {0, 0}
		end try
		set position of window 1 to {0, 0}
		set size of window 1 to {screen_width - w, screen_height}
	end tell
end tell

tell application "System Events" to key code 53 --escape any context we might already be in
tell application "System Events" to keystroke "k" using command down --load the slack switcher
delay delayAmt
tell application "System Events" to keystroke "trivia-bot-testing" --where to go
delay delayAmt
tell application "System Events" to key code 36 --enter

delay delayAmt

-- only run this thing between Slack and Google Chrome 
tell application "System Events"
	repeat 3 times
		set activeApp to name of first application process whose frontmost is true
		if "Slack" is in activeApp then
			do shell script "/usr/local/bin/cliclick c:" & "300,730" -- move to the bottom and click
			tell application "System Events" to keystroke "a" using command down -- select all input
			delay delayAmt
			tell application "System Events" to key code 51 -- delete
			delay delayAmt
			do shell script "/usr/local/bin/cliclick tc:" & "300,700" -- move to the bottom and triple click
			delay delayAmt
			--do shell script "/usr/local/bin/cliclick c:180,0" -- open edit menu
			--do shell script "/usr/local/bin/cliclick c:180,100" -- copy the highlighted text
			tell application "System Events" to tell application process "Slack" to click menu item "Copy" of menu "Edit" of menu bar 1
			delay 1
			set searchTerm to the clipboard
			set searchQuery to "https://www.google.com/search?q=" & searchTerm
			delay delayAmt
			tell application "Google Chrome" to activate
		end if
		if "Google Chrome" is activeApp then
			
			tell application "Google Chrome" to open location (searchQuery)
			do shell script "/usr/local/bin/cliclick c:100,300" -- click on the left
			delay 1 -- long delay
			tell application "System Events" to keystroke "a" using command down -- select all input
			tell application "System Events" to keystroke "a" using command down -- select all input
			delay delayAmt
			do shell script "/usr/local/bin/cliclick c:180,0" -- open edit menu
			delay delayAmt
			do shell script "/usr/local/bin/cliclick c:180,100" -- copy the highlighted text
			tell application "Sublime Text 2" to activate
		end if
		if "Sublime Text 2" is activeApp then
			do shell script "/usr/local/bin/cliclick c:180,0" -- open file menu
			do shell script "/usr/local/bin/cliclick c:180,220" -- new window
			delay delayAmt
			do shell script "/usr/local/bin/cliclick c:180,0" -- open file menu
			do shell script "/usr/local/bin/cliclick c:180,30" -- new file
			delay delayAmt
			do shell script "/usr/local/bin/cliclick c:400,400" -- click in to the file
			do shell script "/usr/local/bin/cliclick c:220,0" -- edit
			do shell script "/usr/local/bin/cliclick c:220,140" -- paste
			delay 1
			exit repeat
		end if
		
	end repeat
end tell
```
