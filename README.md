# Example Express.js Application

This repo holds an example Express.js application that uses FusionAuth as the identity provider. 
This application will use an OAuth Authorization Code Grant workflow to log a user in and 
get them access and refresh tokens.


This application was built by following the [Express.js Quickstart](https://fusionauth.io/docs/quickstarts/quickstart-javascript-express-web/).

## Project Contents

The `docker-compose.yml` file and the `kickstart` directory are used to start and configure a local FusionAuth server.

The `/complete-application` directory contains a fully working version of the application.

## Project Dependencies
* Docker, for running FusionAuth
* Node 16 or later, for running the Changebank Express.js application

## FusionAuth Installation via Docker

In the root of this project directory (next to this README) are two files [a Docker compose file](./docker-compose.yml) and an [environment variables configuration file](./.env). Assuming you have Docker installed on your machine, you can stand up FusionAuth up on your machine with:

```
docker compose up -d
```

The FusionAuth configuration files also make use of a unique feature of FusionAuth, called [Kickstart](https://fusionauth.io/docs/v1/tech/installation-guide/kickstart): when FusionAuth comes up for the first time, it will look at the [Kickstart file](./kickstart/kickstart.json) and mimic API calls to configure FusionAuth for use when it is first run. 

> **NOTE**: If you ever want to reset the FusionAuth system, delete the volumes created by docker compose by executing `docker compose down -v`. 

FusionAuth will be initially configured with these settings:

* Your client Id is: `e9fdb985-9173-4e01-9d73-ac2d60d1dc8e`
* Your client secret is: `super-secret-secret-that-should-be-regenerated-for-production`
* Your example username is `richard@example.com` and your password is `password`.
* Your admin username is `admin@example.com` and your password is `password`.
* Your fusionAuthBaseUrl is 'http://localhost:9011/'

You can log into the [FusionAuth admin UI](http://localhost:9011/admin) and look around if you want, but with Docker/Kickstart you don't need to.

## Running the Example App
To run the application, first go into the project directory

```shell
cd complete-application
```

Install dependencies

```shell
npm install
```

Start the application

```shell
npm run dev
```

## Device Limiting Brief

Write a guide on how to limit the number of devices that can be logged into a system

FusionAuth does not directly support this use case, but you should be able to create your own solution using FusionAuth's APIs.

Here are some features of FusionAuth that may be helpful for implementing this functionality:

* Retrieve a user's session information via the [JWT Retrieve Refresh Tokens API](https://fusionauth.io/docs/v1/tech/apis/jwt#retrieve-refresh-tokens)
* Use the [JWT Revoke Refresh Tokens API](https://fusionauth.io/docs/v1/tech/apis/jwt#revoke-refresh-tokens) to revoke refresh tokens by user, application, Id, or value
* Use the [`user.login.success`](https://fusionauth.io/docs/v1/tech/events-webhooks/events/user-login-success) webhook to trigger a session count check on your side and fail the login if they have too many active sessions

There are two ways to do this, please cover both.

*You can use the webhook to prevent creation of new sessions, with a custom error message on the login screen. This is quick and secure, but not an optimal user experience (because the webhook error message isn't rich and there may be more than one webhook failing login, causing confusion over what caused the failure).

* Modify your application so that after the user logs in, you use the refresh tokens above to see how many devices are logged in. There you can present more options: "you are logged in on 3 devices and are only allowed 2. Please select which device to log out of". When a device is selected, then the refresh token would be revoked for that device.
This also implies a relatively short JWT lifetime (5 min?) so please configure that.

FusionAuth only captures limited information about each device as part of the session information. Devices are not tracked separately, and a user may have multiple sessions on the same device (e.g. on different browsers or in a private browsing mode). So a device is really anything where a user logs in (chrome and firefox on the same computer are two 'devices').

Mention a couple of open GitHub issues requesting a similar feature:

Limit a user to a single session fusionauth-issues#1363
Limit the number of different devices an account can login from.  fusionauth-issues#1156

## Webhooks

Customize message : https://github.com/FusionAuth/fusionauth-issues/issues/757


## Scratch

kickstater: 
 "defaultMessages": "@{messages/messages.txt}"

### Log in json to webhooks:

```json
 {
  event: {
    applicationId: 'e9fdb985-9173-4e01-9d73-ac2d60d1dc8e',
    authenticationType: 'PASSWORD',
    connectorId: 'e3306678-a53a-4964-9040-1c96f36dda72',
    createInstant: 1703070851536,
    id: 'f20ab55d-7a24-4e83-a7f5-7028abd6e85c',
    info: {
      deviceName: 'macOS Safari',
      deviceType: 'BROWSER',
      ipAddress: '192.168.16.1',
      userAgent: 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/16.3 Safari/605.1.15'
    },
    ipAddress: '192.168.16.1',
    tenantId: 'd7d09513-a3f5-401c-9685-34ab6c552453',
    type: 'user.login.success',
    user: {
      active: true,
      birthDate: '1985-11-23',
      connectorId: 'e3306678-a53a-4964-9040-1c96f36dda72',
      data: {},
      email: 'richard@example.com',
      firstName: 'Fred',
      id: '00000000-0000-0000-0000-111111111111',
      insertInstant: 1702993741565,
      lastLoginInstant: 1703070851535,
      lastName: 'Flintstone',
      lastUpdateInstant: 1702993741565,
      memberships: [],
      passwordChangeRequired: false,
      passwordLastUpdateInstant: 1702993741673,
      preferredLanguages: [],
      registrations: [Array],
      tenantId: 'd7d09513-a3f5-401c-9685-34ab6c552453',
      twoFactor: [Object],
      usernameStatus: 'ACTIVE',
      verified: true,
      verifiedInstant: 1702993741565
    }
  }
}
```


## Notes for FusionAuth / Ritza team

- Need to update `@node/types` in the package.json file to allow for fetch
- Rename theme in Kickstart files to 'ChangeBank Theme' instead of 'React Theme'
- Express example uses the fusionauth typescript client for tokens etc. Shouldn't it rather use native Oauth clients like the other quickstarts?
