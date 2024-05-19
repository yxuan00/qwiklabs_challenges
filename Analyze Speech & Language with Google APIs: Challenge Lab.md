# Analyze Speech & Language with Google APIs: Challenge Lab
## Task 1: Create an API key
1. Navigation Menu > APIs & Services > Credentials
2. Create credentials > API key
3. Copy the generated API key and click Close

## Task 2. Make an entity analysis request and call the Natural Language API
1. Navigation Menu > Compute Engine > select provided instance, lab-vm > click SSH
```yaml
export API_KEY=<YOUR_API_KEY>
```
2. create json file
```yaml
touch nl_request.json
nano nl_request.json
```
```yaml
{
  "document":{
    "type":"PLAIN_TEXT",
    "content":"With approximately 8.2 million people residing in Boston, the capital city of Massachusetts is one of the largest in the United States."
  },
  "encodingType":"UTF8"
}
```
click CTRL+X > click y > click Enter
```yaml
curl "https://language.googleapis.com/v1/documents:analyzeEntities?key=${API_KEY}" \
  -s -X POST -H "Content-Type: application/json" --data-binary @nl_request.json > nl_response.json
```

## Task 3. Create a speech analysis request and call the Speech API
```yaml
touch speech_request.json
nano speech_request.json
```
Copy the provided file: gs://cloud-samples-tests/speech/brooklyn.flac
Replace <Pass the API the uri of the audio file in Cloud Storage> in json file with gs://cloud-samples-tests/speech/brooklyn.flac
The file will look like below
```yaml
copy the url created: gs://cloud-samples-tests/speech/brooklyn.flac
{
  "config": {
      "encoding":"FLAC",
      "languageCode": "en-US"
  },
  "audio": {
      "uri":"gs://cloud-samples-tests/speech/brooklyn.flac"
  }
}
```
click CTRL+X > click y > click Enter
```yaml
curl -s -X POST -H "Content-Type: application/json" --data-binary @speech_request.json \
"https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}"

curl -s -X POST -H "Content-Type: application/json" --data-binary @speech_request.json \
"https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" > speech_response.json
```

## Task 4. Analyze sentiment with the Natural Language API
```yaml
cat > sentiment_analysis.py <<EOF

import argparse

from google.cloud import language_v1

def print_result(annotations):
    score = annotations.document_sentiment.score
    magnitude = annotations.document_sentiment.magnitude

    for index, sentence in enumerate(annotations.sentences):
        sentence_sentiment = sentence.sentiment.score
        print(
            f"Sentence {index} has a sentiment score of {sentence_sentiment}"
        )

    print(
        f"Overall Sentiment: score of {score} with magnitude of {magnitude}"
    )
    return 0


def analyze(movie_review_filename):
    """Run a sentiment analysis request on text within a passed filename."""
    client = language_v1.LanguageServiceClient()

    with open(movie_review_filename) as review_file:
        # Instantiates a plain text document.
        content = review_file.read()

    document = language_v1.Document(
        content=content, type_=language_v1.Document.Type.PLAIN_TEXT
    )
    annotations = client.analyze_sentiment(request={"document": document})

    # Print the results
    print_result(annotations)

if _name_ == "__main__":
    parser = argparse.ArgumentParser(
        description=__doc__, formatter_class=argparse.RawDescriptionHelpFormatter
    )
    parser.add_argument(
        "movie_review_filename",
        help="The filename of the movie review you'd like to analyze.",
    )
    args = parser.parse_args()

    analyze(args.movie_review_filename)

EOF
```
```yaml
gsutil cp gs://cloud-samples-tests/natural-language/sentiment-samples.tgz .

gunzip sentiment-samples.tgz
tar -xvf sentiment-samples.tar
python3 sentiment_analysis.py reviews/bladerunner-pos.tx
```
The result will come out with NameError, it is normal





