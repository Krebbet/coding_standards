# Customizing Logging Behavior

Level: Repo
Constraint: Hard
Last Edit: July 17, 2025 5:28 PM
Project Types: GA, MVP, POC

Python's built-in `logging` module allows developers to route logs to multiple destinations—files, streams, remote APIs, or databases like MongoDB—using `basicConfig()` and custom log handlers.

To log to special destinations (like MongoDB, Slack, etc.), create a custom handler by subclassing `logging.Handler`.

There is also means to customize formatting and more!
Check out [https://docs.python.org/3/library/logging.html#logging.basicConfig](https://docs.python.org/3/library/logging.html#logging.basicConfig)  and [https://docs.python.org/3/howto/logging.html](https://docs.python.org/3/howto/logging.html) !

## ❌Example: Send Logs to MongoDB

In this case, compatibility with the standard python logging library is **not** maintained, and all code must be refactored to use the new logging system.

```python
import pymongo
from datetime import datetime
from other_module import do_stuff

# Set up logger
logger = logging.getLogger("__name__")

# Define a custom handler
class MongoLogger():
    def __init__(self, db_name="logs", collection="log_entries"):
        super().__init__()
        self.client = pymongo.MongoClient("mongodb://localhost:27017")
        self.collection = self.client[db_name][collection]

    def info(self, record):
        self.collection.insert_one(record)

if __name__ == "__main__":
	# Add MongoDB handler
	custom_mongo_logger = MongoLogger()
	
	# Example usage
	logger.info("This log message goes to MongoDB!")
	
	# Other logs won't go to mongo db without refactoring!
	do_stuff()
```

## ✅ Example: Send Logs to MongoDB

In this case, compatibility with the standard python logging library is maintained, and all existing logs can be routed to MongoDB without modification.

```python
import logging
import pymongo
from datetime import datetime

from other_module import do_stuff

# Set up logger
logger = logging.getLogger("__name__")

# Define a custom handler
class MongoHandler(logging.Handler):
    def __init__(self, db_name="logs", collection="log_entries"):
        super().__init__()
        self.client = pymongo.MongoClient("mongodb://localhost:27017")
        self.collection = self.client[db_name][collection]

    def emit(self, record):
        log_entry = self.format(record)
        self.collection.insert_one({
            "message": log_entry,
            "level": record.levelname,
            "timestamp": datetime.utcnow()
        })

if __name__ == "__main__":
	# Add MongoDB handler
	mongo_handler = MongoHandler()
	
	logging.basicConfig(
    level=logging.INFO,
    handlers=[mongo_handler]
	)
	
	# Example usage
	logger.info("This log message goes to MongoDB!")
	
	# Other logs will also go to mongodb!
	do_stuff()
```