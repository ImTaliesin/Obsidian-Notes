npm create t3-app@latest 
in package.json add --turbo to the dev command
Set up vercel postgres DB

For drizzle:
in your index file: 
```
import { drizzle } from 'drizzle-orm/vercel-postgres';

import { sql } from "@vercel/postgres";

  

import * as schema from './schema';

  

// Use this object to send drizzle queries to your DB

export const db = drizzle(sql, {schema});
```

Default Table for schema
```
// Example model schema from the Drizzle docs

// https://orm.drizzle.team/docs/sql-schema-declaration

  

import { sql } from "drizzle-orm";

import {

  index,

  pgTableCreator,

  serial,

  timestamp,

  varchar,

} from "drizzle-orm/pg-core";

import { url } from "inspector";

import { use } from "react";

  

/**

 * This is an example of how to use the multi-project schema feature of Drizzle ORM. Use the same

 * database instance for multiple projects.

 *

 * @see https://orm.drizzle.team/docs/goodies#multi-project-schema

 */

export const createTable = pgTableCreator((name) => `modernreact_${name}`);

  

export const images = createTable(

  "image",

  {

    id: serial("id").primaryKey(),

    name: varchar("name", { length: 256 }).notNull(),

    url: varchar("url", { length: 1024 }).notNull(),

  

    userId: varchar("userId", { length: 256 }).notNull(),

  

    createdAt: timestamp("created_at", { withTimezone: true })

      .default(sql`CURRENT_TIMESTAMP`)

      .notNull(),

    updatedAt: timestamp("updatedAt", { withTimezone: true }),

  },

  (example) => ({

    //This is an index. Which lets you  query the database for all images with a given name.

    nameIndex: index("name_idx").on(example.name),

  }),

);
```