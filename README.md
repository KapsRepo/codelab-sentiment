# Alchemy API with Javascript

API documentation:  http://www.ibm.com/watson/developercloud/alchemy-language/api/v1/

1) Navigate to http://www.alchemyapi.com/ and obtain an API key

2) Workspace:  http://jsfiddle.net/mevey/wgL0vmy4/43/
   - Pre-filled HTML and CSS on top left and right
   - Edit Javascript on bottom left
   - Results on bottom right

3) On the Javascript section on JS-Fiddle, enter the Alchemy API key

```
key = '51e2eb05801719c0c12dd695520bbd2811c7781f'
```

4) Fill in the API URLs for each end-point. Reference: http://www.ibm.com/watson/developercloud/alchemy-language/api/v1/#sentiment

```
sentiment_api = 'https://gateway-a.watsonplatform.net/calls/url/URLGetTextSentiment?outputMode=json&apikey=' + key
entities_api = 'https://gateway-a.watsonplatform.net/calls/url/URLGetRankedNamedEntities?outputMode=json&sentiment=1&apikey=' + key
```

5) On your terminal, use curl to test the URLs and view the response format.
```
curl -X POST \
-d "outputMode=json" \
-d "url=http://www.huffingtonpost.com/2010/06/22/iphone-4-review-the-worst_n_620714.html" \
"https://gateway-a.watsonplatform.net/calls/url/URLGetTextSentiment?apikey=b66585d88e1c9cbb09218239e8b16ca03e17b567"
```


```
curl -X POST \
-d "outputMode=json" \
-d "maxRetrieve=3" \
-d "url=http://www-03.ibm.com/press/us/en/pressrelease/49384.wss" \
"https://gateway-a.watsonplatform.net/calls/url/URLGetRankedNamedEntities?apikey=b66585d88e1c9cbb09218239e8b16ca03e17b567"
```

6) Response format example:
```
{
    "status": "OK",
    "usage": "By accessing AlchemyAPI or using information generated by AlchemyAPI, you are agreeing to be bound by the AlchemyAPI Terms of Use: http://www.alchemyapi.com/company/terms.html",
    "totalTransactions": "1",
    "language": "english",
    "docSentiment": {
        "score": "0.603233",
        "type": "positive"
    }
}
```

7) Back on JS-Fiddle, write Javascript code to make a request to the API

```
	  $.ajax({
	    method: "POST",
	    url: sentiment_api + outputMode,
	    data: $('#form').serialize(),
	    contentType: 'application/x-www-form-urlencoded',
	    success: function(msg) {

		// Write your response handling code here

	    }
	  });

```

8) Go ahead and display your result (no. 6 above) on the HTML page.

9) An example of response handling for sentiment analysis:

```
	//Sentiment Analysis
	   $.ajax({
		method: "POST",
		url: sentiment_api + outputMode,
		data: $('#form').serialize(),
		contentType: 'application/x-www-form-urlencoded',
		success: function(msg) {
		
		
		  $('#sentiment-results').html('<span>' + msg.docSentiment.type + '</span> with a score of <span>' + msg.docSentiment.score + '</span>')
		  
		  
		}
	  });
	  
	  //Entity extraction
	  $.ajax({
		method: "POST",
		url: entities_api + outputMode,
		data: $('#form').serialize(),
		contentType: 'application/x-www-form-urlencoded',
		success: function(msg) {
		
		
			markup = ''
			for (k in msg.entities) {
				v = msg.entities[k]
				markup += '<span>Text: ' + v.text + '</span>  <span>count: ' + v.count + '</span>  <span>Type: ' + v.type + '</span>  Relevance: <span>' + v.relevance + '</span>  Sentiment: <span>' + v.sentiment.type + '</span><br>'
			}
			$('#entities-results').html(markup)
			
			
		}
	  });
```

10) Add another response handler for entity extraction.

11) Test.

![alt text](https://goo.gl/BGLvAg "Test screenshot")

=================================


# Polyglot

* What is Polyglot?

1) Clone the github repo: https://github.com/CodeForAfricaLabs/sentiment_icebreaker

```
git clone https://github.com/CodeForAfricaLabs/sentiment_icebreaker
```


1) Obtain Twitter API keys.

- Create a new Twitter app here: https://apps.twitter.com/
- Obtain Consumer Key, Consumer secret from the "Keys and Access Tokens" tab.
- Update permissions to allow for "Read, Write and Access direct messages"
- Generate access tokens and obtain the "Access Token" and "Access Token Secret"


2) Copy twitter API credentials into the configuration file: `twitter_/config.py`. 

```
TW_AUTH_CREDENTIALS['consumer_key'] = ""   # Consumer Key (API Key)
TW_AUTH_CREDENTIALS['consumer_secret'] = ""   # Consumer Secret (API Secret)
TW_AUTH_CREDENTIALS['access_token_key'] = ""  # Access Token
TW_AUTH_CREDENTIALS['access_token_secret'] = ""  # Access Token Secret
```

3) [OPTIONAL] Create a python virtualenv

```
pip install virtualenv
virualenv /path/to/your/env
```

4) Open the root directory `sentiment_icebreaker`

```
cd sentiment_icebreaker
```

5) Install python dependencies

```
pip install -r requirements.txt
```

:warning: OSX dependencies:
```
CFLAGS=-I/usr/local/opt/icu4c/include LDFLAGS=-L/usr/local/opt/icu4c/lib pip install pyicu
```

:warning: Ubuntu dependencies:
```
sudo apt-get install libicu-dev
```

6) Install polyglot language packs

```
make install-language-packs
```

7) Open `twitter_/listener.py`: Run through language detection
```
msg_text = payload["message"]
language_detector = Detector(msg_text)
language = language_detector.language.name
```

8) Run through sentiment analysis
```
try:
    msg_polarity = Text(msg_text).polarity
except ZeroDivisionError:
    msg_polarity = 0
```

9) Run through entity extraction
```
msg_entities = []
for entity in Text(msg_text).entities:
    msg_entities.append(entity)
```

10) Change the question on HTML: `web/app/templates/index.html`
```
<center><h1 class="question">Tell us what you think about this session.</h1></center>

```

11) Start the Twitter Direct Messages stream listener:
```
make start
```

12) Start the web server:
```
make webserver
```

:warning: If you encounter an issue with imports, 
```
export PYTHONPATH=/location/of/your/project/
```

:warning: If you encounter an issue with log file location
```
mkdir logs
```

:bulb: Tail logs to see app activity:
```
tail -f logs/twitter-stream.log
```




# Useful Links

* JS Fiddle:  http://jsfiddle.net/mevey/wgL0vmy4/24/
* Code: https://github.com/CodeForAfricaLabs/sentiment_icebreaker
* UG election sentiment analysis: https://goo.gl/jcuJjC
* Aleph:  https://data.investigativecenters.org/
* Alchemy API: http://www.ibm.com/watson/developercloud/alchemy-language/api/v1/
* Polyglot:  http://polyglot.readthedocs.io
* Semantria:  https://www.lexalytics.com/semantria
* Google Language APIs:  https://cloud.google.com/natural-language
* Eve: github.com/mevey | twitter.com/muthonieve
* Andrew: github.com/andkamau | twitter.com/andkamau


