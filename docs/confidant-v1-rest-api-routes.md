
# Confidant v1 REST API Routes

Confidant is an open source credential storage project from Lyft. I’ve written about how clients authenticate with AWS KMS and what a request looks like. This post contains the list of routes supported by the Confidant v1 REST API.

## Services

### /v1/services

*GET:* [Get list of services](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L73)

### /v1/services/\<id\>

* *GET:* [Get service metadata and list of credentials and blind credentials for a service](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L99)
* *PUT:* [Update a service’s credentials, blind_credentials, account and enabled flag](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L251)

## Credentials

### /v1/credentials

* *GET:* [Get list of credentials sorted by name](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L347)
* *POST:* [Creates a credential](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L712)

### /v1/credentials/\<id\>

* *GET:* [Get a credential (includes the secrets)](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L366)
* *PUT:* [Updates a credentials](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L722)

### /v1/credentials/\<id\>/services

*GET:* [Get list of service ids and enabled flag for credential](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L712)

## Blind Credentials

### /v1/blind_credentials

* *GET:* [get list of blind credentials](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L842)
* *POST:* [creates a blind credential](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L978)

### /v1/blind_credentials/\<id\>

* *GET:* [gets a blind credential](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L859)
* *PUT:* [updates a blind credential](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L1074)

### /v1/blind_credentials/\<id\>/services

*GET:* [returns a list of services associated with this blind credential](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L1064)

## Archive

### /v1/archive/services

*GET:* [Get a list of archived services](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L181)

### /v1/archive/services/\<id\>

*GET:* [Get a list of revisions for an archived service](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L143)

### /v1/archive/credentials

*GET:* [Gets a list of archived credentials](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L445)

### /v1/archive/credentials/\<id\>

*GET:* [get a list of revisions for an archived credential](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L408)

### /v1/archive/blind_credentials

*GET:* [get archived blind credentials](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L954)

### /v1/archive/blind_credentials/\<id\>

*GET:* [get a list of revisions for an archived blind credential](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L911)

## Roles

### /v1/roles

*GET:* [Gets the list of IAM roles](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L89)

## Grants

### /v1/grants/\<id\>

* *GET:* [Get grants for a service](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L229)
* *PUT:* [Add encrypt and decrypt grants for the service](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L199)

## Misc

### /v1/login

*GET, POST:* [Send user through login flow](https://github.com/lyft/confidant/blob/fa08086351d731feb45b9792ab62617ca59246bf/confidant/routes/v1.py#L33)

### /v1/user/email

*GET, POST:* [Get the email address of the currently logged-in user.](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L41)

### /v1/client_config

*GET:* [Get configuration to help clients bootstrap themselves](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L54)

### /v1/value_generator

*GET:* [Generate a random string](https://github.com/lyft/confidant/blob/master/confidant/routes/v1.py#L1211)
