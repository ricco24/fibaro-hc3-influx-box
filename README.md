## Fibaro HC3 logs to influx import

### Requirements

- PHP >= 7.1
- Composer
- Installed InfluxDB

### Usage

#### Initialization
As example we use directory **/home/pi/fibaro** as root of our project.

```bash
# Create project directory
mkdir /home/pi/fibaro
cd /home/pi/fibaro

# Clone project skeleton to created directory
composer create-project kelemen/fibaro-hc3-influx-box .

# Edit configuration
mv config.php.temp config.php
vim config/config.php
```

#### Configuration
Base template for configuration is stored in <code>/config/config.php.temp</code>
```php
return [
    'influx' => [
        'host' => '',           // base InfluxDB host or IP
        'database' => '',       // logs will be saved into this database [database has to be already created in InfluxDB]
        'port' => 8086,
        'username' => '',       // InfluxDB username [with write privileges to selected database]
        'password' => '',       // InfluxDB password
        'ssl' => false,         // set true if InfluxDB is running via https
        'verifySSL' => false,   // verify SSL certificate [set to false if using self signed certificates]
        'timeout' => 5,         // call timeout in seconds [set to reasonable value to prevent infinite calls]
        'connectTimeout' => 3   // connect timeout in seconds [set to reasonable value to prevent infinite calls]
    ],
    'hc' => [
        'url' => '',            // base url of HC3
        'username' => '',       // login username
        'password' => '',       // login password
        'verifySSL' => false    // verify SSL certificate [set to false if using self signed certificates]
    ],
    'storage' => [
        'type' => 'file',       // Type of used storage [current only file is implemented]
        'directory' => __DIR__ . '/../storage' // absolute path to storage directory [write permissions needed]
    ],
    'commands' => [
        'consumption' => [
            'devices' => []     // devices which consumption we want to log (this devices has to support power/consumption logging - like wall plugs etc.)
        ]
    ]
];
```

#### Recommended cron setup
```bash
# Open crontab
crontab -e

# Add this lines and save
# Run every 10 minutes (history logs)
*/10 * * * * <PROJECT_DIR>/vendor/bin/fibaro log:consumption > /dev/null 2>&1
*/10 * * * * <PROJECT_DIR>/vendor/bin/fibaro log:events > /dev/null 2>&1

# Run every 1 minute (status updates from last call)
* * * * * <PROJECT_DIR>/vendor/bin/fibaro log:refreshStates > /dev/null 2>&1

# Run every 1 minute (actual status snapshot)
* * * * * <PROJECT_DIR>/vendor/bin/fibaro log:diagnostics > /dev/null 2>&1
* * * * * <PROJECT_DIR>/vendor/bin/fibaro log:weather > /dev/null 2>&1
```

### Commands
For detailed commands description visit [fibaro-hc3-influx-logs](https://github.com/ricco24/fibaro-hc3-influx)