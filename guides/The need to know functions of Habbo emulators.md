## The habbo protocol consists of messages.

> by [Nillus](https://forum.ragezone.com/members/315061.html) on [ragezone](https://forum.ragezone.com/f331/guide-coding-functions-habbo-emulators-477478/)

### Client > server messages

These messages have a 'header', which is just their ID encoded in Base64, so 'B@' (pet stats btw) isn't just some random thing, no, decode it with a Habbo base64 encoder & you'll see that it's the message 128\. Encode it & it will become 'B@' again.  
 It's easier to 'recognize' the messages by their recognized header than by their ID. (atleast it is for me)  

### Server > client messages

Here we still have the encoded Base64 IDs, but now about the content of server>client messages;:  
 The content which is often a mixture of text and wire encoded ('VL64') values, if there are more strings in a message than they are often 'broken' by a char2, a char13 etc.  
 Each message needs to have an end, so the client can, upon receiving it, see 'hey', this is the full message, I can process it now.  
 In Habbo this is done by 'char1', it let's the client know that it's the end of the packet and it doesn't have to wait for more data, it can process the message. (That's why you could do the funniest things with it when scripting, if you were able to inject char1 you could put a message in your motto & end it with a char1, so the client will see it as a real packet & process it)  

When the server/client message is received a t the client/server, then it Base64 decodes the first two chars of the message string, so they have the message ID.  
 Client/server then processes the correct again for the given message and it's content.  
 For the Habbo server, it's unknown how it's done, since we haven't got sourcecode, and so we can only guess. Java does not support switching on string objects (which is rather innefficent since it is, next to stupid, much slower than switching on numbers), so they had to come up with something else.  
 I don't think Sulake's server works with a switch statement to select the correct action. (like it has been on all retro servers until Joeh's Thor. Thor works with 'reactor' classes hooked to the connection, each 'reactor' contains 'various' listeners for certain messages, the server searches through all hooked 'reactors' and tries to invoke the correct method, this is pretty efficient, saves alot of dataF pushing etc & so it's done in Woodpecker too now)  

As for the Habbo client, we kinda how how it processes messages, since we can decompile it and see cast names and the handlers etc.  
 I posted this a while back:  

> This is from Matt of SOM, it's a list of all the handlers inside the classes of the Habbo client. (V26 I guess?)  
>  This is what is triggered in the client by server messages.  
>  To understand it:  
>  The number before the handler is the message ID, but you guys are more familar with headers, which is the message ID encoded in Base64, so for the first three ones:  
>  0 = "@@"  
>  1 = "@A"  
>  2 = "@B"  
>    
> And a final test:  
>  312: [["One Way Door Manager", #changeStatus]]  
>  312 in Base64: 'Dx', which is the ofcourse the 'header' of the message of entering a onewaydoor.  
>  See how it works? ![](https://forum.ragezone.com/images/smilies/wink.gif "Wink")  
>   
> 
>     
>     0: [[#login_handler, #handleHello]]
>     1: [[#login_handler, #handleSecretKey]]
>     2: [[#login_handler, #handleRights]]
>     5: [[#login_handler, #handleUserObj]]
>     35: [[#login_handler, #handleUserBanned]]
>     50: [[#login_handler, #handlePing]]
>     52: [[#login_handler, #handleEPSnotify]]
>     139: [[#login_handler, #handleSystemBroadcast]]
>     141: [[#login_handler, #handleCheckSum]]
>     161: [[#login_handler, #handleModAlert]]
>     229: [[#login_handler, #handleAvailableBadges]]
>     257: [[#login_handler, #handleSessionParameters]]
>     277: [[#login_handler, #handleCryptoParameters]]
>     278: [[#login_handler, #handleEndCrypto]]
>     287: [[#login_handler, #handleHotelLogout]]
>     308: [[#login_handler, #handleSoundSetting]]
>     354: [[#login_handler, #handleLatencyTest]]
>     290: [[#openinghours_handler, #handleAvailabilityStatus]]
>     291: [[#openinghours_handler, #handleInfoHotelClosing]]
>     292: [[#openinghours_handler, #handleInfoHotelClosed]]
>     293: [[#openinghours_handler, #handleAvailabilityTime]]
>     294: [[#openinghours_handler, #handleLoginFailedHotelClosed]]
>     12: [[#friend_list_handler, #handleFriendListInit]]
>     13: [[#friend_list_handler, #handleFriendListUpdate]]
>     132: [[#friend_list_handler, #handleFriendRequest]]
>     260: [[#friend_list_handler, #handleError]]
>     314: [[#friend_list_handler, #handleFriendRequestList]]
>     315: [[#friend_list_handler, #handleFriendRequestResult]]
>     349: [[#friend_list_handler, #handleFollowFailed]]
>     363: [[#friend_list_handler, #handleMailNotification]]
>     364: [[#friend_list_handler, #handleMailCountNotification]]
>     134: [[#instant_messenger_handler, #handleIMMessage]]
>     135: [[#instant_messenger_handler, #handleIMInvitation]]
>     261: [[#instant_messenger_handler, #handleIMError]]
>     262: [[#instant_messenger_handler, #handleInvitationError]]
>     387: [[#ig_handler, #handle_directory_status]]
>     388: [[#ig_handler, #handle_ENTER_ARENA_FAILED]]
>     389: [[#ig_handler, #handle_GAME_REJOIN]]
>     390: [[#ig_handler, #handle_player_exited_game_arena]]
>     391: [[#ig_handler, #handle_level_hall_of_fame]]
>     392: [[#ig_handler, #handle_start_failed]]
>     393: [[#ig_handler, #handle_join_failed]]
>     394: [[#ig_handler, #handle_in_arena_queue]]
>     395: [[#ig_handler, #handle_stage_still_loading]]
>     396: [[#ig_handler, #handle_game_not_found]]
>     399: [[#ig_handler, #handle_game_chat]]
>     400: [[#ig_handler, #handle_enter_arena]]
>     401: [[#ig_handler, #handle_arena_entered]]
>     402: [[#ig_handler, #handle_load_stage]]
>     403: [[#ig_handler, #handle_stage_starting]]
>     404: [[#ig_handler, #handle_stage_running]]
>     405: [[#ig_handler, #handle_stage_ending]]
>     406: [[#ig_handler, #handle_game_ending]]
>     407: [[#ig_handler, #handle_game_created]]
>     408: [[#ig_handler, #handle_game_long_data]]
>     409: [[#ig_handler, #handle_create_game_info]]
>     410: [[#ig_handler, #handle_game_list]]
>     413: [[#ig_handler, #handle_user_joined_game]]
>     414: [[#ig_handler, #handle_user_left_game]]
>     415: [[#ig_handler, #handle_game_observation_started_short]]
>     416: [[#ig_handler, #handle_game_cancelled]]
>     417: [[#ig_handler, #handle_game_long_data]]
>     418: [[#ig_handler, #handle_game_started]]
>     355: [[#guide_handler, #handleInvitation]]
>     359: [[#guide_handler, #handleInvitationFollowFailed]]
>     360: [[#guide_handler, #handleInvitationCancelled]]
>     425: [[#guide_handler, #handleInitTutorServiceStatus]]
>     426: [[#guide_handler, #handleEnableTutorServiceStatus]]
>     163: [[#getServerDate, #handle_date]]
>     300: [[#soundmachine_handler, #handle_song_info]]
>     301: [[#soundmachine_handler, #handle_machine_sound_packages]]
>     302: [[#soundmachine_handler, #handle_user_sound_packages]]
>     332: [[#soundmachine_handler, #handle_invalid_song_name]]
>     322: [[#soundmachine_handler, #handle_song_list]]
>     323: [[#soundmachine_handler, #handle_play_list]]
>     324: [[#soundmachine_handler, #handle_song_missing_packages]]
>     325: [[#soundmachine_handler, #handle_play_list_invalid]]
>     326: [[#soundmachine_handler, #handle_song_list_full]]
>     331: [[#soundmachine_handler, #handle_new_song]]
>     333: [[#soundmachine_handler, #handle_user_song_disks]]
>     334: [[#soundmachine_handler, #handle_jukebox_disks]]
>     335: [[#soundmachine_handler, #handle_jukebox_song_added]]
>     336: [[#soundmachine_handler, #handle_song_locked]]
>     337: [[#soundmachine_handler, #handle_jukebox_playlist_full]]
>     338: [[#soundmachine_handler, #handle_invalid_song_length]]
>     339: [[#soundmachine_handler, #handle_song_saved]]
>     365: [[#roomdimmer_handler, #handleDimmerPresets]]]
>     148: [[#hobba_handler, #handle_cryforhelp]]
>     149: [[#hobba_handler, #handle_picked_cry]]
>     273: [[#hobba_handler, #handle_delete_cry]]
>     274: [[#hobba_handler, #handle_cry_reply]]
>     299: [[#error_report_handler, #handle_error_report]]
>     59: [[#roomkiosk_handler, #handle_flatcreated]]
>     33: [[#roomkiosk_handler, #handle_error]
>     [#room_handler, #handle_error]
>     [#navigator_handler, #handle_error]
>     [#login_handler, #handleErr]]
>     353: [[#roomkiosk_handler, #handle_webShortcut]]
>     319: [[#dialogs_handler, #handle_get_pending_response]]
>     320: [[#dialogs_handler, #handle_pending_CFHs_deleted]]
>     321: [[#dialogs_handler, #handle_cfh_sending_response]]
>     24: [["Chat Manager", #handle_chat]]
>     25: [["Chat Manager", #handle_chat]]
>     26: [["Chat Manager", #handle_chat]]
>     312: [["One Way Door Manager", #changeStatus]]
>     -1: [[#room_handler, #handle_disconnect]
>     [#login_handler, #handleDisconnect]]
>     18: [[#room_handler, #handle_clc]]
>     19: [[#room_handler, #handle_opc_ok]]
>     28: [[#room_handler, #handle_users]]
>     29: [[#room_handler, #handle_logout]]
>     30: [[#room_handler, #handle_OBJECTS]]
>     31: [[#room_handler, #handle_heightmap]]
>     32: [[#room_handler, #handle_activeobjects]]
>     34: [[#room_handler, #handle_status]]
>     41: [[#room_handler, #handle_flat_letin]]
>     45: [[#room_handler, #handle_items]]
>     42: [[#room_handler, #handle_room_rights]]
>     43: [[#room_handler, #handle_room_rights]]
>     46: [[#room_handler, #handle_flatproperty]]
>     47: [[#room_handler, #handle_room_rights]]
>     48: [[#room_handler, #handle_idata]]
>     62: [[#room_handler, #handle_doorflat]]
>     63: [[#room_handler, #handle_doordeleted]]
>     64: [[#room_handler, #handle_doordeleted]]
>     69: [[#room_handler, #handle_room_ready]]
>     70: [[#room_handler, #handle_youaremod]]
>     71: [[#room_handler, #handle_showprogram]]
>     76: [[#room_handler, #handle_no_user_for_gift]]
>     83: [[#room_handler, #handle_items]]
>     84: [[#room_handler, #handle_removeitem]
>     [#buffer_handler, #handle_removeitem]]
>     85: [[#room_handler, #handle_updateitem]
>     [#buffer_handler, #handle_updateitem]]
>     88: [[#room_handler, #handle_stuffdataupdate]
>     [#buffer_handler, #handle_stuffdataupdate]]
>     89: [[#room_handler, #handle_door_out]]
>     90: [[#room_handler, #handle_dice_value]]
>     91: [[#room_handler, #handle_doorbell_ringing]]
>     92: [[#room_handler, #handle_door_in]]
>     93: [[#room_handler, #handle_activeobject_add]]
>     94: [[#room_handler, #handle_activeobject_remove]
>     [#buffer_handler, #handle_activeobject_remove]]
>     95: [[#room_handler, #handle_activeobject_update]
>     [#buffer_handler, #handle_activeobject_update]]
>     98: [[#room_handler, #handle_stripinfo]]
>     99: [[#room_handler, #handle_removestripitem]]
>     101: [[#room_handler, #handle_stripupdated]]
>     102: [[#room_handler, #handle_youarenotallowed]]
>     103: [[#room_handler, #handle_othernotallowed]]
>     105: [[#room_handler, #handle_trade_completed]]
>     108: [[#room_handler, #handle_trade_items]]
>     109: [[#room_handler, #handle_trade_accept]]
>     110: [[#room_handler, #handle_trade_close]]
>     112: [[#room_handler, #handle_trade_completed]]
>     129: [[#room_handler, #handle_presentopen]]
>     131: [[#room_handler, #handle_flatnotallowedtoenter]]
>     140: [[#room_handler, #handle_stripinfo]]
>     208: [[#room_handler, #handle_roomad]]
>     210: [[#room_handler, #handle_petstat]]
>     219: [[#room_handler, #handle_heightmapupdate]]
>     228: [[#room_handler, #handle_userbadge]]
>     230: [[#room_handler, #handle_slideobjectbundle]]
>     258: [[#room_handler, #handle_interstitialdata]]
>     259: [[#room_handler, #handle_roomqueuedata]]
>     254: [[#room_handler, #handle_youarespectator]]
>     283: [[#room_handler, #handle_removespecs]]
>     266: [[#room_handler, #handle_figure_change]]
>     298: [[#room_handler, #handle_spectator_amount]]
>     309: [[#room_handler, #handle_group_badges]]
>     310: [[#room_handler, #handle_group_membership_update]]
>     311: [[#room_handler, #handle_group_details]]
>     345: [[#room_handler, #handle_room_rating]]
>     350: [[#room_handler, #handle_user_tag_list]]
>     361: [[#room_handler, #handle_user_typing_status]]
>     362: [[#room_handler, #handle_highlight_user]]
>     367: [[#room_handler, #handle_roomevent_permission]]
>     368: [[#room_handler, #handle_roomevent_types]]
>     369: [[#room_handler, #handle_roomevent_list]]
>     370: [[#room_handler, #handle_roomevent_info]]
>     419: [[#room_handler, #handle_ignore_user_result]]
>     420: [[#room_handler, #handle_ignore_list]]
>     3: [[#habbo_club_handler, #handle_ok]
>     [#login_handler, #handleLoginOK]
>     [#friend_list_handler, #handleOk]]
>     7: [[#habbo_club_handler, #handle_scr_sinfo]]
>     280: [[#habbo_club_handler, #handle_gift]]
>     4: [[#photo_handler, #handle_film]]
>     16: [[#navigator_handler, #handle_flat_results]]
>     54: [[#navigator_handler, #handle_flatinfo]]
>     55: [[#navigator_handler, #handle_flat_results]]
>     57: [[#navigator_handler, #handle_noflatsforuser]]
>     58: [[#navigator_handler, #handle_noflats]]
>     61: [[#navigator_handler, #handle_favouriteroomresults]]
>     130: [[#navigator_handler, #handle_flatpassword_ok]]
>     220: [[#navigator_handler, #handle_navnodeinfo]]
>     221: [[#navigator_handler, #handle_userflatcats]]
>     222: [[#navigator_handler, #handle_flatcat]]
>     223: [[#navigator_handler, #handle_spacenodeusers]]
>     224: [[#navigator_handler, #handle_cantconnect]]
>     225: [[#navigator_handler, #handle_success]]
>     226: [[#navigator_handler, #handle_failure]]
>     227: [[#navigator_handler, #handle_parentchain]]
>     286: [[#navigator_handler, #handle_roomforward]]
>     351: [[#navigator_handler, #handle_recommended_room_list]]
>     6: [[#catalogue_handler, #handle_purse]]
>     67: [[#catalogue_handler, #handle_purchase_ok]]
>     65: [[#catalogue_handler, #handle_purchase_error]]
>     68: [[#catalogue_handler, #handle_purchase_nobalance]]
>     126: [[#catalogue_handler, #handle_catalogindex]]
>     127: [[#catalogue_handler, #handle_catalogpage]]
>     296: [[#catalogue_handler, #handle_purchasenotallowed]]
>     295: [[#dynamicdownloader_handler, #handle_furni_revisions]]
>     297: [[#dynamicdownloader_handler, #handle_alias_list]]
>     303: [[#recycler_handler, #handle_recycler_configuration]]
>     304: [[#recycler_handler, #handle_recycler_status]]
>     305: [[#recycler_handler, #handle_approve_recycling_result]]
>     306: [[#recycler_handler, #handle_start_recycling_result]]
>     307: [[#recycler_handler, #handle_confirm_recycling_result]]
>     316: [[#poll_handler, #handle_poll_offer]]
>     317: [[#poll_handler, #handle_poll_contents]]
>     318: [[#poll_handler, #handle_poll_error]]
>     352: [[#new_user_help_handler, #handleHelpItems]]
>     356: [[#new_user_help_handler, #handleTutorsAvailable]]
>     357: [[#new_user_help_handler, #handleInvitingCompleted]]
>     358: [[#new_user_help_handler, #handleInvitationExists]]
>     421: [[#new_user_help_handler, #handleInvitationSent]]
>     423: [[#new_user_help_handler, #handleGuideFound]]
>     424: [[#new_user_help_handler, #handleInviterLeftRoom]]
>     166: [[#statsBrokerJs, #handle_update_stats]]
> 
>   
> The last things are about Guides etc, as you can see.  
>  Just use my Nillus Packet Scout app & it's Base64 encoder to convert the message ID's to their encoded equivalents.

That's inside the Habbo client, you see that each server>client message (for example, 'BK', 139), is for '[[#login_handler, #handleSystemBroadcast]]', which is exactly what it does.  
 Then we have '@c', 35, which becomes '[[#login_handler, #handleUserBanned]]', and so on.  
 Etc, you know how it works now I guess.  

You just have to understand that;  
 - Habbo protocol consists out of messages  
 - Message 'header' isn't just random chosen, it's the ID of the message encoded in Base64  
 - Messages consist of: encoded ID + content separated by char2 etc/wire encoded values + char1 to finalize the message & tell client message is complete  
 - Nothing in the message is static except the encoded ID and the char1 at the end, you can't just raw packetlog anything & paste it in your server  

This is how the 'change console mission' works in Woodpecker;  

           /// 
            /// "@d" - "MESSENGER_ASSIGNPERSMSG"
            /// 
            public void Listener36()
            {
                string newMotto = Request.getParameter(0);
                Session.User.messengerMotto = newMotto;
                Session.User.updateAppearanceDetails();

                Response.Initialize(147); // "BS"
                Response.appendClosedValue(newMotto);
                sendResponse();
            }  

See, the current message is stored in 'Request', which is a string object with various cool handy things, I can easily get the first Base64 parameter of it etc.  
 The new server>client message is called Response, and by using it's 'Initialize' method, I can assign it a ID.  
 Then I can add values to it, here I'm using 'appendClosedValue' to append a value + char2, but it also has 'appendWired(NUMBER)' etc.  
 Finally I use 'sendResponse' to convert the current 'response' to a string object, append char1 to close it & then send it to the client.  
 Then I can do 'Response.Initialize' again to start a new response, etc.  
 It's just more structured than the old servers, thanks to Joeh.  

Hope that helped you out a bit, as a mod you could sticky it since it's some basic knowledge about the Habbo protocol? ![](https://forum.ragezone.com/images/smilies/wink.gif "Wink")  
Most people don't know more than 'Send Index, 'BKHAHAHA THIS IS A MESSAGE' & Chr(1)', lmfao. ![](https://forum.ragezone.com/images/smilies/tongue_smilie.gif "Tongue Smilie")  

- Nillus  

**From another thread - thanks to Nillus once again.**  
 VL64 is most used in server > client packets.  
 What's VL64? Well, it's an encoding for numbers, it makes numbers 'understandable' for the Habbo client.  
 Example;  
 I = 1  
 J = 2  
 K = 3  
 PA = 4  

You can see an ascending of the numbers, see? [1=2-3]  
 And you also see something of the alphabet in it. [I-J-K]  
 -1 in VL64 is M.  

Okay, that looks pretty easy.  
 How does a 'big' number in VL64 looks like?  
 We'll encode 8123732.  
 Result: "hUuoG"  
 Ah yeah! Self explaining!  
 Not really lol.  
 Well, if you see the code of the encoding (can be found in JASE etc, HabboEncoding class), it's pretty logical.  

But why using such cryptic things just for numbers?  
 At packets, Habbo likes to use alot of numbers after each other without delimiters.  
 Like if you want to use 10, 39, 843, and 7 after each other in a packet, you can do 10398437, but then it's a whole different number.  
 There's where VL64 comes in.  
 Let's encode the numbers 10, 39, 843 and 7...  
 **10:** RB  
 **39:** SI  
 **843:** [RC  
 **7:** SA  

"Ehm, yeh, and now? Why?"  
 Well, VL64 has a 'magical' part that makes it that good as it is:  
 _If you decode a whole VL64 string, so like **RBSI[RCSA**, it'll return as number ONLY the first encoded number in the string!_ So, only the 10 (the first encoded number of that string) will be the result, while you've inputted a whole string with three other VL64 numbers!  

Now it'll get tricky, but I'll show you how to decode a string like that to it's original numbers.  
 1) Decode _**RBSI[RCSA**_, 10 will be the result  
 2) Encode 10 to VL64, so it'll become **RB**.  
 3) RB is 2 characters, R-B  
 4) Now we 'cut' the first number (RB = 10) from the string _**RBSI[RCSA**_, and we know the lenght of the first part. In a .NET language for example, we can do that by this way;  
 STRING OBJECT.Substring(start,length).  
 If length isn't supplied than it will trigger an overload (a 'brother' of a void/function) that will simply 'run to the end of the string', so taking everything after 'start'. That's what we want, since we only want to cut off the first part and keep the other part.  
 So: STRING OBJECT.Substring(2)  
 It'll result in;  
 _**SI[RCSA** _  
Okay, now I can say, just repeat till you are done! =D  
 Keep decoding the whole string to one number, store the number somewhere to work with it or w/e, and encode the number to a VL64 string, get it's length (STRING OBJECT.Length property in .NET), and keep the string after that part by using string manipulators such as .Substring.  

It appears that it's hard to explain lol, code says more than thousand words! ;P  

    // encodeVL64, returns a string
    // decodeVL64, returns an integer

    string SomeInput = "PAJIKPASASF";
    while(SomeInput != "") // Keep looping this loop till the string is empty (so, completed with decoding)
    {
    int currentNumber = decodeVL64(SomeInput);
    // Do something with current number, write it in database or something, just where you need the number for
    int currentNumberLength = encodeVL64(currentNumber).ToString().Length; // Encode the number to it's VL64 equivalent to see what it would be if you only encoded that number to VL64, and get it's length
    someInput = someInput.Substring(currentNumberLength); // Only keep the part of SomeInput after the string
    } // Proceed to next cycle of the loop (so, decode the next number)  

That's how you can use alot of numbers after each other, while the numbers stay on their own && this is what Habbo wants to keep their packets small.  

I hope you have understood something of it.  

Anyway, that's what the VL64 analyzer in Nillus Packet Scout does, and tbh, it helps me alot at finding packet structures.  

I'll show how it can decode a BattleBall packet for you, which gives you alot more 'understanding' of how it works.  
 Full packet + header:  
 _Ct_**IQAJPCSAIJPCHJHJXKDX]BIIJQAPCRE**  
 Okay, Ct is the header, but it looks like a load of crap.  
 Nope, it are just numbers!  
 Let's shove the part after the header ("Ct") in my app and hit the button...  
 Result:  

> Result of this VL64 scout session was:   
>  # I = 1  
>  # QA = 5  
>  # J = 2  
>  # PC = 12  
>  # SA = 7  
>  # I = 1  
>  # J = 2  
>  # PC = 12  
>  # H = 0  
>  # J = 2  
>  # H = 0  
>  # J = 2  
>  # XKD = 1068  
>  # X]B = 628  
>  # I = 1  
>  # I = 1  
>  # J = 2  
>  # QA = 5  
>  # PC = 12  
>  # RE = 22  
>    
> Processed input string summary: I-QA-J-PC-SA-I-J-PC-H-J-H-J-XKD-X]B-I-I-J-QA-PC-RE

Woo, numbers!  
 There you can do something with, can't you?  

& That's where it comes handy while coding.  
 How it works?  
 It just runs that loop till the string is empty, and outputting the result after decoding done.  
 A good example of where it's used is BattleBall/SnowStorm and the Recycler/Ecotron, alot of numbers after each other as tidy as possible.
 
 ![](http://i.imgur.com/wzWspPr.png)
