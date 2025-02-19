---
sitemap:
  priority: 0.7
---

# Authentication

You'd need one or more hostnames and [API keys](./api-keys.md) to integrate with Typesense.

If you're self-hosting Typesense, the hostnames are the IP addresses or DNS names of each of your Typesense nodes. 
If you're using Typesense Cloud, we generate unique hostnames for each of your nodes and show them on the dashboard for you to use. 

<Tabs :tabs="['JavaScript','PHP','Python','Ruby','Dart','Shell']">
  <template v-slot:JavaScript>

```js
/*
 *  Our JavaScript client library works on both the server and the browser.
 *  When using the library on the browser, please be sure to use an
 *  API Key that only has search permissions rather than the master API key since the latter
 *  has write access to Typesense and you don't want to expose that.
 */

const Typesense = require('typesense')

let client = new Typesense.Client({
  'nodes': [{
    'host': 'localhost', // For Typesense Cloud use xxx.a1.typesense.net
    'port': '8108',      // For Typesense Cloud use 443
    'protocol': 'http'   // For Typesense Cloud use https
  }],
  'apiKey': '<API_KEY>',
  'connectionTimeoutSeconds': 2
})

// Typesense.Client() has methods for all API operations.
// If you only intend to search through documents (for eg: in the browser),
//    you can also use Typesense.SearchClient().
// This can also help reduce your bundle size by only including the classes you need:

import { SearchClient as TypesenseSearchClient } from "typesense";
let client = new TypesenseSearchClient({
  'nodes': [{
    'host': 'localhost', // For Typesense Cloud use xxx.a1.typesense.net
    'port': '8108',      // For Typesense Cloud use 443
    'protocol': 'http'   // For Typesense Cloud use https
  }],
  'apiKey': '<API_KEY>',
  'connectionTimeoutSeconds': 2
})
```

  </template>

  <template v-slot:PHP>

```php
use Typesense\Client;

$client = new Client(
  [
    'api_key'         => 'abcd',
    'nodes'           => [
      [
        'host'     => 'localhost',  // For Typesense Cloud use xxx.a1.typesense.net
        'port'     => '8108',       // For Typesense Cloud use 443
        'protocol' => 'http',       // For Typesense Cloud use https
      ],
    ],
    'connection_timeout_seconds' => 2,
  ]
);
```

  </template>
  <template v-slot:Python>

```py
import typesense

client = typesense.Client({
  'nodes': [{
    'host': 'localhost',  # For Typesense Cloud use xxx.a1.typesense.net
    'port': '8108',       # For Typesense Cloud use 443
    'protocol': 'http'    # For Typesense Cloud use https
  }],
  'api_key': '<API_KEY>',
  'connection_timeout_seconds': 2
})
```

  </template>
  <template v-slot:Ruby>

```rb
require 'typesense'

client = Typesense::Client.new(
  nodes: [{
    host:     'localhost',   # For Typesense Cloud use xxx.a1.typesense.net
    port:     8108,          # For Typesense Cloud use 443
    protocol: 'http'         # For Typesense Cloud use https
  }],
  api_key:  '<API_KEY>',
  connection_timeout_seconds: 2
)
```

  </template>
  <template v-slot:Dart>

```dart
import 'dart:io';
import 'package:typesense/typesense.dart';

final host = InternetAddress.loopbackIPv4.address;
final config = Configuration(
    nodes: {
      Node(
        host: host,        // For Typesense Cloud use xxx.a1.typesense.net
        port: 8108,        // For Typesense Cloud use 443
        protocol: 'http',  // For Typesense Cloud use https
      ),
    },
    apiKey: '<API_KEY>',
    connectionTimeout: Duration(seconds: 2),
  );
```

  </template>
  <template v-slot:Shell>

```bash
export TYPESENSE_API_KEY='<API_KEY>'
export TYPESENSE_HOST='http://localhost:8108'

# For Typesense Cloud use:
# export TYPESENSE_HOST='https://xxx.a1.typesense.net'

# a) Passing API key via header

curl -H "X-TYPESENSE-API-KEY: ${TYPESENSE_API_KEY}" \
"http://localhost:8108/collections/companies/documents/search\
?q=stark&query_by=company_name&filter_by=num_employees:>100\
&sort_by=num_employees:desc"

# b) Passing API key via GET parameter

curl "http://localhost:8108/collections/companies/documents/search\
?q=stark&query_by=company_name&filter_by=num_employees:>100\
&sort_by=num_employees:desc&x-typesense-api-key=${TYPESENSE_API_KEY}"
```

  </template>
</Tabs>

## Search Delivery Network

On Typesense Cloud, you can turn ON a setting called `Search Delivery Network` and choose a set of geographic regions that you'd like each of your Typesense nodes to be deployed to. The advantage of this setup is that you can then have the node that's closest to your users serve the search query, which reduces latency and keeps your search experience consistently fast for your users spread across geographic regions.

You'll be a given a special hostname called the Nearest Node hostname when you turn this setting on for your cluster. You'd then need to configure your clients to use this hostname:


<Tabs :tabs="['JavaScript','PHP','Python','Ruby', 'Dart','Shell']">
  <template v-slot:JavaScript>

```js
/*
 *  Our JavaScript client library works on both the server and the browser.
 *  When using the library on the browser, please be sure to use the
 *  search-only API Key rather than the master API key since the latter
 *  has write access to Typesense and you don't want to expose that.
 */

const Typesense = require('typesense')

let client = new Typesense.Client({
  'nearestNode': { 'host': 'xxx.a1.typesense.net', 'port': '443', 'protocol': 'https' }, // This is the special Nearest Node hostname that you'll see in the Typesense Cloud dashboard if you turn on Search Delivery Network
  'nodes': [
    { 'host': 'xxx-1.a1.typesense.net', 'port': '443', 'protocol': 'https' },
    { 'host': 'xxx-2.a1.typesense.net', 'port': '443', 'protocol': 'https' },
    { 'host': 'xxx-3.a1.typesense.net', 'port': '443', 'protocol': 'https' },
  ],
  'apiKey': '<API_KEY>',
  'connectionTimeoutSeconds': 2
})
```

  </template>

  <template v-slot:PHP>

```php
use Typesense\Client;

$client = new Client(
  [
    'nearest_node' =>  ['host' => 'xxx.a1.typesense.net', 'port' => '443', 'protocol' => 'https'], // This is the special Nearest Node hostname that you'll see in the Typesense Cloud dashboard if you turn on Search Delivery Network
    'nodes' => [ 
      ['host' => 'xxx-1.a1.typesense.net', 'port' => '443', 'protocol' => 'https'],
      ['host' => 'xxx-2.a1.typesense.net', 'port' => '443', 'protocol' => 'https'],
      ['host' => 'xxx-3.a1.typesense.net', 'port' => '443', 'protocol' => 'https'],
    ],
    'api_key'         => '<API_KEY>',
    'connection_timeout_seconds' => 2,
  ]
);
```

  </template>
  <template v-slot:Python>

```py
import typesense

client = typesense.Client({
  'nearest_node': {'host': 'xxx.a1.typesense.net', 'port': '443', 'protocol': 'https'}, # This is the special Nearest Node hostname that you'll see in the Typesense Cloud dashboard if you turn on Search Delivery Network
  'nodes': [
    {'host': 'xxx-1.a1.typesense.net', 'port': '443', 'protocol': 'https'},
    {'host': 'xxx-2.a1.typesense.net', 'port': '443', 'protocol': 'https'},
    {'host': 'xxx-3.a1.typesense.net', 'port': '443', 'protocol': 'https'}
  ],
  'api_key': '<API_KEY>',
  'connection_timeout_seconds': 2
})
```

  </template>
  <template v-slot:Ruby>

```rb
require 'typesense'

client = Typesense::Client.new(
  nearest_node: { host: 'xxx.a1.typesense.net', port: 443, protocol: 'https' }, # This is the special Nearest Node hostname that you'll see in the Typesense Cloud dashboard if you turn on Search Delivery Network
  nodes: [
    { host: 'xxx-1.a1.typesense.net', port: 443, protocol: 'https' },
    { host: 'xxx-2.a1.typesense.net', port: 443, protocol: 'https' },
    { host: 'xxx-3.a1.typesense.net', port: 443, protocol: 'https' },
  ],
  api_key:  '<API_KEY>',
  connection_timeout_seconds: 2
)
```

  </template>
  <template v-slot:Dart>

```dart
import 'package:typesense/typesense.dart';

final config = Configuration(
    // This is the special Nearest Node hostname that you'll see in the
    // Typesense Cloud dashboard if you turn on Search Delivery Network.
    nearestNode: Node(
      host: 'xxx.a1.typesense.net',
      port: 443,
      protocol: 'https',
    ),
    nodes: {
      Node(
        host: 'xxx-1.a1.typesense.net',
        port: 443,
        protocol: 'https',
      ),
      Node(
        host: 'xxx-2.a1.typesense.net',
        port: 443,
        protocol: 'https',
      ),
      Node(
        host: 'xxx-3.a1.typesense.net',
        port: 443,
        protocol: 'https',
      ),
    },
    apiKey: '<API_KEY>',
    connectionTimeout: Duration(seconds: 2),
  );
```

  </template>
  <template v-slot:Shell>

```bash
export TYPESENSE_API_KEY='<API_KEY>'
export TYPESENSE_HOST='https://xxx.a1.typesense.net'
```

  </template>
</Tabs>
