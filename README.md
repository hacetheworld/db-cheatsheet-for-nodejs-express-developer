# 🧠 DB Cheatsheet for Node.js + Express Developers

If you are a **Node.js / Express** developer and confused about how to work with databases — this repository is for you! 🚀

Whether you’re a **full-stack** or **backend** engineer working with **MongoDB**, **PostgreSQL**, **MySQL**, or **MSSQL**,  
this guide will help you set up each database properly — without confusion.

### 📝 Before You Begin

A few quick notes before we dive in:
  - I’m assuming you’re already familiar with basic Node.js and Express syntax — this repo focuses mainly on how to work with databases.
  - Make sure the database you are trying to connect exist in db
  - The goal here is to guide you conceptually, not just hand you copy-paste code.
  - Use the examples to understand the structure and flow — then adapt them to your own project setup.

Think of this repo as a reference point to remove confusion around database setup, not a final production template.



---

## 🟢 MongoDB with Mongoose

```
npm install mongoose
```

### 1️⃣ Create a connection file — `db.js`

```
// db.js
import mongoose from "mongoose";

export const connection = mongoose.connect(process.env.MONGO_URL, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

connection
  .then(() => console.log("✅ MongoDB connected"))
  .catch(err => console.error("❌ MongoDB connection error:", err));
```

### 4️⃣ Connect in your entry file — app.js
You don’t need to do anything else — just importing ./db.js runs the connection code once.
All Mongoose models (mongoose.model(...)) will automatically use this same global connection internally.
```
import "./db.js"
```

### 2️⃣ Define a model — models/User.js

```
import mongoose from "mongoose";

const userSchema = new mongoose.Schema(
  {
    name: { type: String, required: true },
    email: { type: String, required: true, unique: true },
    age: Number,
  },
  { timestamps: true }
);

export const User = mongoose.model("User", userSchema);
```


### 3️⃣ Use it anywhere
```
import { User } from "../models/User.js";

const users = await User.find();
const newUser = await User.create({ name: "Ajay", email: "ajay@example.com" });
```

### 🧩 Why this works
In Mongoose, you don’t need to manually pass the connection — once you call mongoose.connect() anywhere in your app, Mongoose sets up a global connection, and all models automatically use that same connection internally.


## 🟣 PostgreSQL / MySQL / MSSQL with Sequelize

Now we’re working with SQL databases like PostgreSQL, MSSQL, or MySQL, so we need to make a few changes — but the overall concept stays the same. 

We create a connection, use it in the entry file for initialization, then define models using that connection,

and finally use those models inside our services or other files — and boom 💥, 

your code works with any database!


### 1️⃣ Install dependencies
You’ll notice that we never directly use pg, mysql2, or tedious in our code — but they’re still required.

That’s because Sequelize internally uses these database drivers based on the dialect you specify (e.g., "postgres", "mysql", "mssql").

So make sure you install the correct driver, or Sequelize will throw an error saying the module is missing.

```
# PostgreSQL
npm install sequelize pg

# MySQL
npm install sequelize mysql2

# MSSQL
npm install sequelize tedious
```


### 2️⃣ Setup — config/db.js
```
import { Sequelize } from "sequelize";

export const sequelize = new Sequelize(
  "dbname",   // database name (make sure it exists in db)
  "postgres",      // username (e.g., 'sa' for MSSQL)
  "1234",          // password
  {
    host: "localhost",
    dialect: "postgres", // change to 'mysql' or 'mssql'
    logging: false,
  }
);

export const connectDB = async () => {
  try {
    await sequelize.authenticate();
    console.log("✅ Database connected successfully");
  } catch (err) {
    console.error("❌ DB connection failed:", err.message);
  }
};
```

### 3️⃣ Define a model — models/User.js
```
import { DataTypes } from "sequelize";
import { sequelize } from "../config/db.js";

export const User = sequelize.define(
  "User",
  {
    id: {
      type: DataTypes.INTEGER,
      autoIncrement: true,
      primaryKey: true,
    },
    name: {
      type: DataTypes.STRING,
      allowNull: false,
    },
    email: {
      type: DataTypes.STRING,
      allowNull: false,
      unique: true,
      validate: { isEmail: true },
    },
    age: {
      type: DataTypes.INTEGER,
      defaultValue: 18,
    },
  },
  {
    tableName: "users",
    timestamps: true,
  }
);

```

### 4️⃣ Connect in your entry file — app.js

##### ⚠️ Important Note:
You must import all your models and db connection into the main file so that their code executes — this ensures Sequelize is aware of them before syncing the database.
also call the methods of the db connection if u wrote the code inside the function inside "db.js" but u dont have to do anything with models just import them and it will work 
```
import { connectDB, sequelize } from "./config/db.js";

//Just import to register models (no variable needed and u dont have to use them )
import "./models/User.js";

await connectDB();
await sequelize.sync({ alter: true }); // ✅ use only in development

```


### 5️⃣ Use it anywhere
```
import { User } from "../models/User.js";

// Get all users
const users = await User.findAll();

// Create a user
const newUser = await User.create({ name: "Ravi", email: "ravi@example.com" });

// Update user
await User.update({ age: 25 }, { where: { id: 1 } });

```



the whole idea behind this repository — to make working with databases ridiculously easy for you guys, so you can stop fearing the DB part and just focus on your actual work, knowing your database will always work. 🚀




Github    : <a href="github.com@hacetheworld">github.com@hacetheworld</a>

Linkedin  : <a href="https://www.linkedin.com/in/ajay-meena1/">Linkedin/ajay-meena</a>





