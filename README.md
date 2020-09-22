<div align="center">

## IRC bot Tutorial


</div>

### Description

This allows users to write their own IRC bot.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[br1m5t0n3](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/br1m5t0n3.md)
**Level**          |Intermediate
**User Rating**    |4.5 (50 globes from 11 users)
**Compatibility**  |VB 3\.0, VB 4\.0 \(16\-bit\), VB 4\.0 \(32\-bit\), VB 5\.0, VB 6\.0
**Category**       |[Internet/ HTML](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/internet-html__1-34.md)
**World**          |[Visual Basic](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/visual-basic.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/br1m5t0n3-irc-bot-tutorial__1-36814/archive/master.zip)





### Source Code

<style type="text/css">
 #server { background-color: #FFAAAA; font-family: Courier New;}
 #client { background-color: #AAAAFF; font-family: Courier New;}
 #code { background-color: #CCCCCC; font-family: Courier New;}
</style>
<H1>IRC Bot tutorial</H1>
These colors denote: <a id="server">what the server sends</a>, <a id="client">what the client sends</a> and <a id="code">pieces of code</a>
<BR>
Many of you have asked, "How did you make that?" referring to my IRC bot made in vb. Well, Let me explain. This tutorial will explain the process of making an IRC bot in any language but I'll use VB and the Winsock control for examples. Before you do any coding, go get the IRC RFC, it's a guidebook to the protocol and really helpful.<BR>
<BR>
The first thing to need to do is to create a form and drag in 1 Winsock control. The majority of your code will go in the _DataArrival sub, but it doesn't have to.<BR>
<BR>
Once you got the controls set up, switch to the code view, this is where you will hangout for the rest of your time with me....<BR>
<BR>
The first thing your bot should do is figure out what its name is, along with its real name and its username. I recommend creating a class to store these in. The class that I made was a simple one useing the wizard. It contained 4 properties that were strings, nick, username, realname, and ip. Just walk through with the wizard and set up those string properties, it's that easy. I will refer to this class as "Person", and you will want to "Dim bot as New Person". Now you have declared bot.nick, bot.realname, bot.username, you are ready to connect to a server. Well what are you waiting for? <BR>
<div id="code">Winsock1.connect "graz2.at.eu.undernet.org", "6667"<BR>
</div><BR>
Now that wasn't that hard was it? If you've been watching what comes in you'll notice that you've already started to receive information. But how does the server know who you are? Tell it! When the Winsock connects, the _Connect sub fires, in the sub you want to send the bot.* information to the server, but there is a special way that it has to be sent.<BR>
<BR>
<div id="client">USER <username> <hostname> <servername> <realname><BR>
NICK <nick><BR>
</div><BR>
Ahh! What's this? IRC protocol! Just fill in the blanks and send it to the server, use the .remotehost property for the <servername>. Your code should look something like this...<BR>
<div id="client">USER "bot.nick" "" "<servername>" "bot.realname"<BR>
NICK "bot.nick"<BR>
</div><BR>
But you have to encapsulate the string in double quotes, terminate all original double quotes (that gets messy) then send it, so you get something that ends up looking like this...<BR>
<div id="code">winsock1.send "USER " & bot.Nick & " " & """" & """" & " " & """" & Winsock1.RemoteHost & """" & " " & bot.realname & vbCrLf<BR>
winsock1.send "NICK """ & bot.nick & """" & vbCrLf<BR>
</div><BR>
Yes, that's four (4) double quotes (") near the end of that last statement<BR>
Well, now that the server knows who you are, it will want to ping you, to make sure that you're still alive....<BR>
<div id="server">PING :12345678<BR>
</div><BR>
Well? Say pong back<BR>
<div id="client">PONG :12345678<BR>
</div><BR>
But you have to change the PING to PONG and then send it to Winsock<BR>
<div id="code">winsock1.send replace(incommingstring, "PING", "PONG") & vbCrLf<BR>
</div><BR>
But that's only when the start of the incommingstring is "PING", so throw in an If test and you're good to go...<BR>
<div id="code">If Left(incommingstring, len("PING")) = "PING" Then<BR>
...<BR>
End If<BR>
</div><BR>
Wohhh! What's all this information that the server's sending!? It's the message of the day, or MOTD for short you can disregard most if not all of it. But look at the syntax of these commands, they all start with a : and the name that the server wants to be called... you'll have to remember that to tell if it's the server sending you messages or someone is trying to send you a message.<BR>
<BR>
Wow, we've done a lot in a few lines of code, we've connected to a server, told it who we are, and now what? Join a channel!<BR>
<div id="client">JOIN <channel><BR>
</div><BR>
Gee, now that wasn't that hard was it? More information coming! Looks like a listing of the names in the channel!<BR>
<div id="server">:<servername> 372 <bot.nick> @ <channel> <bot.nick> @oldbot @X<BR>
</div><BR>
Well! It looks like our buddy X and oldbot are in here, how nice of them to protect the channel for us...<BR>
<div id="server">:<servername> 321 <channel> TOPIC :Welcome to <channel><BR>
</div><BR>
At least there is a topic here, X does do a good job of keeping the topic... Let's change it!<BR>
<div id="client">TOPIC <channel> :Testing bot<BR>
</div><BR>
Now that wasn't so hard?<BR>
<div id="server">:<servername> 417 :You're not channel operator<BR>
</div><BR>
I really wish they would change the grammar in that error message, but it's basically telling us that we're not a channel operator, just a normal user. So, let's get ops. To do so, we must send a message to our good buddy oldbot.<BR>
<div id="client">PRIVMSG oldbot :login <bot.nick> password<BR>
</div><BR>
The PRIVMSG command refers to a simple text message the first parameter can be a user or a channel<BR>
<div id="server">:oldbot!~bot@my.isp.com NOTICE :Password Accepted<BR>
</div><BR>
It's that nice our old bot has informed us that it liked our password, now let's get ops.<BR>
<div id="client">PRIVMSG <channel> :-op<BR>
</div><BR>
Another message to a channel<BR>
<div id="server">:oldbot!~bot@my.isp.com MODE <channel> +o <bot.nick><BR>
</div><BR>
Warning, this isn't as confusing as it looks. It's just saying that oldbot changed the mode of <bot.nick> to +o, +o just happens to require a channel, which is <channel>, now let's kick out our oldbot<BR>
<div id="client">KICK <channel> oldbot<BR>
</div><div id="server">:<bot.nick>!<bot.username>@<bot.ip> KICK <channel> oldbot<BR>
</div><BR>
You'll notice that almost every command that we send to the server is echoed back, when it's not echoed back and it should be you should ping the server just to make sure that it's still there and someone hasn't unplugged you<BR>
<div id="client">PING :12345<BR>
</div><div id="server">PONG :12345<BR>
</div><BR>
Good, the server's still up. Well, you can continue this sending and receiving of commands 'til your hearts content, but for the purpose of this tutorial, let's leave IRC with a message for all to see<BR>
<div id="client">QUIT :I learned the basics of the IRC Protocol!<BR>
</div><BR>
This is the end of the tutorial, but not the extent of the IRC protocol, there are two other protocols embedded within IRC, CTCP and DCC, client-to-client-protocol and direct-client-to-client. CTCP goes through the server, DCC doesn't. CTCP is used for version identification and client pings, DCC is used for the sending of files, and chat that doesn't go through the server.<BR>
<div id="code">End<BR>
</div><BR>
<a href="http://www.pscode.com/vb/scripts/ShowCode.asp?txtCodeId=36841&lngWId=1">Link to source code for a vb irc bot.</a>

