# Covey.Town (feat. Spotify)

Demo: https://friendly-nightingale-cac19d.netlify.app

Covey.Town provides a virtual meeting space where different groups of people can have simultaneous video calls, allowing participants to drift between different conversations, just like in real life.
Covey.Town was built for Northeastern's [Spring 2021 software engineering course](https://neu-se.github.io/CS4530-CS5500-Spring-2021/), and is designed to be reused across semesters.
You can view our reference deployment of the app at [app.covey.town](https://app.covey.town/) - this is the version that students built on, and our [project showcase](https://neu-se.github.io/CS4530-CS5500-Spring-2021/project-showcase) highlights select projects from Spring 2021.

![Covey.Town Architecture](docs/covey-town-architecture.png)

The figure above depicts the high-level architecture of Covey.Town.
The frontend client (in the `frontend` directory of this repository) uses the [PhaserJS Game Library](https://phaser.io) to create a 2D game interface, using tilemaps and sprites.
The frontend implements video chat using the [Twilio Programmable Video](https://www.twilio.com/docs/video) API, and that aspect of the interface relies heavily on [Twilio's React Starter App](https://github.com/twilio/twilio-video-app-react). Twilio's React Starter App is packaged and reused under the Apache License, 2.0.

A backend service (in the `services/townService` directory) implements the application logic: tracking which "towns" are available to be joined, and the state of each of those towns.

The manner in which we have connected Covey.Town to Spotify is described below.
In the login page, a registered user may click on an embedded link to a Spotify login page and login. Upon successful login, an authorization token is generated and the user is redirected to Covey.Town where the token is scraped from the URL. Once the player goes about normally and joins a town, this code is stored with that user's backend player object ('services/townService/src/types/Player.ts').
Using the access token, the player makes calls to the Spotify API and populates the necessary fields for our feature.

The frontend player (in 'frontend/src/classes/Player.ts') contains a spotifyData type to package those fields for rendering in the WorldMap and SocialSidebar.

## Running this app via Netlify

We have set up Netlify deploys for our repo. Thus, a Netlify page can be used for remote access to the Covey Town app. In order for the page to work correctly with the Spotify API, we must add the Netlify URL to the list of trusted URLs that the Spotify API can redirect to. To request that a new Netlify deploy URL be added to this list, contact the owner of the Spotify Web app (Pavan Hirpara)

## Running this app locally

Running the application locally entails running both the backend service and a frontend.

### Getting access to the Spotify API

In order to make API calls after logging into your Spotify user, the Spotify Web API requires you to register your account with the Spotify X Covey Town App. To do this, send the owner of the Web API app (Pavan Hirpara) the email associated with your Spotify account. Then, access to the API can be granted and you can log into the Covey Town app with your Spotify user.

### Setting up the backend

To run the backend, you will need a Twilio account. Twilio provides new accounts with $15 of credit, which is more than enough to get started.
To create an account and configure your local environment:

1. Go to [Twilio](https://www.twilio.com/) and create an account. You do not need to provide a credit card to create a trial account.
2. Create an API key and secret (select "API Keys" on the left under "Settings")
3. Create a `.env` file in the `services/townService` directory, setting the values as follows:

| Config Value            | Description                                                  |
| ----------------------- | ------------------------------------------------------------ |
| `TWILIO_ACCOUNT_SID`    | Visible on your twilio account dashboard.                    |
| `TWILIO_API_KEY_SID`    | The SID of the new API key you created.                      |
| `TWILIO_API_KEY_SECRET` | The secret for the API key you created.                      |
| `TWILIO_API_AUTH_TOKEN` | Visible on your twilio account dashboard.                    |

### Starting the backend

Once your backend is configured, you can start it by running `npm start` in the `services/townService` directory (the first time you run it, you will also need to run `npm install`).
The backend will automatically restart if you change any of the files in the `services/townService/src` directory.

### Configuring the frontend

Create a `.env` file in the `frontend` directory, with the line: `REACT_APP_TOWNS_SERVICE_URL=http://localhost:8081` (if you deploy the towns service to another location, put that location here instead)

You will also need to add the line: `SPOTIFY_CLIENT_SECRET=<secret>`. `The SPOTIFY_CLIENT_SECRET` is a unique secret to our Spotify Web API app and must be included to gain access to the Spotify API. You can request access to the Spotify secret from the owner of the Web API (Pavan Hirpara hirpara.p@northeastern.edu).

### Running the frontend

In the `frontend` directory, run `npm start` (again, you'll need to run `npm install` the very first time). After several moments (or minutes, depending on the speed of your machine), a browser will open with the frontend running locally.
The frontend will automatically re-compile and reload in your browser if you change any files in the `frontend/src` directory.
