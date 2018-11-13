---
title: Working With Data Models
date: 2017-04-23 16:51:29
---

# Background

Hitchy has its own object document manager (ODM) called [odem](https://www.npmjs.com/package/hitchy-odem). It comes with its own concept for implementing data models for managing instances of either model. It's API is designed to be quite similar to key-value based databases such as LevelDB. Currently it includes an adapter for temporarily managing data in memory. A second adapter supports managing data in a folder of your file system. Further adapter will be created e.g. for interacting with LevelDB backends such as Hitchy's Raft consensus cluster called [scull](https://www.npmjs.com/package/scull).

# Installation

A separate package called [hitchy-plugin-odem-rest](https://www.npmjs.com/package/hitchy-plugin-odem-rest) provides a REST-like API for accessing Hitchy's ODM out of the box. By installing it as dependency the underlying [hitchy-odem](https://www.npmjs.com/package/hitchy-odem) gets installed implicitly as well.

1. Run this command using your CLI while residing in directory containing your base project:  
   `npm install hitchy-plugin-odem-rest`

2. Create new folder **api/model** inside your Hitchy project (which is the one in subfolder **server** in case of you've been starting off with a VueJS project before).

3. Put a file **person.js** into that folder containing this:
   ```javascript
   module.exports = {
      	attributes: {
      		salutation: {},
      		title: {},
      		firstName: {},
      		lastName: {},
      		company: {},
      		address: {},
      		zip: {},
      		locality: {},
      		phone: {},
      		mail: {},
      	},
   };
   ```
   This is declaring a data model named **person** consisting of a set of attributes each of type **string** implicitly. There are no constraints declared on any attribute which is available, too. But for the sake of demonstrating a minimum declaration of a model we stick with this example.
   
4. Restart Hitchy.


# Manually Control Data

The following examples demonstrate how to control data in your defined model using browser.

1. **List available instances:** Open your browser at URL http://localhost:3000/api/person. As you haven't created any model instances yet, the result is an empty list:  
   ![](/images/hitchy-odm-initial.png)

2. **Create new instance:** Switch to URL http://localhost:3000/api/person/create?lastName=Doe&firstName=John. You should see something like this, though the displayed UUID might be different for sure:    
   ![](/images/hitchy-odem-first-created.png)

3. Return to the previous URL. You might need to reload the page due to browser's caching. But eventually there is a record listed now.  
   ![](/images/hitchy-odm-list-created.png)

4. **Write single property of existing instance:** Copy the UUID listed in your output and replace `<uuid>` in URL [http://localhost:3000/api/person/write/&lt;uuid>?phone=555+12345](http://localhost:3000/api/person/write/&lt;uuid>?phone=555+12345) with that UUID. Query the resulting URL with your browser. The output should be equivalent to the one in step 6 before.

5. Return to the URL used in steps 1 and 3 again. This time there is the phone number you've added.  
   ![](/images/hitchy-odm-phone-added.png)

6. **Feed the list:** Create a second person using URL http://localhost:3000/api/person/create?lastName=Doe&firstName=Jane. (**Note the adjusted `firstName`!**) Return to the previous URL again:  
   ![](/images/hitchy-odm-added-record.png)

7. **Search the model:** Open URL http://localhost:3000/api/person/find/firstName/eq/Jane. This time you get a list consisting of the second instance created right before due to matching search criteria `firstName` `eq`uals `Jane`:  
   ![](/images/hitchy-odm-find.png)

8. **Limit the results:** Extend URL used to query model before or the URL used initially to list all records by parameters `offset` and/or `limit` skip some listed records or limit number of resulting records, e.g. 

   - http://localhost:3000/api/person?offset=1 or

   - http://localhost:3000/api/person/find/firstName/eq/Jane?limit=1.

See the full list of available URLs per model in [documentation of hitchy-plugin-odem-rest](https://www.npmjs.com/package/hitchy-plugin-odem-rest).


# Persisting Your Data

Until now all data was backed using a default adapter which happens to use computer's volatile memory thus loosing all data on restarting Hitchy. This is great for testing but it won't help to get become production-ready. You can fix this by configuring different default adapter to be used.

**hitchy-odem** includes `FileAdapter` storing all data in a folder of your file system. This adapter is anything but performant and definitely not suitable for large-scale applications. But it's sufficient to extend your next web application to a production-ready first prototype.

> Keep waiting for an adapter connecting with a LevelDB backend or similar.

1. Create folder **server/data** to contain your data.

2. Create file **server/config/database.js** and put this content:  
   ```javascript
   const Path = require( "path" );
   const { FileAdapter } = require( "hitchy-odem" );
   
   module.exports = {
   	database: {
   		default: new FileAdapter( {
   			dataSource: Path.resolve( __dirname, "../data" )
   		} ),
   	},
   };
   ```

This file is adjusting configuration option **config.database.default** which is read by **hitchy-odem** on selecting default adapter to use with every model created. On next restart Hitchy is going to create all data model instances in created folder.
