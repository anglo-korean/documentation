---
title: Forecasts
has_children: true
nav_order: 3
---

# Forecasts

A forecast contains:

* An ID, which can be used to avoid re-processing foreacasts
* A [RIC](https://en.wikipedia.org/wiki/Reuters_Instrument_Code)
* A [confidence score](/minimum-score)
* A more full `symbol`, for platforms which don't read RICs
* A label, representing the forecast price change

## Dummy Forecast

When connecting to the forecasts stream for the first time, the server emits a dummy forecast to show that the connection has been correctly initiated.

```json
{
  "id": "dummy-forecast",            // The ID of a forecast (non-unique for dummy forecatss)
  "ric": "DUMMY",                    // A Reuters Instrument Code
  "score": 0,                        // A score of 0 means this forecast is untrustworthy. The range is 0..1
  "symbol": {                        // Symbol is the raw, internal, representation of a symbol
    "symbol": "DUMMY",               // Market symbol
    "score": 100,                    // See https://docs.anko-investor.com/protobuf
    "ratio": 1,                      // A ratio of 1 means DUMMY is the only symbol derived from some intel
    "exchange": "Anglo Korean"       // The exchange this symbol is listed on
  },
  "label": "unknown"                 // See https://docs.anko-investor.com/protobuf
}
```
