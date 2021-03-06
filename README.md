# @dekproject/mongoose

Mongoose interface plugin for DEK

What does this plugin do?

* Control configuration for connection to Mongoose in production development mode in a simplified way with **dotenv**
* Performs connection implementation along the lines ES6 being pre requirement to start the project

## Instalation

To install the bootstrap we recommend using the CLI

```bash
$ yarn add @dekproject/cli --save
```

In the .env file add the following settings

```
MONGO_USER=
MONGO_PASSWORD=
MONGO_HOST=localhost
MONGO_PORT=27017
MONGO_DB=dek
```

For applications with cluster usage in MongoDB

```
MONGO_USER=
MONGO_PASSWORD=
MONGO_PATH=mongodb://mongo-01:27017,mongo-02:27017/dek?replicaSet=dek&readPreference=secondaryPreferred
MONGO_DB=dek
```

## Usage

Using direct

```bash
$ npm i @dekproject/scope @dekproject/routes
```

Using in the standard DEK skeleton

```js
import { $, app } from "@dekproject/scope";
import routes from "@dekproject/routes";

$.wait("mongoose").then(() => {
    $.app.use(await routes(process.env.ROUTES_PATH || "src/routes"));

    $.app.listen(PORT, () => {
        console.log(`App listening on port ${PORT}!`);
    });
});
```

### Model

```js
import { mongoose } from "@dekproject/scope";

const Schema = mongoose.Schema;

var UserSchema = new Schema({
    email: {
        type: String, required: true,
        trim: true, unique: true,
        match: /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/
    },
    firstName: {type: String},
    lastName: {type: String}
});

mongoose.model('User', UserSchema);
```

### Controller

```js
import { mongoose } from "@dekproject/scope";
import "../model/users";

var User = mongoose.model("User");

export let createUser = (req, res) => {
    var user = new User(req.body);

    user.save(function (err) {
        if (err) res.status(500).send(err.message).end();
        else res.send(user).end();
    });
}

export let getAllUsers = (req, res) => {
    User.find(function (err, users) {
        if (err) res.status(500).send(err.message).end();
        else res.send(users).end();
    });
}
```

### Route
```js
import { createUser, getAllUsers } from "../controllers/users";

export default async (router) => {
    router.route('/users').post(createUser).get(getAllUsers);
}
```
