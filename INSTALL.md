# Running Trustroots locally

_These instructions are for installing locally. If you'd like to have containerised setup, see [INSTALL-DOCKER.md](INSTALL-DOCKER.md) instead._


## Prerequisites

Make sure you have installed all these prerequisites:
* Unix operating system, like Linux or MacOS. If you use Windows, please look into [installing via Docker](INSTALL-DOCKER.md) instead.
* [Git](https://git-scm.com/) (`git --version`, preinstalled on MacOS)
* [Node.js](https://nodejs.org/en/download/) version 8 or 10 and the NPM v5+ (`node --version && npm --version`). We recommend managing Node.js versions using [NVM](https://github.com/creationix/nvm).
* [MongoDB](http://www.mongodb.org/downloads) v3.6 - 4.0. (`mongod --version`).
* Some of the NPM modules require compiling native code, which might require installing X-Code's [Command line tools](https://railsapps.github.io/xcode-command-line-tools.html) on MacOS or `build-essential` and `make` on Linux. On MacOS you can install or confirm they're installed by running `xcode-select --install`
* [GraphicsMagick](http://www.graphicsmagick.org/). In MacOS, you can simply use [Homebrew](http://mxcl.github.io/homebrew/) to install it:
    ```bash
    brew install graphicsmagick
    ```

## Installing

### 1. Clone the repository:

```bash
git clone https://github.com/Trustroots/trustroots.git
cd trustroots
```

### 2. Make sure MongoDB is running on the default port (27017):

```bash
mongod
```

Optional: If you need to modify connection settings, see `config/env/local.js` config file.

### 3. Start the app:
```bash
npm start
```

🎉 Open [localhost:3000](http://localhost:3000) in your browser.

#### Good to know

- Run the app by typing `npm start`.
- Stop the app by hitting `Ctrl+C`.
- When you change any file, they get recompiled and the browser is refreshed.
- Keep an eye on the console in case of compiling errors.
- [Read more](https://github.com/Trustroots/trustroots/wiki/Development).
- You can start clean by running `npm run distclean && npm run dropdb`.

## Modifying configurations

Add any configurations you want to keep out of version control to `config/env/local.js` file. It's created for you on the first start and overrides anything in `config/env/local.js`.


## Running services

### MailDev

MailDev is there for viewing and testing emails during development

[MailDev](https://github.com/djfarrelly/MailDev) will be running at [localhost:1080](http://localhost:1080)

### Agendash

Agendash is a dashboard & inspector for [Agenda](https://github.com/agenda/agenda), our job scheduling library.

[Agendash](https://github.com/joeframbach/agendash) (background job dashboard) at [localhost:1081](http://localhost:1081).

## Debugging

The standard node inspector runs on each start for the main app (port 5858) and the worker (port 5859).

To debug using Chrome:

1. Run 'npm start'
2. Open `chrome://inspect/#devices`. Note the "Remote Target" list should be empty to start
3. Press "Open dedicated DevTools for Node"
4. Press "Add connection" and add both `localhost:5858` and `localhost:5859`
5. They will now appear in "Remote Target" list
6. Press 'inspect' on whichever process you want to debug
7. You should now have console/profiler etc available.

More information can be found in the NodeJS [debug documentation](https://nodejs.org/en/docs/guides/debugging-getting-started/).

## Running tests

- `npm test` (both client & server)
- `npm run test:client`
- `npm run test:client:watch` (run + watch for changes)
- `npm run test:server`
- `npm run test:server:watch` (run + watch for changes)

To lint files, run `npm run lint`

## Mock data

There is a data script that the individual mock data scripts with default values to generate mock user data, hosting offers, messages and tribes. It's highly recommended you run this script after installation, that way you'll have something to look at.

Run `npm run seed` - This will add automatically add:

* 100 tribes
* 1000 users including 3 users with user names of `admin1`, `admin2`, `admin3` each with the password of `password123`
* 2000 message threads with 1 to 10 messages in each thread

For more custom setups, you can alternatively run the scripts for generating data individually. It is currently recommended that you run them in the sequence provided below.

1. To add tribes, run `npm run seed:tribes 50` — This will create 50 tribes.
    * Run this prior to adding users to add users to tribes automatically

2. To add users, run `npm run seed:users 1000 adminusername` — This will create 1000 users and hosting offers. `adminusername` is optional (a-z0-9) and will create an admin user.
    * It can take up to 5 minutes. Mongoose might complain about duplicates — just ignore these errors.
    * To see the result, log in with your chosen username and password `password123`.
    * Additional admin usernames are also supported (eg. `npm run seed:users 1000 admin1 admin2 admin3`)
    * If tribes exist, users will also be added to random tribes

3. To add messages, run `npm run seed:messages 1000 10` — This will create 1000 message threads between random users with up to 10 messages in each thread.

## Clean database

To drop your database, run:
```bash
npm run dropdb
```

## Enable FCM push notifications (optional)

1. Create [FCM account](https://firebase.google.com/)

2. Go to [FCM console](https://console.firebase.google.com/) and create a new project

3. Open the project and hit the small gear next to "Overview" at the sidebar so that you get to "project settings" page

4. Choose "Cloud messaging" tab, copy "Sender ID" number

5. Choose "Service accounts" tab

6. Either "create new service account" via "Manage all service accounts" link or choose existing one from the list (for development, "Firebase Admin SDK" account is fine)

7. Click on the "Generate new private key" button

8. Choose "json" format and you'll get a file to download

9. Add contents from that file to your `./config/env/local.js`:

    ```js
    fcm: {
      senderId: 'PASTE_YOUR_SENDER_ID_NUMBER_HERE',
      serviceAccount: PASTE_YOUR_JSON_CONFIG_HERE
    },
    ```

10. To stop eslint from complaining, you might need to convert double quotes to single quotes. (`"` → `'`)


## Enable collecting statistics to InfluxDB (optional)

1. [Install InfluxDB](https://docs.influxdata.com/influxdb/latest/introduction/installation/) v1.0+ and run it (type `influxd`)

2. Add InfluxDB configuration to your `./config/env/local.js`:

    ```js
    influxdb: {
      enabled: true,
      options: {
        host: 'localhost',
        port: 8086, // default 8086
        protocol: 'http', // default 'http'
        // username: '',
        // password: '',
        database: 'trustroots'
      }
    }
    ```

3. You can observe data through InfluxDB admin panel: [localhost:8083](http://localhost:8083/) or optionally [install Grafana](http://docs.grafana.org/installation/) and connect it to InfluxDB.

4. [Read more](INFLUXDB.md) about the collected data and metrics

## Use ImageMagick instead of GraphicsMagick

If you prefer [ImageMagick](http://www.imagemagick.org/) over [GraphicsMagick](http://www.graphicsmagick.org/):

1) In MacOS, you can simply use [Homebrew](http://mxcl.github.io/homebrew/) to install it:
    ```bash
    brew install imagemagick
    ```

2) Change `imageProcessor` setting from `./configs/env/local.js` to `imagemagic`.

## Updating

Run these to get most recent version:
```bash
$ git pull            # Get the latest code for the current branch
$ npm update          # Update NPM
$ npm run migrate     # Migrate database up
```

...or simply `./scripts/update.sh` which does this all for you.



## Support

- Check [troubleshooting](https://github.com/Trustroots/trustroots/wiki/Troubleshooting).
- Check and open issues at [GitHub](https://github.com/Trustroots/trustroots/issues)
- [Contact us](https://www.trustroots.org/contact)
