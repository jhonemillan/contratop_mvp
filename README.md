# Brief description
This project is built on Node.js, Express, Typescript, Sequelize, Postgresql, and Jest mainly.

# Basic Requirements
- [x] Install PostgreSQL
- [x] Install Node.js (See comments)
- [x] Install some DB Manager (Normally we use DBeaver)
- [x] Be sure you have word or some text proccessor to read and open doc/docx files
- [x] Install python

# How to setup project

1.  You need to clone the project using **git clone https://github.com/MiloCreditPlatform/backend.git**

2.  Execute in command line, npm install -g node-gyp 

3.  Set python variable for node-gyp with the command npm config set python 'route to python'
    Please visit the next web [node-gyp](https://github.com/nodejs/node-gyp) and select proccess depending of your Operative System

4.  Be Sure about the editor configuration you are using, because is important to maintain the same Lint rules.
    We recommend to use VS Code.

5.  Now, you can execute npm i to download required libraries in the project. Remember until now we just install node-gyp.
    this has be done apart, in order that require a very specific process to install it and configure it.

6.  After install libraries, is time to create DB.

7.  Please be aware about the env file, you should configure the variables DATABASE_URL, DB_USERNAME, DB_PASSWORD, DB_HOSTNAME
    You have to be sure that the user exist and is authorized to log in the Postgres DB.

    In this point, is important to mention that for local setup, DB_HOSTNAME should be 'localhost'

8.  Execute npm run migrate:test. This will execute several tasks as drop entire DB, and then recreate all tables

9.  Execute npm run seed:test. This will put initial data in some tables.
    At this point, if all goes well, you should have your BD with tables and some of them with data, but let's go
    a little deep..the migrations, this initial sequelize data comes from the folder sequelize you will see 2 folders:
    migrations: Folder contains each table will be created in DB, and all its changes 
    seedes: Initial necessary data for some tables.

# Run Tests
For testing we are using Jest, you can see documentation in [Jest](https://jestjs.io/).
With this library, you can make any kind of test you want and put them in different files to organize better.

There are 2 ways to execute tests in our project, located in root path in folder __tests__

1. Run all tests at the same time
2. Run each file.

## Run all test
You can run **npm run test** and all files will be execute by Jest but be aware because this script run several tasks:
1. Execute script dropTables.js (This script drop entire DB)
2. Execute migrations locates in folder sequelize
3. Execute seeds to fill some initial tables.

## Run each file
If you run this file, the proccess described previously is not executed, this means the tests in the file are executed and DB is not dropped.
You can do this executing the command npm run jest __tests__/controllers/***name of test file***

We recommend always to execute the first option to be sure that all runs well.
When you execute npm run test you should get in terminal all tests results. I hope all are in "passed" status

Note: We use [Faker](https://github.com/marak/Faker.js/), as library to mock data in our tests.

If so, congrats! you have installed the project well.

# Development Process
There are some things important to mention in code development like lint, migrations and seeders.

For lint code, we are using eslint and Prettier, is important you configure your IDE for lint code well in VS Code is just replicate some rules. Please aks these rules to someone in the Backend Team.

## Migrations
Is very important to mention that any change, no matter what small is it, should be done by migration.
See [Sequelize-migrations](https://sequelize.org/master/manual/migrations.html)

If you add a new field, or delete it o change the name, or create a new table..any DDL shoul be done with this.
To do that, in terminal you can execute the next command:

npx sequelize-cli migration:generate --name ***name of migration***

When you execute this, sequelize creates a new file in the folder sequelize/migrations with the next name format
yyyyMMddHHmmss-name. Inside this the file is created with basic code

```
'use strict';

module.exports = {
  up: (queryInterface, Sequelize) => {
    
  },
  
  down: (queryInterface, Sequelize) => {
    
  }
};

```

You can see 2 methods here: UP/DOWN

- Up: is executed by sequelize when you run migration. Here you can specify new column, new column etc.
- Down: is executed when migraton is undoing. For example if you create a new field in up, in down you should specify remove that column

### Undoing migrations
For undo migrations you can execute the next command

**npx sequelize-cli db:migrate:undo**

This will delete the last migration file created ever.

## Seeders
You use a seed when a base new table is created and is necessary fill it to use with another for example
If you have table Bank, with field typeBankId. Is necessary create the table  TypeBank and this table should be fill up with seed.
Another important case, is when table has not way to managed by app, for example in this case where user does not fill TypeBank in any step.

To create seed is necessary run the next command:

npx sequelize-cli seed:generate --name ***name of seed***

This will create a new file in the folder sequelize/seeders

This file seems like migration file, you can put here initial json to fill data or script with some logic.

```
'use strict';

module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.bulkInsert('Test', [
      {
        code: 'test',
        name: 'test',
        createdAt: new Date(),
        updatedAt: new Date()
      },
      
    ], {});
  },

  down: (queryInterface, Sequelize) => {
    return queryInterface.bulkDelete('Test', null, {});
  }
};
```

# Project Structure
When the project is downloaded you will see the next structure:

- __tests__: this folder store all unit test of project
- public: Some public data, files
- report-templates: This folder store all templates used by project to create word/pdf reports.
- scripts: Generally these scripts are JS code, and are executed outside the project by a cron job or something like that.
- sequelize: This folder store migrations and seeders created by sequelize.
- src: This folder store entire logic app. In root you can find app.ts and bootstrap.ts
- src/controllers: This folder contains endpoints and logic, we have folder by module
- src/hooks: This folder store the logic applied in hook methods for some models. 
  ***Hooks (also known as lifecycle events), are functions which are called before and after calls in sequelize are executed. For example, if you want to always set a value on a model before saving it, you can add a beforeUpdate hook.***
  See [Hooks](https://sequelize.org/master/manual/hooks.html)
- src/integrations: This folder store logic to integrate third party libraries to the project, in order to be available to entire backend.
- src/interfaces: This folder store all interfaces to get types in codes. The idea is to have types a lot as possible and avoid use of    type ANY for variables or methods. All should return a known type.
- src/lib: This folder store some logic for third party libraries and its configuration.
- src/models: All models used in app. Each model correspond to a table in DB.
- src/policies: This folder store some policies and logic applied to enpoints in decorator way
- src/services: Logic available for any method, service, endpoint that need it.
- src/utils: Some methods used for entire application from any module, for example upload file, convert files etc

# Error Format
In application, we have a specific way to manage errors look the next object. You will see some categories to apply to each error

```
[
    {
        "code": "BJ400",
        "message": "Missing params",
        "status": 400
    },
    {
        "code": "NX404",
        "message": "Non-existent instance",
        "status": 404
    },
    {
        "code": "WD500",
        "message": "Wrong incoming data",
        "status": 400
    },
    {
        "code": "MS501",
        "message": "The instance cannot be saved",
        "status": 500
    },
    {
        "code": "MD502",
        "message": "The instance cannot be deleted",
        "status": 500
    },
    {
        "code": "MRG401",
        "message": "Your mortgages are higher than the amount we can lend you.", 
        "status": 401
    },
    {
        "code": "INT503",
        "message": "Internal error",
        "status": 500
    },
    {
        "code": "IC505",
        "message": "Insufficient income", 
        "status": 500
    },
    {
        "code": "AST505",
        "message": "Insufficient assets", 
        "status": 500
    },
    {
        "code": "DCP506",
        "message": "Pending signing process", 
        "status": 403
    },
    {
        "code": "DCS507",
        "message": "Document already signed", 
        "status": 403
    },
    {
        "code": "NOA441",
        "message": "Action not authorized",
        "status": 403
    },
    {
        "code": "AUT403",
        "message": "Invalid authentication.", 
        "status": 401
    },
    {
        "code": "TK408",
        "message": "Invalid token.", 
        "status": 401
    },
    {
        "code": "LE002",
        "message": "Account not verified.", 
        "status": 401
    },
    {
        "code": "UPTO441",
        "message": "Unable to change your rates", 
        "status": 401
    },
    {
        "code": "OB500",
        "message": "OB does not provide products", 
        "status": 404
    },
    {
        "code": "NX405",
        "message": "Existent instance", 
        "status": 412
    }
]

```
# Scripts

In the previous section, you see there is a folder where some specific scripts has been done for specific tasks. This is a short description about them

- dropTables: Used for script ***npm run test*** this will drop entire DB
- exec: This is used for execute webhook methods in a different threat of Node. Mainly encompass module uses this.
- migrateParametersToLowDB: This is important because a json file is created and contains all data from table Parameter. With this file   backend avoids consume DB in a lot of tasks where parameter data is consumed.
- send-encompass-encomplete-letter: This script is used to send to a client, a formal letter when the loan is very close to expire and is in status 'admin-overview'.
- send-encompass-encomplete-loan: This script takes all loans no passed by Pass to Review yet, and then execute a method with Encompass   module to assign id, and code, if fail a email is sent.
- send-mqs: This script is used to test Active MQ sending messages
- get-mqs: This script is used to test Active MQ receiving messages

# Queues
For some taks we are using Active MQ platform, to send and receive messages in Encompass module. Each update in Loan send a update to Encompass with MQ, if fail is resent again up to 5 times. All this fails are shown in slack.

# Postman JSON
We have a compilation of all endpoints with all possible request to import in Postman. Please contact to any member of Backend Team

# API Rest Documentation
We are using swagger for document each endpoint.

# Known Issues
There are some know issues and some possible solutions.

### Node.js version 14.xxxx 
If you install Node v14, sequelize will not work in a normal way. Migrations, seedes, connections nothing will work.
To fix that, is necessary change the version of pg library from 7.7.1 to 8

You can check this [Sequelize error](https://github.com/sequelize/sequelize/issues/12158)

Check this about version documentation [pg v8](https://node-postgres.com/announcements#2020-02-25)

### Stripe endpoint fail
The test of stripe, or call from Postman does not works. After check console and be sure that there is not error raised from backend
Sometimes, when you install with npm i, some errors are hidden by a lot of information shown on console, and you will not see the error maybe

The library related is html-pdf.

- Just execute npm uninstall html-pdf and then npm i html-pdf.





