---
title: "How Google Sheets and Webhooks made my life easier!"
datePublished: Wed Oct 13 2021 14:42:29 GMT+0000 (Coordinated Universal Time)
cuid: ckupmgyw611ujrvs13qrz8hqc
slug: how-google-sheets-and-webhooks-made-my-life-easier
canonical: https://dev.to/afrocoder/how-google-sheets-and-webhooks-made-my-life-easier-a05
tags: javascript, google, google-sheets

---

I work in Tech Support and Google Sheets helped me  automate repetitive tasks!

This helped me push messages to various teams who use __Google Chats__, the Webhook option present in Google Chat is amazing. 

The Goal was to track Incidents(Outages) and then push notifications to various teams which was manual before I found out about __Google Sheets Simple Triggers__.

Here are few links that might help you get started.

[How to Build Apps using Google App Script](https://www.google.com/script/start/)

[Read more about Google Sheets Triggers here](https://developers.google.com/apps-script/guides/triggers/)

[Read more about Google Chat Incoming webhooks](https://developers.google.com/hangouts/chat/how-tos/webhooks)


Now all we had to do was enter the data in a specific Cell and the `onEdit()` function would read it and post it to the necessary chat rooms.

You will need to add the trigger from
[Google Scripts](https://script.google.com)

Enough talk! Show me the code! 


```
function myEditNew(e) {
// The e is an event Object
//https://developers.google.com/apps-script/guides/triggers/events

if (e.value)
{
Logger.log(e.range);

Logger.log("Data: "+e.value);

var val=e.value;
// This function is to get the current user that created the task its documented in the Gist at the end of the post
var cur_user=getCurrentUserEmail();

var text = Utilities.formatString('Incident created by *%s*: ```%s``` <users/all>',cur_user,val);
}

//1. Bot Test room
// [Args[Webhook URL, Space Name, Thread Name]]

// To get the Thread ID read this Stackoverflow answer

// https://webapps.stackexchange.com/questions/117392/get-link-to-specific-conversation-thread-and-or-message-in-a-chat-room-in-google

var urls = [
  [
   "https://chat.googleapis.com/v1/spaces/room_id/messages?key=Webhook_ID",
   "spaces/room_id/messages/space_name.space_name",
   "spaces/Thread_ID/threads/Thread_ID"
   ],
  
];

// Lots of Logging to see if Things are working properly.
Logger.log(e.range.getA1Notation());
Logger.log(e.range.getColumn());
Logger.log(val);


if (e.range.getA1Notation().startsWith('C') && val != "")
{
// Synchronously Post it to the Rooms
for(i=0;i<urls.length;i++)
{
var payload={
  "name":urls[i][1],
  "thread":{
    "name":urls[i][2]
  },
  "text":text
};
var options = {
  'method' : 'post',
  'contentType': 'application/json',
  // Convert the JavaScript object to a JSON string.
  'payload' : JSON.stringify(payload)
};
Logger.log(options);

// UrlFetchApp Documentation
// https://developers.google.com/apps-script/reference/url-fetch/url-fetch-app
var response = UrlFetchApp.fetch(urls[i][0], options);
Logger.log(response);

}

}
}
```
[Github Gist of the entire code](https://gist.github.com/afro-coder/ebf01c3718f9be47f530168ed62fc13e)



Thank you for reading, if you like this article feel free to share it as always I'm open to constructive feedback and criticism, feel free to leave a comment or message me.