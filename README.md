# MongoDB-Copy-indexes
# Copy indexes from database A to database B

  - Please connect to your server and database A.
 ```sh
$ mongo somewhere.mlab.com:10011 -u username -p password
$ use A;
```
  - Run the following script to obtain list of commands to create indexes.
   ```sh
db.getCollectionNames().forEach(function(col) {
    var indexes = db[col].getIndexes();
    indexes.forEach(function (c) {
        var fields = '', result = '', options = {};
        for (var i in c) {
            if (i == 'key') {
                fields = c[i];
            } else if (i == 'name' && c[i] == '_id_') {
                return;
            } else if (i != 'name' && i != 'v' && i != 'ns') {
                options[i] = c[i];
            }
        }
        var fields = JSON.stringify(fields);
        var options = JSON.stringify(options);
        if (options == '{}') {
            result = "db." + col + ".createIndex(" + fields + "); ";
        } else {
            result = "db." + col + ".createIndex(" + fields + ", " + options + "); ";
        }
        result = result
            .replace(/{"floatApprox":-1,"top":-1,"bottom":-1}/ig, '-1')
            .replace(/{"floatApprox":(-?\d+)}/ig, '$1')
            .replace(/\{"\$numberLong":"(-?\d+)"\}/ig, '$1');
        print(result);
    });
});
```

  - Run the following script to obtain list of commands to create indexes.
 ```sh
db.progressdatas.createIndex({"Item1":1,"template":1}, {"background":true}); 
db.progressdatas.createIndex({"Item2":1,"template":1}, {"background":true});
```
  - Now connect to database B
   ```sh
$ mongo somewhere.mlab.com:10011 -u username -p password
$ use A;
```



 **IN CASE YOU HAVE A DIFFERENT COLLECTION NAME. PLEASE REPLACE DB.COLLECTION1 TO DB.COLLECTION2**


  - Paste list of indexes you obtained in step 2.
 ```sh
db.progressdatas.createIndex({"item":1,"template":1}, {"background":true}); 
db.progressdatas.createIndex({"item2":1,"template":1}, {"background":true}); 
db.progressdatas.createIndex({"template ":1}, {"background":true}); 
```
** In order to check status of index creation use this command **
```sh 
db.currentOp().inprog.forEach(function(op){ if(op.msg) print(op.msg) })
```
Output will be like 
```sh
Index Build (background) Index Build (background): 2391615/61466000 3%
Index Build (background) Index Build (background): 3149330/61466000 5%
```
  



