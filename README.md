# patient-recommender

An api that provides a client list recommendation for a given position.

# How to run

## Prerequisites

- Node (v14.8.0 or higher)
- Yarn (v1.22.4 or higher)

  Or just **Docker**!

## Running

- First, install the dependencies by running

  `yarn install`

- Then start the application by running

  `yarn start`

  This will start the application on the default port (8000).

  To specify a given port, run

  `SERVER_PORT=<port> yarn start`

  For example, if desired to run the http server at port 8080, then we should run

  `SERVER_PORT=8080 yarn start`

- And to run tests,

  `yarn test`

## Running using Docker

- First, build the image

  `docker build -t lucaskc/patient-recommender .`

- Then, run using

  `docker run -p 8000:8000 lucaskc/patient-recommender`

- Or use **docker-compose** to run tests **and** start the application

  - Running:

    `docker-compose up`

  - Rebuilding dockerfile image and running:

    `docker-compose up --build`

  - Stopping docker-compose:

    `docker-compose down`

# API

Application base URL: /api/v1

## Retrieve list of recommended patients

- **URL**

  /patient/queue

- **Method**

  `GET`

- **Query Params**

  **Required:**

  `latitude=[number]`

  `longitude=[number]`

- **Success Response:**

  **Status Code:** 200 (OK)

  **Body content:** json list of recommended patients.

- **Error Response:**

  **Status Code:** 422 (Unprocessable Entity)

  **Body content:** `{ error: "Cannot process empty values" }`

  OR

  **Status Code:** 500 (Internal Server Error)

  **Body content:** `{ error: "Internal error, please try again later" }`

- **Request example:**

  `curl --location --request GET 'http://localhost:8000/api/v1/patient/queue?latitude=37.791050&longitude=-122.401932'`

  Will return 10 patients that will most likely accept appointment offers for a facility located at 37.791050 latitude and -122.401932 longitude.

# Implementation decisions

- For the recommendation, the first 7 patients with the highest score (behavior score + demographic score) are chosen. The 3 other ones are picked randomly within a slice of the left patients with the highest demographic score (instead of just choosing the ones with smallest behavior scores). By taking this decision, I'm focusing on recommending the more promising ones disconsidering behavior scores. This allows promising candidates, even with low behavior scores, to be selected.

- Added the dependency **rimraf**. If you want to import this project, you should add `"postinstall": "rimraf src/server app.js"` within "scripts" in the `package.json` file before running `yarn install`. This will remove unnecessary files after the installation.
