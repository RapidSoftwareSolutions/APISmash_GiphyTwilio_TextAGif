#Text A GIF Project: Giphy and Twilio API Smash#

(adapted from a [RapidAPI blog post](http://blog.rapidapi.com/2016/12/07/giphy-and-twilio-text-a-gif-project-api-smash/) ) 

We're starting a series where we smash two APIs together to make a project. Today? We're putting together GIPHY and Twilio to make "Text A GIF" script that texts someone a GIF based on a user-inputed phrase.

![api-smash-5-copy](http://blog.rapidapi.com/wp-content/uploads/2016/12/API-Smash-5-copy.png)

 Read on for more!

### **Demo: "Text a GIF" **

We created a "Text a GIF" app that runs in the Terminal.  The user types in a phrase and a phone number and the "Text A GIF" app sends a related, random GIF. 

Visit this link to see a demo: https://media.giphy.com/media/3o6Zt2Z8gjq8KqNRsc/giphy.gif

Aaaaaand here's the source code.  

    var RapidAPI = new require('rapidapi-connect');
    var rapid = new RapidAPI('Demo', '############'); //Your RapidAPI Account number

    //Retrieving GIF text and target phone number from command line
    var gifText = process.argv[2];
    var phoneNumber = process.argv[3];

    rapid.call('Giphy', 'translateTextToGif', { 
        'rating': 'pg',
        'apiKey': '###########', //This is GIPHY's public beta key
        'text': gifText, //This value needs to be input by user
        'lang': 'en'
    }).on('success', (payload)=>{
        var gif = payload.data.images.fixed_height.url;
        rapid.call('Twilio', 'sendMms', { 
            'from': '+###########', //This is the phone number you register on Twilio
            'to': phoneNumber,//This value needs to be input by user
            'applicationSid': '',
            'statusCallback': '',
            'accountSid': '####################',//This is your Twilio accountSid number (from registering a Twilio account)
            'accountToken': '###################',//This is your Twilio accountToken (from registering a Twilio account)
            'messagingServiceSid': '################',//This is your Twilio messagingSid number (from registering Twilio    messaging functionality)
            'mediaUrl': gif, //This link will be generated by Giphy API
            'maxPrice': '',
            'provideFeedback': ''
        }).on('success', (payload)=>{

             console.log("Check your phone!");
        }).on('error', (payload)=>{
             console.log("Error sending MMS");
        });

    }).on('error', (payload)=>{
        res.send("Error finding GIF");
    });


We built this project by exporting JavaScript code snippets from RapidAPI's [GIPHY](https://goo.gl/LZ2Cuq) and [Twilio API](https://rapidapi.com/package/Twilio/functions?utm_source=GitHub&utm_medium=GitHubReadME&utm_content=APISmash_TwilioFunctions1) packages. You can recreate this app in your preferred language by filling in the parameters on RapidAPI and exporting the snippet in the language of your choice (ex. Python, PHP, Java, Objective-C and cURL). 

![giphy-5](http://blog.rapidapi.com/wp-content/uploads/2016/12/giphy-5.gif) 

Now, here's how we built it!

### **Step 1: Storing user inputs in variables**

The first lines of code call RapidAPI. (If you haven't already installed it, you'll be walked through that process when you sign up for a free RapidAPI account). 

    var RapidAPI = new require('rapidapi-connect');
    var rapid = new RapidAPI('Demo', '############'); //Your RapidAPI Account number
    
Next, the "Text a GIF" app requires the user to input text and a phone number from the command line. The following lines of code store these user inputs into two variables.

    //Retrieving GIF text and target phone number from command line
    var gifText = process.argv[2];
    var phoneNumber = process.argv[3];

_Note: In order for Twilio to read the phone number, it must be written in [E.164 formatting](https://en.wikipedia.org/wiki/E.164). All that means is “+” then country code, then number, with no dashes or spacing. For example, +18008675309._

### **Step 2: Connecting to the GIPHY API**

**How to Call the GIPHY API** Our next step was [connecting to the GIPHY via the RapidAPI package](https://rapidapi.com/package/GIPHY/functions?utm_source=Blog&utm_content=APISmash_GIPHYFunctions2) and exporting the code snippet in JavaScript. The endpoint we used for this app was `translateTextToGif`.  This endpoint generates a GIF based on the word that you type in. You can even filter the results by rating (G, PG, PG-13, R) and language (using the [ISO 639-1 country cod](https://www.loc.gov/standards/iso639-2/php/code_list.php)e--English is "en"). You can [test this endpoint](https://goo.gl/S2vJYl) right within your browser. Type in the parameters above and use **dc6zaTOxFJmzC** as an `access_token`. GIPHY provides a public beta key for anyone to use. (If you want to submit an app for production, you can request a production key [here](http://api.giphy.com/submit).)

![giphy-6](http://blog.rapidapi.com/wp-content/uploads/2016/12/giphy-6.gif)    

**Incorporating the GIPHY API into the code** Using the `translateTextToGiF` endpoint, the GIPHY API generates a GIF based on the text the user inputed into the Terminal (in this case, `gifText` ).  

    rapid.call('Giphy', 'translateTextToGif', { 
        'rating': 'pg',
        'apiKey': '###########', //This is GIPHY's public beta key
        'text': gifText, //This value needs to be input by user
        'lang': 'en'
    }).on('success', (payload)=>{
        ....call Twilio API here...
        });

    }).on('error', (payload)=>{
        res.send("Error finding GIF");
    });

If the call is successful, the script will call Twilio's API to text a MMS with the generated GIF. If the call isn't successful, the script will read an error message that lets the user know the problem was with the GIF generation.

### **Step 3: Call the Twilio API**

**How to call the Twilio API** 
Our next step was connecting to the [Twilio API](https://goo.gl/RAvGz4) to send a text message. In order to connect to the Twilio API, you'll need an `accountToken`, `accountSid`, `messagingSid` and to generate a Twilio phone number. These can all be generated for free on the [Twilio developer porta](https://www.twilio.com/try-twilio)l. Here's a [quick post](http://blog.rapidapi.com/2016/10/22/how-to-connect-to-the-twilio-api/) on how to get the credentials that you need. 

_Note: If you generate a <span style="text-decoration: underline;">free</span> "from" phone number with your Twilio developer account, then the phone number you associate with the free "from" number is the ONLY one you can send messages to. In this case, the "Text A GIF" app will only send GIFs to your phone number. In order to send a message to any number, you must purchase an upgrade (see more in the [Twilio developer console](http://twilio.com/console)). _ 

**Incorporating the Twilio API into the code** 
For the "Text a GIF" project, we called the `sendMms` endpoint. Using this endpoint, Twilio can send a multi-media message to the phone number the user entered (in this case, the variable `phoneNumber`). The GIPHY API `translateTextToGiF` endpoint returns a variety of data. To store the image URL, we created a variable called `gif`.

    rapid.call('Giphy', 'translateTextToGif', { 
        'rating': 'pg',
        'apiKey': '###########', //This is GIPHY's public beta key
        'text': gifText, //This value needs to be input by user
        'lang': 'en'
    }).on('success', (payload)=>{
        var gif = payload.data.images.fixed_height.url;
        rapid.call('Twilio', 'sendMms', { 
            'from': '+###########', //This is the phone number you register on Twilio
            'to': phoneNumber,//This value needs to be input by user
            'applicationSid': '',
            'statusCallback': '',
            'accountSid': '####################',//This is your Twilio accountSid number (from registering a Twilio account)
            'accountToken': '###################',//This is your Twilio accountToken (from registering a Twilio account)
            'messagingServiceSid': '################',//This is your Twilio messagingSid number (from registering Twilio    messaging functionality)
            'mediaUrl': gif, //This link will be generated by Giphy API
            'maxPrice': '',
            'provideFeedback': ''
        }).on('success', (payload)=>{

             console.log("Check your phone!");
        }).on('error', (payload)=>{
             console.log("Error sending MMS");
        });

    }).on('error', (payload)=>{
        res.send("Error finding GIF");
    });

If the GIPHY API call is successful in generating a GIF, then the Twilio API will retrieve the image URL (`gif`) and text it to the phone number the user inputted initially (`phoneNumber`). The script will then prompt the user with "Check your phone!" If the Twilio API call is unsuccessful, the script will return the prompt "Error sending MMS."

### **Ta-da! **

![giphy-4](http://blog.rapidapi.com/wp-content/uploads/2016/12/giphy-4.gif) 

This article wouldn't be complete without at least one celebratory GIF. Let us know what you think of [this project] or give us some suggestions on which APIs we should mash up next!
