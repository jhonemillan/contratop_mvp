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
    Please visit the next web [node-gyp] (https://github.com/nodejs/node-gyp) and select proccess depending of your Operative System

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

# Sequelize
Create a new migration file `node_modules/.bin/sequelize migration:generate --name name`
Create models `node_modules/.bin/sequelize model:generate --name User --attributes firstName:string,lastName:string,email:string`


  https://github.com/sequelize/sequelize/issues/12158
