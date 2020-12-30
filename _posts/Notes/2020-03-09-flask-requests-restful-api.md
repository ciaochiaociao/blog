---
title: flask/requests/RESTful API
---

## get POST json data

Server:

```

from flask import Flask, request
import requests

app = Flask(__name__)

@app.route('/test', methods=['POST'])
def get_event():

    content = request.json
    return content

app.run(host='0.0.0.0', port=5000, debug=True)
```

Client:
```
print('test!!', requests.post('http://localhost:5000/test', json={'test': 'cheers'}))

curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"prop1":"xyz","prop2":"abc"}' \
  http://localhost:5000/test
```