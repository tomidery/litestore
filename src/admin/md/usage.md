## Usage

### Command Line Syntax

[litestore](class:kwd) **[** _command_ **]** **[** _option1_, _option2_, ... **]**

#### Commands

* **run** &mdash; Start LiteStore server (default if no command specified).
* **delete** &mdash; Delete a previously-imported specified directory (requires -d).
* **execute** &mdash; Execute an operation on data stored in the datastore (requires -o, -u, and in certain cases -f or -b and -t).
* **import** &mdash; Import the specified directory into the datastore (requires -d).
* **export** &mdash; Export the previously-imported specified directory to the current directory (requires -d).
* **optimize** &mdash; Optimize search indexes.
* **vacuum** &mdash; Vacuum datastore.

#### Options

* **-a**, **-\-address** &mdash; Specify server address (default: 127.0.0.1).
* **-\-auth** &mdash; Specify an authorization configuration file.
* **-b**, **-\-body** &mdash; Specify a string containing input data for an operation to be executed.
* **-c**, **-\-config** &mdash; Specify a configuration file.
* **-d**, **-\-directory** &mdash; Specify a directory to serve, import, export, delete, or mount.
* **-f**, **-\-file** &mdash; Specify a file containing input data for an operation to be executed.
* **-h**, **-\-help** &mdash; Display program usage.
* **-l**, **-\-log** &mdash; Specify the log level: debug, info, warn, error, none (default: info)
* **-m**, **-\-mount** &mdash; Mirror database changes to the specified directory on the filesystem.
* **-o**, **-\-operation** &mdash; Specify an operation to execute via the execute command: get, put, delete, patch, post, head, options.
* **-p**, **-\-port** &mdash;Specify server port number (default: 9500).
* **-r**, **-\-readonly** &mdash; Allow only data retrieval operations.
* **-s**, **-\-store** &mdash; Specify a datastore file (default: data.db)
* **-\-system**  &mdash; Set the system flag for import, export, and delete operations
* **-\-import-tags**  &mdash; During import read tags from '_tags' file and apply them to imported documents from the same directory.
* **-\-not-searchable**  &mdash; During import exclude content of files and folders starting with '_' from full text search.
* **-t**, **-\-type** &mdash; Specify a content type for the body an operation to be executed via the execute command.
* **-u**, **-\-uri** &mdash; Specify an uri to execute an operation through the execute command.
* **-v**, **-\-version** &mdash; Display the program version.
* **-w**, **-\-middleware** &mdash; Specify a path to a folder containing middleware definitions

### Examples

#### Starting the HTTP Server

* with default settings:
  
  [litestore](class:cmd)

* loading configuration from a configuration file called **config.json**:

  [litestore -c:config.json](class:cmd)
  
* loading middleware definition files stored in a directory called **myMiddleware**:

  [litestore -w:myMiddleware](class:cmd)

* with custom port (**9700**) and address (**0.0.0.0**):
 
  [litestore -p:9700 -a:0.0.0.0](class:cmd)

* in read-only mode with logging level set to **debug**:

  [litestore -r -l:debug](class:cmd)
  
* serving the contents of a directory called **admin**:

  [litestore -d:admin](class:cmd)

* mouting a directory called **admin** (changes will be mirrored to filesystem, directory contents will be served):

  [litestore -d:admin -m](class:cmd)

#### Importing a directory

Import a directory called **admin**:

[litestore import -d:admin](class:cmd)

#### Importing system documents from a directory

Import all documents stored in a directory called **system** as system documents:

[litestore import -d:system -\-system](class:cmd)

#### Importing a directory and tagging files

Import all documents stored in a directory called **media** (including subdirectories):

```
+ media
  + cars
  | + _tags
  | + Lamborghini.jpg
  | + VW.jpg
  | ` BMW.jpg
  + planes
  | + _tags
  | + 767.jpg
  | + F-16.jpg
  | ` B-1.jpg
  ` trains
    + TGV.jpg
    ` Eurostar.jpg
```   

[litestore import -d:media -\-import-tags](class:cmd)

Every **_tags** file contains a list of tags, one per line, which are applied to all imported documents from the same directory. In the example above all cars and planes images will be tagged on import. The trains images, not as there is not **_tags** file in the **trains** directory.

The individual **_tags** files are also imported. When the **\-\-import\-tags** option is not set the **_tags** files are ignored and not imported.

#### Excluding files from full text search

Import all documents stored in a directory called **media** (including subdirectories):

```
+ media
  + _css
  | + style1.css
  | ` style2.css
  + cars
  | + _index.html
  | + Lamborghini.md
  | + VW.md
  | ` BMW.md
  + planes
  | + _index.html
  | + 767.md
  | + F-16.md
  | ` B-1.md
  ` trains
    + _index.html
    * _script.js
    + TGV.md
    ` Eurostar.md
```   

[litestore import -d:media -\-not-searchable](class:cmd)

All documents are imported but the files starting which name starts with **underscore** and files inside a folder which name starts with **underscore** are excluded from full text serach. The idea is that these files have special meaning for the application:
* they should be accessible via regular URLs (unlike **system** files)
* but they content should not be searchable.

#### Exporting a directory

Export all documents tagged with **$dir:admin** to a local directory called **admin**:

[litestore export -d:admin](class:cmd)

#### Deleting documents within a directory

Delete all documents tagged with **$dir:admin**:

[litestore delete -d:admin](class:cmd)

#### Performing maintenance operations

* vacuum SQlite database:

  [litestore vacuum](class:cmd)

* optimize search index:

  [litestore optimize](class:cmd)

#### Executing operations

* Retrieve all documents tagged with `$subtype:json`:

  [litestore execute -o:get -u:docs?tags=$subtype:json](class:cmd)

* Add a new document from a JSON file:

  [litestore execute -o:put -u:docs/test-doc -f:test.json -t:application/json](class:cmd)