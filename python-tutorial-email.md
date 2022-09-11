---
title: Python - Send Email
parent: Python SDK
grand_parent: SDKs
---

# Sending an email on Forecasts

On completion of this tutorial we will have a python app that:

1. Streams forecasts from Anko Investor
1. Validates these forecasts against some basic rules
1. Sends these validated forecasts, via email, to some address

## Requirements

1. Python 3.10
1. [Pipenv](https://pipenv.pypa.io/en/latest/)
1. An [Anko Investor account, organisation, and token](/getting-started)
1. An email address; this tutorial works specifically with a gmail account but includes genericised instructions for a normal SMTP account


## Setup

First, create a new python project, install `anko-sdk` into it, and enable the pipenv virtualenv:

```bash
$ pipenv install anko-sdk protobuf==3.20
Creating a virtualenv for this project...
Pipfile: /home/me/projects/anko-emailer/Pipfile
Using /usr/bin/python (3.10.7) to create virtualenv...

# SNIP

To activate this project's virtualenv, run pipenv shell.
Alternatively, run a command inside the virtualenv with pipenv run.
$ pipenv shell
```

Now's a good opportunity to store your Anko Investor token in your environment, too.

```bash
$ export ANKO_TOKEN=anko-foo
```

Where `anko-foo` is the value of the token you created when running through the requirements above.

## Development Time

Create and open the file `main.py` in your favourite editor or IDE, and add the following:

```python
import os
import socket

from anko import Client
from typing import *

class Emailer:
    def __init__(self, token: str, _id: str) -> None:
        self.client = Client(token, _id)


if __name__ == '__main__':
    emailer = Emailer(os.environ.get('ANKO_TOKEN'), socket.gethostname())

```

This basic code creates a new `Emailer` class, and instantiates a new `anko.Client` in it.

Running this code should, hopefully, run without error, doing nothing.


### Validating forecasts

In our example, we're (somewhat arbitrarily) only interested in:

1. Forecasts where the derived subject ratio is over 50% (which is to say, [in a piece of intelligence, the derived subject makes up over half the companies mentioned](/protobuf))
1. Forecasts where the derived subject score is over 25 (a higher score reduces the chances of a false positive in subject detection, though too high could lean to false negatives)
1. Forecasts where the market is the [LSE](https://www.londonstockexchange.com/)

Thus we make the change:

```diff
diff --git a/main.py b/main.py
index 4032059..032c179 100644
--- a/main.py
+++ b/main.py
@@ -8,6 +8,10 @@ class Emailer:
     def __init__(self, token: str, _id: str) -> None:
         self.client = Client(token, _id)

+    def __is_valid(self, forecast: Type) -> bool:
+        return forecast.symbol.ratio >= 0.5 and \
+            forecast.symbol.score >= 25 and \
+            forecast.symbol.exchange == 'London Stock Exchange'

 if __name__ == '__main__':
     emailer = Emailer(os.environ.get('ANKO_TOKEN'), socket.gethostname())
```

Which allows us to test the above conditions.


### Sending emails

In our example we're going to send email via gmail, but realistically the only change necessary for using a different service is to change the SMTP address used below.

You'll need to [generate an app specific password](https://support.google.com/accounts/answer/185833), and then make the following change:

```diff
diff --git a/main.py b/main.py
index 032c179..c32dc9b 100644
--- a/main.py
+++ b/main.py
@@ -2,16 +2,40 @@ import os
 import socket

 from anko import Client
+import smtplib
 from typing import *

+SUBJECT = 'Brand new forecast!'
+
 class Emailer:
-    def __init__(self, token: str, _id: str) -> None:
+    def __init__(self, token: str, _id: str, email_addr: str, email_password: str) -> None:
         self.client = Client(token, _id)

+        self.email_addr = email_addr
+        self.smtp_connection = smtplib.SMTP_SSL('smtp.gmail.com', 465)
+        self.smtp_connection.ehlo()
+        self.smtp_connection.login(email_addr, email_password)
+
+
     def __is_valid(self, forecast: Type) -> bool:
         return forecast.symbol.ratio >= 0.5 and \
             forecast.symbol.score >= 25 and \
             forecast.symbol.exchange == 'London Stock Exchange'

+    def __send_forecast(self, forecast: Type) -> None:
+        body = f'''From: {self.email_addr}
+To: {self.email_addr}
+Subject: {SUBJECT}
+
+{forecast.symbol.symbol} on exchange {forecast.symbol.exchange} (RIC: {forecast.ric}) is forecast a price change of {forecast.label} with a confidence of {int(forecast.score*100)}%
+
+
+'''
+
+        self.smtp_connection.sendmail(self.email_addr, self.email_addr, body)
+
 if __name__ == '__main__':
-    emailer = Emailer(os.environ.get('ANKO_TOKEN'), socket.gethostname())
+    emailer = Emailer(os.environ.get('ANKO_TOKEN'),
+                      socket.gethostname(),
+                      os.environ.get('EMAIL_ADDRESS'),
+                      os.environ.get('EMAIL_PASSWORD'))
```

At this point you'll need to export your email address and password, before running

```bash
$ export EMAIL_ADDRESS=me@example.com
$ export EMAIL_PASSWORD=super-secret
$ python main.py
```

### Finishing

At this point we're not actually doing anything, certainly not with forecasts at least. We need to iterate over `self.client` as per:

```diff
diff --git a/main.py b/main.py
index c32dc9b..18fde88 100644
--- a/main.py
+++ b/main.py
@@ -17,6 +17,15 @@ class Emailer:
         self.smtp_connection.login(email_addr, email_password)


+    def run(self):
+        for forecast in self.client:
+            if forecast == None:
+                continue
+
+            if self.__is_valid(forecast):
+                __send_forecast(forecast)
+
+
     def __is_valid(self, forecast: Type) -> bool:
         return forecast.symbol.ratio >= 0.5 and \
             forecast.symbol.score >= 25 and \
@@ -39,3 +48,5 @@ if __name__ == '__main__':
                       socket.gethostname(),
                       os.environ.get('EMAIL_ADDRESS'),
                       os.environ.get('EMAIL_PASSWORD'))
+
+    emailer.run()
```


## Summing up

At this point, by starting our app via `python main.py` and letting it run, we should start receiving forecasts via email!
