twilio-taskrouter.js
===============

TaskRouter is Twilio's skills based routing system. With this library, you can manage your Workers in the browser or view the state of your Workspace.

Note that this is a preview release. You may encounter bugs and instability, and
the APIs available in this release may change in subsequent releases.

**We want your feedback!** Email Ben Getson Product Manager for TaskRouter
at [bgetson@twilio.com](mailto:bgetson@twilio.com) with suggested
improvements, feature requests and general feedback. If you need technical
support, contact [help@twilio.com](mailto:help@twilio.com).

Installation
------------

### NPM

```
npm install twilio-taskrouter
```

Usage
-----

The following is a simple example showing a Worker waiting for Reservations.
For more information, refer to the
[API Docs](//media.twiliocdn.com/taskrouter/js/v2/docs).

```js
const TaskRouter = require('twilio-taskrouter');
const Twilio = require('twilio');
const AccessToken = Twilio.jwt.AccessToken;
const TaskRouterGrant = AccessToken.TaskRouterGrant;

const accountSid = '';
const signingKeySid = '';
const signingKeySecret = '';
const workspaceSid = '';
const workerSid = '';

const token = createAccessToken(accountSid, signingKeySid, signingKeySecret, workspaceSid, workerSid);
const alice = new TaskRouter.Worker(token);

alice.on('ready', readyAlice => {
    console.log(`Worker ${readyAlice.sid} is now ready for work`);
});

alice.on('reservationCreated', reservation => {
    console.log(`Reservation ${reservation.sid} has been created for ${alice.sid}`);
    console.log(`Task attributes are: ${reservation.task.attributes}`);

    reservation.on('accepted', acceptedReservation => {
      console.log(`Reservation ${acceptedReservation.sid} was accepted.`);
    });

    reservation.accept().then(acceptedReservation => {
      console.log(`Reservation status is ${acceptedReservation.status}`);
    }).catch((err) => {
      console.log(`Error: ${err}`);
    });
});

function createAccessToken(accountSid, signingKeySid, signingKeySecret, workspaceSid, workerSid) {
    const taskRouterGrant = new TaskRouterGrant({
        workerSid: workerSid,
        workspaceSid: workspaceSid,
        role: 'worker'
    });

    const accessToken = new AccessToken(accountSid, signingKeySid, signingKeySecret);
    accessToken.addGrant(taskRouterGrant);
    accessToken.identity = 'alice';

    return accessToken.toJwt();
}

```

Changelog
---------

See [CHANGELOG.md](https://github.com/twilio/twilio-taskrouter.js/blob/master/CHANGELOG.md).

License
-------

See [LICENSE.md](https://github.com/twilio/twilio-taskrouter.js/blob/master/LICENSE.md).

Building
--------

Fork and clone the repository. Then, install dependencies with

```
yarn install
```

Then run the `build` script:

```
yarn build
```

The builds and docs will be placed in the `dist/` directory.

Contributing
------------

Bug fixes welcome! If you're not familiar with the GitHub pull
request/contribution process, [this is a nice tutorial](https://gun.io/blog/how-to-github-fork-branch-and-pull-request/).
