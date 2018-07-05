# Http Client API (cf.infrastructure.http_aio)
 
Provides implementation for HTTP communication to interface with REST Api's and option to add OAuth2 bearer tokens authorization header, HTTP exception handling policy with retry logic and tracing information.
 
### Installing
 
TBD - Artifactory info.

 
## Usage
### Basic usage.
```python
from cf.infrastructure.http_aio import HTTPAio
import asyncio
...

http_client = HTTPAio.create()
loop = asyncio.get_event_loop()
response = loop.run_until_complete(http_client.get('http://localhost/endpoint', timeout=0.1))
print(response.content)

```
### With async pool.
```python
from cf.infrastructure.http_aio import HTTPAio
...

async def main():
    http_pool = HTTPAio.create_pool(10)
    async with http_pool.item() as client:
        response = await client.get('http://localhost/endpoint', timeout=0.1)
        print(response.content)

```

### Exception handling with retry policy.
```python
from cf.infrastructure.http_aio import HTTPAio, HTTPAioException
from cf.loggingutils import get_logger
...

LOGGER = get_logger(__name__)

async def main():
    http_pool = HTTPAio.create_pool(10)
    async with http_pool.item() as client:
        try:
            response = await client.get('http://localhost/endpoint', timeout=0.1, 
            retries=2, failure_statuses=[500, 503, 400, 401])
            print(response.content)
        except HTTPAioException as e:
            LOGGER.error(f"HTTP error: {e}")
            raise

```

### Adding trace information to request.
```python
from cf.infrastructure.http_aio import HTTPAio
from cf.loggingutils import get_logger
from cf.infrastructure.tracer import TraceInfo
...

LOGGER = get_logger(__name__)

async def main():
    http_pool = HTTPAio.create_pool(10)
    async with http_pool.item() as client:
        response = await client.get('http://localhost/endpoint', timeout=0.1, trace=TraceInfo.create_new())
        print(response.content)

```

### Adding authentication to requests.
```python
from cf.infrastructure.http_aio import HTTPAio
from cf.loggingutils import get_logger
from cf.infrastructure.tracer import TraceInfo
...

LOGGER = get_logger(__name__)

from cf.infrastructure.http_aio import HTTPAio
from cf.infrastructure.tracer import TraceInfo
import asyncio


async def main():
    http_pool = OAuthHTTPAio.create_pool(10, client_id='client_id',client_secret='client_secret',authorize_url='http://localhost/auth')
    async with http_pool.item() as client:
        response = await client.get('http://localhost/endpoint', timeout=0.1, trace=TraceInfo.create_new())
        print(response.content)

```

## Deployment

TBD.

 
## Built With
 
* [aiohttp](https://aiohttp.readthedocs.io/en/stable/client.html) - The http client we are using underneath.
* cf.loggingutils
* cf.infrastructure.backoff
* cf.infrastructure.tracer
 
## Contributing
 
Please read [CONTRIBUTING.md](https://gist.github.com/PurpleBooth/b24679402957c63ec426) for details on our code of conduct, and the process for submitting pull requests to us.
 
## Versioning
 
TBD.
