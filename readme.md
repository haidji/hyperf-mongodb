# hyperf mongodb pool

```
composer require haidji/hyperf-mongodb
```

## config 
Create a file mongodb.php in the /config/autoload directory and add the following content
```php
return [
    'default' => [
             'username' => env('MONGODB_USERNAME', ''),
             'password' => env('MONGODB_PASSWORD', ''),
             'host' => env('MONGODB_HOST', '127.0.0.1'),
             'port' => env('MONGODB_PORT', 27017),
             'db' => env('MONGODB_DB', 'test'),
             'authMechanism' => 'SCRAM-SHA-256',
             //Set the replication set, if not set
             //'replica' => 'rs0',
            'pool' => [
                'min_connections' => 3,
                'max_connections' => 1000,
                'connect_timeout' => 10.0,
                'wait_timeout' => 3.0,
                'heartbeat' => -1,
                'max_idle_time' => (float) env('MONGODB_MAX_IDLE_TIME', 60),
            ],
    ],
];
```


# Use Cases

Use annotations to automatically load 
**\Hyperf\Mongodb\MongoDb** 
```php
/**
 * @Inject()
 * @var MongoDb
*/
 protected $mongoDbClient;
```

#### **tips:** 
The value of the query is to strictly distinguish the type, string, int type

### Add

Single add
```php
$insert = [
            'account' => '',
            'password' => ''
];
$this->$mongoDbClient->insert('fans',$insert);
```

Batch add
```php
$insert = [
            [
                'account' => '',
                'password' => ''
            ],
            [
                'account' => '',
                'password' => ''
            ]
];
$this->$mongoDbClient->insertAll('fans',$insert);
```

### Inquire

```php
$where = ['account'=>'1112313423'];
$result = $this->$mongoDbClient->fetchAll('fans', $where);
```

### Paging query
```php
$list = $this->$mongoDbClient->fetchPagination('article', 10, 0, ['author' => $author]);
```

### Renew
```php
$where = ['account'=>'1112313423'];
$updateData = [];

$this->$mongoDbClient->updateColumn('fans', $where,$updateData); // // Only update the fields that appear in $newObject in the column information of the row whose data satisfies $where
$this->$mongoDbClient->updateRow('fans',$where,$updateData);// update the information of the row whose data satisfies $where into $newObject
```
### Delete

```php
$where = ['account'=>'1112313423'];
$all = true; // false only deletes one match, true deletes multiple
$this->$mongoDbClient->delete('fans',$where,$all);
```

### count统计

```php
$filter = ['isGroup' => "0", 'wechat' => '15584044700'];
$count = $this->$mongoDbClient->count('fans', $filter);
```



### Command, execute more complex mongo commands

**sql** vs **mongodb**

|   SQL  | MongoDb |
| --- | --- |
|   WHERE  |  $match (and, or, and logical judgment can be used in match, but it seems that where cannot be used)  |
|   GROUP BY  | $group  |
|   HAVING  |  $match |
|   SELECT  |  $project  |
|   ORDER BY  |  $sort |
|   LIMIT  |  $limit |
|   SUM()  |  $sum |
|   COUNT()  |  $sum |

```php

$pipeline= [
            [
                '$match' => $where
            ], [
                '$group' => [
                    '_id' => [],
                    'groupCount' => [
                        '$sum' => '$groupCount'
                    ]
                ]
            ], [
                '$project' => [
                    'groupCount' => '$groupCount',
                    '_id' => 0
                ]
            ]
];

$count = $this->$mongoDbClient->command('fans', $pipeline);
```
