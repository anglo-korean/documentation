# Anko Investor

Welcome to the Anko Investor documentation project. This project includes SDK guides, getting started guides, and a run-down of Anko Investor's features.

Requests for improved docs can be made by [opening an issue](https://github.com/anglo-korean/documentation/issues/new/choose). Pull requests are similarly welcomed.

## Security

### Login Security

Anko Investor uses [Auth0](https://auth0.com) to handle signup and logins. We receive, and store, from Auth0:

* Email adresses- this allows us to handle things like allowing users to login via many services, and to allow for inviting users to organisations
* Nickname - this is used for reporting user operations in the Notifications panel in the webapp

At no point do we store passwords, whether in a hashed, salted+hashed, plaintext, or any other form.

We enforce MFA on all logins via Auth0.

## Getting Started

The simplest way to start consuming forecasts is with [grpcurl](https://github.com/fullstorydev/grpcurl). Install this, and then continue below.

### Creating an account

Visit [https://anko-investor.com/](https://anko-investor.com/) and click "Get Started".

This will take you to Auth0, where you may either login or signup. If you have a current Auth0 session you may even be automatically signed in.

From here you will be redirected to the Dashboard.

### Dashboard

The Dashboard contains three pieces of information. Your *Organisation* membership, News and Updates from the Anko Investor team, and a set of events and notifications for audit.

For now we will concentrate on *Organisations*.

Click the + button next to *My Organisations* and fill out the *Create Organisation* form. The billing email may be used to provide account icons via [Gravatar](https://en.gravatar.com/).

On creation of a new *Organisation*, a link will appear on the page to the *Organisation Page*

### Organisation page

The *Organisation Page* is used to invite members, update your organisation, and view organisation specific events.

For now we will use the other feature; creating *Tokens*.

Anko Investor uses token-based authentication to ensure you get access to the Forecasts you're entitled to, and are expecting.

Click the + next to *Tokens* and fill out the form. *Minimum Score* is described below.

On Creation, your token will appear. This is the last time your token will appear, so make a note of it!

#### On Minimum Score

Every forecast has a *Confidence Score* between 0 and 1. A *Confidence Score* of 0 means that the forecast is a total guess. A *Confidence Score* of 1 means that this forecast is, in effect, a prophecy. In reality, *Confidence Scores* tend to range between 0.3 and 0.99.

By setting the minimum score you're interested in at the token level, you avoid dealing with forecasts you may find to be of low quality.

Each plan has a minimum score, too; if you select a minimum score below the plan level then your choice is ignored. For the hobby plan, the minimum is 0.75, which discards about 30% of forecasts.


### Streaming Forecasts

Given the token `anko-abc123`, the following command will stream forecasts.

```bash
$ grpcurl -H 'authorization: bearer anko-abc123' forecasts.anko-investor.com:443 Forecasts/Stream
```

The first forecast you will receive is a dummy forecast which signifies that the connection has been created correctly.

The gRPC service at `forecasts.anko-investor.com:443` supports [reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and, so, exploring the response format is reasonably simple.

Otherwise, the `.proto` file which this service is generated at can be found [here](proto/gateway.proto).

This file can also be used to generate client code.
