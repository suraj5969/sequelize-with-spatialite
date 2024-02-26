# Sequelize with Spatialite

A copy of [Sequelize](https://github.com/sequelize/sequelize]) ORM with support for [Spatialite](https://www.gaia-gis.it/fossil/libspatialite/index) extension in SQLite.

The original Sequelize ORM does not support Spatialite extension in SQLite. This package adds support for Spatialite extension in SQLite with Sequelize ORM.

## Installation

```bash
npm install sequelize-with-spatialite
```

For any documentation on Sequelize, refer to the original [Sequelize documentation](https://sequelize.org/docs/v6/) remember to read v6 docs, this package is based on that. The code of this repo is based on the commit [505467b](https://github.com/sequelize/sequelize/commit/505467bd7fb66a0fe3298038307390c597500689).

You can read Spatialite documentation [here](https://www.gaia-gis.it/gaia-sins/spatialite-cookbook-5/index.html).

I recommend first reading my blog post on this [Integrating Spatialite with Sequelize](https://surajgupta.hashnode.dev/integrating-spatialite-with-sequelize).

## Usage

To use Spatialite in any SQLite database, you first need to initialize spatialite in the database. Run this query to initialize spatialite in the database. This query is only needed once for a database.

```javascript
import { DataTypes, Sequelize } from "sequelize-with-spatialite";

const sequelize = new Sequelize({
    dialect: "sqlite",
    storage: "./db.sqlite", // path to your database file
    logging: false,
});

await sequelize.query("SELECT InitSpatialMetaData();");
```

That's it, Now you can use Sequelize for Post GIS data in SQLite with all the same syntax and notations as postgres.

```javascript
const User = sequelize.define('User', {
    id: {
        type: Sequelize.UUID,
        primaryKey: true,
        defaultValue: Sequelize.UUIDV4 
    },
    name: {
        type: DataTypes.STRING,
        allowNull: false
    },
    location: {
        type: DataTypes.GEOMETRY('POINT'),
        allowNull: true
    },
    area: {
        type: DataTypes.GEOMETRY('POLYGON'),
        allowNull: true
    }
});
```
Insert data or Run migartions normally as you would do with Sequelize.

```javascript
import circleToPolygon from 'circle-to-polygon';

const coordinates = [73.78291978700082, 20.06382394128455]; // longitude, latitude
const point = {type: "Point", coordinates: coordinates};
const radius = 10000; // in meters
const polygon = circleToPolygon(coordinates, radius, 32); // 32 is the number of edges

await User.create({
    name: "John Doe",
    location: point,
    area: polygon
});
```

> **Note:** This package will not be actively maintained. I will ocassionally do bug fixes and updates, but I will not be adding new features or updating to new versions of Sequelize. If you want to use the latest features of Sequelize, you can use the original Sequelize package and use the `spatialite` package to use Spatialite extension in SQLite (which is messy, because you have to always use raw queries, hence I created this package.)

If you find any issues, feel free to open an issue or a pull request.