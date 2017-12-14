
# rworker [![Travis-CI Build Status](https://travis-ci.org/lecardozo/rworker.svg?branch=master)](https://travis-ci.org/lecardozo/rworker)
Long running (and remote) tasks execution in R mediated by Redis.
## Motivation
The main motivation of this package was the need for executing long running R functions
triggered by [Celery](https://github.com/celery/celery) (asynchronous task queue package for Python).

![Data flow](https://raw.githubusercontent.com/lecardozo/rworker/master/img/rworker.png)
## Usage
##### Start consuming tasks from R
```R
library(rworker)
library(magrittr)

# Broker url
redis_url <- 'redis://localhost:6379'

# Instantiate Rworker object
consumer <- rworker(qname='celery', workers=2, queue=redis_url, backend=redis_url)

# Register tasks
(function(){
    Sys.sleep(5)
}) %>% consumer$task(name='long_running_task')

# Start consuming messages
consumer$consume()
```
The `rworker` function returns a `Rworker` object. This object is responsible for registering tasks, listening for messages coming from the message queue and triggering tasks execution on background processes

##### Send tasks from Python
```python
from celery import Celery

worker = Celery('app', broker="redis://localhost:6379/0", backend="redis://localhost:6379/0")
worker.send_task('long_running_task')
```
## Future decisions / TODO
- [x] ZeroMQ for master~worker communication
- [ ] Integration tests or mocking?
