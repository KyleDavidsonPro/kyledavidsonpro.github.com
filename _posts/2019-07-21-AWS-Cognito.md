---
layout: post
title: "AWS Cognito"
description: "AWS Cognito"
tagline: "AWS Cognito"
category: cloud
tags: [cloud, digital services]
---

Just over a year ago I evaluated [AWS Cognito](https://aws.amazon.com/cognito/) and it's suitability for a digital service project in Kainos. This is a large solution that aims to become a centralised system for collecting and processing a specific sector of information in the UK. It's a highly active environment with around 3 million events carried out per year. These activities have an estimated economic impact of £4 billion per annum and operate in a highly complex ecosystem between local authorities, utility companies, third party contractors and specialist software vendors. We use AWS Cognito to support these 500+ organisations and their users.

For the uninitiated AWS Cognito is a secure and scalable user directory. It has features like [federated third-party sign-in](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-identity-federation.html) and [cross-device-syncing](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-sync.html) but in the case of our project it was chosen for its standards-driven authentication and authorisation capabilities.  

14 months later, the project is now in private beta and in that time we've learnt quite a few things about Cognito that may be useful to others.

## Early days

To determine Cognito's suitability I authored an [Open Design Proposal](https://medium.com/kainos/you-must-think-first-before-you-move-7ac4af0346a9) in May 2018 which was submitted to our development team. It detailed:
1. How we could use Cognito for authentication
2. How we could use Cognito for authorisation
3. How we might implement Cognito flows (sign-up, sign-in, reset-password)
4. Non functional concerns (security, data-ownership, migration, costs)

The ODP was followed up with a proof-of-concept I had developed using the [Hosted UI](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-app-integration.html) provided by Cognito. 

<img src="{{ site.url }}/assets/cognito-sign-up.png" alt="Sign-up" height="248" width="248">
<img src="{{ site.url }}/assets/cognito-sign-in.png" alt="Sign-up" height="248" width="248">

Although this is quite a barebones interface it still validated the most significant benefits to using Cognito for our project:

1. A plug and play authentication layer with customisable password policy controls we wouldn't have to build ourselves
2. Cognito provided [JSON web tokens](https://tools.ietf.org/html/rfc7519) which we could use in our authorisation layer
3. A well documented SDK available in many languages (in our case Node.js)

The most significant limitations we found at the time were:

1. Custom attributes are not searchable as they cannot be indexed
2. Cognito does not provide an option to export users increasing vendor lock-in
3. The hosted UI did not offer suitable customisation controls for our [GDS compliant web UI](https://design-system.service.gov.uk/)

That said, it was very easy to get started with Cognito and coming off of a project where we had built and managed our own user directories with [OpenDJ](https://backstage.forgerock.com/docs/opendj) as well as maintained our own authentication and authorisation controls, the immediate benefits were clear. 

As a team we decided that it was a good fit, our project was already AWS heavy and we were keen to go with a managed service.

## The Good Stuff

Authentication and authorisation in our project works like this:

1. Users login to our system using an authentication endpoint on our API
2. The API uses the AWS Cognito SDK to authenticate the user
3. If successful, Cognito brings us back ID, Access and Refresh tokens
4. We give the tokens back to the user, which they in turn use for future requests via an [Authorization header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization)

<img src="{{ site.url }}/assets/cognito-authr.png" alt="Sign-up">

Any request to a secure endpoint in our application expects and validates the token provided in this header. [Verifying a JWT](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-verifying-a-jwt.html) in Cognito is well documented and straight-forward. We even found a node package which bundles the steps up for us called [cognito-express](https://www.npmjs.com/package/cognito-express) but we found it gave us less control around logging and health monitoring than we wanted so we wrote our own small service to carry out the validation instead. 

Cognito has given us a well documentated and flexible authentication layer with flows for sign-up, sign-in, sign-out, forgot-password, administrative invites, resets, disabling and more. Furthermore the [group](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-user-groups.html) functions have given us an access control layer that has held up to this day. The JSON web tokens have allowed us to build a secure authorisation layer with virtually no effort.

## Challenges

Depending on the type of solution you're building you may never come across the same challenges we have, but I think they are good to know in any case.

# Amplify JS

[Amplify JS](https://aws-amplify.github.io/) is essentially a wrapper around the AWS SDK giving you an easy to use interface to a bunch of different services, including Cognito. We used this early on in the project and came across massive issues with how this framework uses the browser's localStorage for a large amount of it's Cognito utilities. We caught on pretty early but in my opinion the docs are misleading and imply the framework is suitable for a Node.JS project. It polyfills the window object and server-side `localStorage` was a literal `{}`. This lead to fun behaviours we found when testing our application like one user signing-out signed every user out. [Other people have had problems using Amplify with Node](https://github.com/aws-amplify/amplify-js/issues/876). Eventually we just moved to using the SDK directly and as a result seen significant performance improvements to response times around our Cognito driven endpoints.

If you're building a mobile app or working with a client-side technology then Amplify is a good choice. Ultimately this library just calls onto the actual SDK so if you want to have these functions server-side I would recommend you just use the SDK directly.

# Custom Attributes

Cognito comes with a ton of [standard attributes](https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-settings-attributes.html) you can use, as well as reasonable custom attribute options. Where you run into trouble is if you need to list and search for users using one of these custom attributes. Given that they cannot be indexed, they are not searchable. 

# Exporting Users

Although you can import users into Cognito from a CSV file, there is no option at this point to export users out of Cognito. However depending on your needs you could [list the users](https://docs.aws.amazon.com/cognito-user-identity-pools/latest/APIReference/API_ListUsers.html) out into a CSV file. 

# Rate limits & Errors

Cognito defines a set of [soft & hard limits](https://docs.aws.amazon.com/cognito/latest/developerguide/limits.html) but when we exceeded one of the API limits we couldn't really get any useful information about what rate we hit. Cognito raises a generic 'Rate exceeded' error and we were told by AWS support that there are no metrics for Cognito and throttling available to keep a track of API calls. 
 

## Summary

Our project has been using Cognito for over a year now. Where it has really shined for us is it's authentication and access controls but it has also allowed us to build administrative and standard user journeys at a much lower cost than if we had built our own user services. I still think there could be a few improvements made around querying users, the group features are great for RBAC but something simple to let me group and query users by a department or organisation would go a long way for the type of enterprise projects I've worked on.

I would absolutely recommend Cognito for a new project. 