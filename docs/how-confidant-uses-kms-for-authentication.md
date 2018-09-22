---
layout: post
date: 2018-07-15 20:50:00 ET
title: "How Confidant uses KMS for authentication"
---

# How Confidant uses KMS for authentication

Confidant is an open source product from Lyft that lets you store credentials securely. Your server then makes a request to Confidant via its client to fetch the credentials your code needs.

![The workflow with Confidant](https://cdn-images-1.medium.com/max/4096/1*ktCjE1b08txJL-W7vdwTqg@2x.jpeg)*The workflow with Confidant*

This post explains how the Confidant client generates usernames and tokens for KMS based authentication. They are passed in the request either through [basic auth](https://en.wikipedia.org/wiki/Basic_access_authentication) or as headers (X-Auth-Token and X-Auth-From).

![How the client handles authentication](https://cdn-images-1.medium.com/max/4096/1*__9ZnlICJEkSeXsWAW7M5A@2x.jpeg)*How the client handles authentication*

## Token

The Confidant client uses [KMS encryption](https://lyft.github.io/confidant/advanced/kms_auth/) to generate a token, running the [equivalent](https://github.com/lyft/python-kmsauth/blob/aa2dd957a5d3e58c89fe51a55c6053ff81d9191e/kmsauth/__init__.py#L451) of aws kms encrypt --key-id confidant-key --encryption-context to=confidant-server,from=username,user_type=user --plaintext payload --region us-east-1

1. confidant-key is a KMS key that the Confidant server has access to

1. The from field of the encryption context is what is recorded as having performed the request

1. user_type is typically service, but it’s possible to allow users to make KMS auth based requests (you need to [set an environment variable](https://lyft.github.io/confidant/basics/configuration/#kms-authentication-for-end-users)).

1. The payload looks like {"not_before":timestamp1, "not_after":timestamp2} where the timestamps are in %Y%m%dT%H%M%SZ format

## Username

The username [looks like](https://github.com/lyft/python-kmsauth/blob/aa2dd957a5d3e58c89fe51a55c6053ff81d9191e/kmsauth/__init__.py#L438) 2/user-type/from where the user-type and from have to match the encryption context.

If you’re making requests as a service, you should use the service’s IAM role in the from field as Confidant [generates a grant](https://lyft.github.io/confidant/advanced/kms_auth/#service-to-service-authentication) to allow a service to encrypt / decrypt using its key.

When authenticating as a user, it makes sense to use the AWS username — this can be checked by [adding an IAM policy](https://lyft.github.io/confidant/advanced/kms_auth/#user-to-service-authentication) to the Confidant key to only allow encryption if the username of the person making the AWS request matches what’s in the from field of the encryption context.

Note that if the user is making the AWS request in an assumed role, attempting to match on the username will [cause the request to fail](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html#policy-vars-infotouse). You can use the AWS userid instead, which looks like role-id:caller-specified-role-name. This means you’ll have to pass the username and token in the request’s headers as the colon in the username will cause basic authentication to fail on the server side.
